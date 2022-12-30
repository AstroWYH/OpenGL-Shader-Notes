```cpp
int CalcIsland(cv::Mat &maskbase, cv::Mat &seg, cv::Mat &label_image, std::vector <struct BlockInfo> &blocks, int *max_light_num) {
    int MASK_THRESHOLD = 127;

    float nonZeroNum_ratio = 22.0f;
    float temp_lightnum_ratio = 22.0f;

    int status = 0;
    cv::threshold(maskbase, seg, MASK_THRESHOLD, 255, cv::THRESH_BINARY); // seg为二值化以后的灰度图，0-127为0, 128-255为255
    float mask_size = seg.rows * seg.cols * 1.0f;

    int nonZeroNum = cv::countNonZero(seg);
    LOG("non zero num %d", nonZeroNum);
    if (nonZeroNum * nonZeroNum_ratio < mask_size) {
        float ratio = nonZeroNum / mask_size;
        if (ratio < 0.005) {
            status = 4;
        } else {
            status = 5;
        }
        LOG("set skipFrameNum nonZeroNum(%d)/mask_size(%.0f): %.4f ",
            nonZeroNum, mask_size, ratio);
        memset(seg.data, 0, seg.rows * seg.cols);
        return status;
    }

    seg.convertTo(label_image, CV_32SC1); // label_image同样为二值化以后的灰度图，修改格式为CV_32SC1的目的是，可以突破255的上限，为label_count铺路

    int maxlightnum = 0;
    int label_count = 256; // start at 256 because 0, 255 are used already

    for (int y = 0; y < label_image.rows; y++) {
        int *row = (int *) label_image.ptr(y);
        for (int x = 0; x < label_image.cols; x++) {
            if (row[x] == 255) {
                cv::Rect rect;
                // 当前label_image的情况是，一个最大的岛屿(全是255，真正的mask)，多个小的岛屿（全是255，真正的下岛），其余地方都是水（全是0）
                // floodFill的目的是，将每个岛屿进行浸染，之前全是255的岛屿后续会变为，一个岛屿全是256，一个岛屿全是257，一个岛屿全是258...
                // floodFill设置倒数第2、3个参数都是0，则floodFill会在当前岛屿上，将和当前坐标(x, y)相同(即255)的位置改为label_count（new_val）
                int temp_lightnum = cv::floodFill(label_image, cv::Point(x, y), label_count, &rect,
                                                  0, 0, 4);
                if (temp_lightnum * temp_lightnum_ratio > mask_size) {
                    LOG("remove island label_count %d temp_lightnum(%d)/mask_size(%.0f) %.4f",
                        label_count, temp_lightnum, mask_size, temp_lightnum / mask_size);
                    maxlightnum = std::max(maxlightnum, temp_lightnum); // 最大岛屿就是真正的mask

                    struct BlockInfo newInfo;
                    newInfo.label = label_count; // 此后，256、257、258...作为了各个岛屿的标签
                    newInfo.rc = rect; // 未能找到这项参数的含义，此处代码也没用到
                    newInfo.lightnum = temp_lightnum; // 猜测为当前岛屿所占的像素数
                    blocks.push_back(newInfo);
                }
                label_count++;
            }
        }
    }
    *max_light_num = maxlightnum;
    return status;
}

int RemoveIslands(cv::Mat &seg) {
    cv::Mat label_image;
    std::vector <struct BlockInfo> blocks;
    int maxlightnum = 0;
    int status = CalcIsland(seg, seg, label_image, blocks, &maxlightnum);
    if (status != 0) {
        return status;
    }
    float blocks0_lightnum_ratio = 22.0f;

    float mask_size = seg.rows * seg.cols * 1.0f;
    float chose_toleft_ratio = 0.5;
    float head_nonZeroNum_ratio = 0.05;

    LOG("before remove island blocks %d maxlightnum %d", blocks.size(), maxlightnum);
    if (blocks.size() >= 1) { // 去掉较小的小岛
        LOG("remove island lightnum_ratio blocks[0].light_number(%d)/mask_size(%.f) %.2f",
                    blocks[0].lightnum, mask_size, blocks[0].lightnum / mask_size);
        auto itr = blocks.begin();
        while (itr != blocks.end()) {
            const struct BlockInfo &info = *itr;
            LOG("remove island info.light_number  %lu maxlightnum %d  x %d y %d width %d height %d",
                    info.lightnum, maxlightnum, info.rc.x, info.rc.y, info.rc.width, info.rc.height);
            if (info.lightnum < maxlightnum * 0.3) {
                // 和真正的mask相比，若小于其*0.3，则认为是小岛，否则可能是多个mask（或mask和head）
                itr = blocks.erase(itr);
            } else {
                itr++;
            }
        }
    }
    LOG("remove island blocks %d", blocks.size());
    if (blocks.size() == 0 || blocks.size() > 999 ||
        (blocks.size() == 1 && (blocks[0].lightnum * blocks0_lightnum_ratio < mask_size))) {
        // 异常情况处理
        memset(seg.data, 0, mask_size);
        if (blocks.size() > 999) {
            status = 5;
        } else {
            status = 4;
        }
    } else {
        bool has_head = true;
        if (blocks.size() == 1) {
            // 这个判断意味着，认为如果只有一个真正的mask，那上面的过程就已经完全将小岛去除了，这一个block一定是真正的mask
            int label = blocks[0].label;
            for (int y = 0;
                    y < seg.rows; y++) {
                uchar *ptrMask = seg.ptr(y);
                int *row_label = (int *) label_image.ptr(y);
                for (int x = 0; x < seg.cols; x++) {
                    if (row_label[x] == label) {
                        ptrMask[x] = 255;
                    } else {
                        ptrMask[x] = 0;
                    }
                }
            }
            LOG("curFrameHaveHuman light_number %ld", blocks[0].lightnum);
            LOG("curFrameHaveHuman rect %d  %d ", blocks[0].rc.width, blocks[0].rc.height);
        } else { // 当head count >=2 时才能进入这里 if(blocks.size() > 1)
            int label1 = blocks[0].label;
            int label2 = blocks[1].label;
            for (int y = 0; y < seg.rows; y++) {
                uchar *ptrMask = seg.ptr(y);
                int *row_label = (int *) label_image.ptr(y);
                for (int x = 0; x < seg.cols; x++) {
                    if (row_label[x] == label1 || row_label[x] == label2) {
                        ptrMask[x] = 255;
                    } else {
                        ptrMask[x] = 0;
                    }
                }
            }
            LOG("curFrameHaveHuman light_number %ld, rect %d  %d ", blocks[0].lightnum,
                        blocks[0].rc.width, blocks[0].rc.height);
        }
    }

    LOG("has human %d", status == 0);
    return status;
}
```

