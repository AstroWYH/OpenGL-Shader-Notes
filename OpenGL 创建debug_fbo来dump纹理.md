简述：glReadPixels读取的是当前cur_fbo上attached的纹理。如果**想要读取的纹理**当前未bind fbo，则需要创建并bind fbo，并通过glFramebufferTexture2D将**想要读取的纹理**bind到fbo上，再进行读取（fbo可新创建，无限制）。

```cpp
{
    // 准备纹理dump_tex宽高和id
    int dump_width = render_output.bokeh_tex.width;
    int dump_height = render_output.bokeh_tex.height;
    GLuint dump_tex_id = render_output.bokeh_tex.texture_id;

    // 准备dump_buf
    std::vector<uint8_t> dump_buf;
    dump_buf.resize(dump_width * dump_height * 4);
    void* dump_ptr = dump_buf.data();

    // 获取当前cur_fbo
    GLint cur_fbo;
    glGetIntegerv(GL_FRAMEBUFFER_BINDING, &cur_fbo);

    // 创建debug_fbo并bind
    if (debug_fbo_ == 0) glGenFramebuffers(1, &debug_fbo_);
    glBindFramebuffer(GL_FRAMEBUFFER, debug_fbo_);

    // 获取debug_fbo的attached_tex，默认为0
    GLint attached_tex;
    glGetFramebufferAttachmentParameteriv(GL_FRAMEBUFFER, GL_COLOR_ATTACHMENT0,
                                          GL_FRAMEBUFFER_ATTACHMENT_OBJECT_NAME,
                                          &attached_tex);

    // 将dump_tex绑定到debug_fbo
    glFramebufferTexture2D(GL_FRAMEBUFFER, GL_COLOR_ATTACHMENT0,
                           false ? GL_TEXTURE_EXTERNAL_OES : GL_TEXTURE_2D,
                           dump_tex_id, 0);
    
    // 读取GPU dump_tex到CPU dump_buf
    glReadPixels(0, 0, dump_width, dump_height, GL_RGBA,
                 GL_UNSIGNED_BYTE, dump_ptr);
    
	// 将dump_buf转换成dump_mat
    cv::Mat dump_mat(dump_height, dump_width, CV_8UC4, dump_buf.data());
    cv::putText(dump_mat, std::to_string(g_frame_cnt), cv::Point(50, 50),
                cv::FONT_HERSHEY_SIMPLEX, 1, cv::Scalar(0, 0, 255), 2, 8);

    // 将dump_mat(BGR格式)写入.jpg文件
    ANC_LOGD("[wyh] RENDER g_frame_cnt:%lld", g_frame_cnt);
    fsp::DumpMat(fsp::StrCat("/data/local/tmp/dump_test/" + std::to_string(g_frame_cnt) +
                             ".jpg"), dump_mat, cv::COLOR_RGBA2BGR);

    // 将fbo重置为cur_fbo
    glBindFramebuffer(GL_FRAMEBUFFER, cur_fbo);
}
```

