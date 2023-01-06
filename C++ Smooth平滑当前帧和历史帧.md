## 两层for循环遍历图像[i, j]

```cpp
int SmoothByPreview(cv::Mat &seg, std::vector<unsigned char> &history_seg_image) {
    // seg为当前帧mask
    // history_seg_image为上一帧mask
    // diff为seg和history_seg_image的绝对值差异图

    int size = seg.rows * seg.cols;
    if (history_seg_image.size() != size) {
        history_seg_image.resize(size);
        memcpy(history_seg_image.data(), seg.data, size);
    }
    cv::Mat history_seg(seg.rows, seg.cols, CV_8UC1, history_seg_image.data());
    cv::Mat diff(seg.rows, seg.cols, CV_8UC1);
    cv::absdiff(seg, history_seg, diff); // cv::absdiff
    for (int y = 0; y < seg.rows; ++y) {
        uchar *ptr_y_seg = seg.ptr(y);
        uchar *ptr_y_seg_his = history_seg.ptr(y);
        uchar *ptr_diff = diff.ptr(y);
        for (int x = 0; x < seg.cols; ++x) {
            uchar _diff = ptr_diff[x];
            uchar _seg_his = ptr_y_seg_his[x];
            uchar _seg = ptr_y_seg[x];
            // 遍历gray图像的每个像素点，根据diff的值确定mix值（hist_ratio），将seg和his_seg进行混合，输出到seg上
            float hist_ratio = 1.0f;
            if (_diff < 60) {
                hist_ratio = 0.9f;
            } else if (_diff < 80) {
                hist_ratio = 0.85f;
            } else if (_diff < 100) {
                hist_ratio = 0.8f;
            } else if (_diff < 120) {
                hist_ratio = 0.3f;
            } else if (_diff < 140) {
                hist_ratio = 0.2f;
            } else {
                hist_ratio = 0.0f;
            }
            ptr_y_seg[x] = hist_ratio * _seg_his + (1 - hist_ratio) * _seg;
        }
    }
    memcpy(history_seg_image.data(), seg.data, size);
    return 0;
}
```

## 矩阵运算

```cpp
int SmoothByPreviewOpt(cv::Mat &seg, std::vector<unsigned char> &history_seg_image) {
    int size = seg.rows * seg.cols;
    if (history_seg_image.size() != size) {
        history_seg_image.resize(size);
        memcpy(history_seg_image.data(), seg.data, size);
    }
    cv::Mat history_seg(seg.rows, seg.cols, CV_8UC1, history_seg_image.data());
    cv::Mat diff(seg.rows, seg.cols, CV_8UC1);
    cv::absdiff(seg, history_seg, diff);

    Mat history_seg_float(seg.size(), CV_32FC1, Scalar(0.0));
    history_seg.convertTo(history_seg_float, CV_32FC1, 1.0, 0);

    Mat seg_float(seg.size(), CV_32FC1, Scalar(0.0));
    seg.convertTo(seg_float, CV_32FC1, 1.0, 0);

    Mat diff_geater_0 = 0 <= diff;
    Mat diff_less_60 = diff < 60;
    Mat mask_0_60(seg.size(), CV_8UC1, Scalar(0));
    Mat mask_nor_0_60(seg.size(), CV_32FC1, Scalar(0.0));

    mask_0_60 = diff_geater_0.mul(diff_less_60); // 0-60 diff为255*255=255，其余为0
    mask_0_60.convertTo(mask_nor_0_60, CV_32FC1, 1.0 / 255, 0); // 0-60 diff为1.0，其余为0

    Mat his_weight_mask_0_60 = mask_nor_0_60 * 0.6;
    Mat seg_weight_mask_0_60 = mask_nor_0_60 * (1 - 0.6);
    Mat mat_0_60 = his_weight_mask_0_60.mul(history_seg_float) + seg_weight_mask_0_60.mul(seg_float);

    Mat diff_geater_60 = 60 <= diff;
    Mat diff_less_255 = diff < 255;
    Mat mask_60_255(seg.size(), CV_8UC1, Scalar(0));
    Mat mask_nor_60_255(seg.size(), CV_32FC1, Scalar(0.0));

    mask_60_255 = diff_geater_60.mul(diff_less_255);
    mask_0_60.convertTo(mask_nor_60_255, CV_32FC1, 1.0 / 255, 0);

    Mat his_weight_mask_60_255 = mask_nor_60_255 * 0.1;
    Mat seg_weight_mask_60_255 = mask_nor_60_255 * (1 - 0.1);

    Mat mat_60_255 = his_weight_mask_60_255.mul(history_seg_float)
        + seg_weight_mask_60_255.mul(seg_float);

    seg_float = mat_0_60 + mat_60_255;
    seg_float.convertTo(seg, CV_8UC1, 1, 0);
    
    memcpy(history_seg_image.data(), seg.data, size);
    return 0;
}
```

![image-20230104205222109](https://hanbabang-1311741789.cos.ap-chengdu.myqcloud.com/Pics/image-20230104205222109.png)