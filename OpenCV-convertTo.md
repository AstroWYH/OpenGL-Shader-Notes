- convertTo：保持通道数一致，完成精度转换。
- 如：CV_8UC3---->CV_32FC3

```cpp
cv::Mat tmp = cv::Mat(height, width, CV_8UC3);

if (is_float) {
    // 对于浮点格式，从CV_8UC3->CV_32FC3
    // 对于浮点形式，如果纹理glTexImage2D的buf输入是dst，则dst需要提前归一化，否则不能识别，输出read后，需要重新*255放大回来
    // 经最新测试，浮点类型并不需要这个步骤！
    tmp.convertTo(dst, CV_32FC3, 1.0 / 255, 0);
} else {
    // 对于无符号整型格式，从CV_8UC3->从CV_8UC3
    // 对于整型格式，则不需要归一化
    tmp.convertTo(dst, CV_8UC3, 1, 0);
}
// 注意，该convertTo操作，需要保证通道数C一致，然后精度转换，最后*scale
```

