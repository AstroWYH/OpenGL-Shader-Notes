```cpp
int Morph(cv::Mat &mask, cv::Mat &alpha) {
    int seg_width = alpha.cols;
    int seg_height = alpha.rows;
    const int band = 3;

    static cv::Mat element = cv::getStructuringElement(cv::MORPH_RECT,
                                                       cv::Size(2 * band + 1, 2 * band + 1),
                                                       cv::Point(band, band));
    cv::Mat dilated = alpha.clone();
    cv::dilate(dilated, dilated, element);

    cv::Mat eroded = alpha.clone();
    cv::erode(eroded, eroded, element);

    unsigned char *v0 = mask.data;  // before
    unsigned char *v1 = alpha.data; // after

    const unsigned char *vd = dilated.data;
    const unsigned char *ve = eroded.data;

    for (int i = 0; i < seg_height * seg_width; i++) {
        if (v1[i] < 120 && v0[i] >= 127) {
            v0[i] = 0;
        } else if (v1[i] < 120 && v0[i] < 127) {
            if (vd[i] >= 192 && ve[i] < 64) {
                v0[i] = v0[i];
            } else {
                v0[i] = 0;
            }
        }
    }
    return 0;
}

```

