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
    cv::absdiff(seg, history_seg, diff);
    for (int y = 0; y < seg.rows; ++y) {
        uchar *ptr_y_seg = seg.ptr(y);
        uchar *ptr_y_seg_his = history_seg.ptr(y);
        uchar *ptr_diff = diff.ptr(y);
        for (int x = 0; x < seg.cols; ++x) {
            uchar _diff = ptr_diff[x];
            uchar _seg_his = ptr_y_seg_his[x];
            uchar _seg = ptr_y_seg[x];
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

