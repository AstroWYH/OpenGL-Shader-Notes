```cpp
// 构建imwrite需要的8UC3 BGR
cv::Mat bgr_dump(height, width, CV_8UC3);

// 先将dump_result确保通道数不变，通过convertTo转换成8UCX
dump_result.convertTo(dump_result, cvt_cv_fmt, is_float ? 255 : 1, 0);

// 将dump_result：8UC3 RGB->bgr_dump：8UC3 BGR，再进行imwrite
// 这是因为opencv在read和write时，处理图像都需要是8UC3的BGR格式
// dump_result只需要是8UCX即可，因为有cv::COLOR_RGB2BGR、cv::COLOR_RGBA2BGR、cv::COLOR_GRAY2BGR，满足1、3、4通道
cv::cvtColor(dump_result, bgr_dump, cv::COLOR_RGB2BGR);
cv::imwrite(savePath, bgr_dump);

// 如果dump_result通道数为2，由于没有对应的cv::COLOR_RG2BGR，应该单独处理
if (channels == 2) {
    std::vector<cv::Mat> split_channels;
    cv::split(dump_result, split_channels);
    cv::Mat sup_mat_1c(height, width, CV_8UC1);
    split_channels.push_back(sup_mat_1c);
    cv::Mat sup_mat_3c(height, width, CV_8UC3);
    cv::merge(split_channels, sup_mat_3c);
    // 8UC3 RGB转换成8UC3 BGR
    cv::cvtColor(sup_mat_3c, bgr_dump, cv::COLOR_RGB2BGR);
}
```

