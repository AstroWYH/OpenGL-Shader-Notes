```cpp
{
    LOG("[wyh] OUTPUT! g_frame_cnt:%lld", g_frame_cnt);
    // 准备dump_buf(nv21/12格式)
    int dump_width = result_->uframe.width;
    int dump_height = result_->uframe.height;
    void* dump_buf = result_->uframe.data.buf;
    
    // 准备dump_yuv_mat(注意宽高和格式)
    cv::Mat dump_yuv_mat(dump_height * 3 / 2, dump_width, CV_8UC1, dump_buf);
    cv::Mat dump_bgr_mat;
    
    // 通过cv::cvtColor和cv::COLOR_YUV2BGR_NV12，将dump_yuv_mat转换成dump_bgr_mat
    cv::cvtColor(dump_yuv_mat, dump_bgr_mat, cv::COLOR_YUV2BGR_NV12);
    
    // 将dump_bgr_mat写为xxx.jpg文件
    cv::imwrite("/data/local/tmp/dump_test/"+std::to_string(g_frame_cnt)+"_output.jpg",
                dump_bgr_mat);
}
```

