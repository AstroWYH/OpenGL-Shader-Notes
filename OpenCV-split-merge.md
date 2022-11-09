```c++
// src:8UC4
cv::Mat src(height, width, CV_8UC4, const_cast<unsigned char *>(rgba_data));
std::vector<cv::Mat> split_channels;
// 将8UC4 split成4个8UC1
split(src, split_channels);
// 根据传入的channels，如2通道，最终split_channels保留2通道，剩下RG
split_channels.erase(split_channels.end() - (4 - channels), split_channels.end());
// merge合并RG两通道为8UC2
merge(split_channels, tmp);
```

