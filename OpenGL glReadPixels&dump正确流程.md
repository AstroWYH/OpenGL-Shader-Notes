简述：glReadPixels读取的是当前cur_fbo上attached的纹理。如果**想要读取的纹理**当前未bind fbo，则需要创建并bind fbo，并通过glFramebufferTexture2D将**想要读取的纹理**bind到fbo上，再进行读取（fbo可新创建，无限制）。

```cpp
std::vector<uint8_t> output;
output.resize(gpu_buffer.width() * gpu_buffer.height() * 4);
void* out_ptr = output.data();

GLint current_fbo;
glGetIntegerv(GL_FRAMEBUFFER_BINDING, &current_fbo);
LOG("debug current_fbo:%d", current_fbo);
FSP_CHECK_FOR_GL_ERROR();

// 必须要有一个fbo，将需要read的tex bind在上面，才能去read
if (debug_fbo_ == 0) {
    glGenFramebuffers(1, &debug_fbo_);
}
// 每次都进来bind debug_fbo_
glBindFramebuffer(GL_FRAMEBUFFER, debug_fbo_);

LOG("debug debug_fbo_:%d", debug_fbo_);
GLint color_attachment_name;
glGetFramebufferAttachmentParameteriv(GL_FRAMEBUFFER, GL_COLOR_ATTACHMENT0,
                                      GL_FRAMEBUFFER_ATTACHMENT_OBJECT_NAME,
                                      &color_attachment_name);
LOG("debug text_buf:%d color_attachment_name:%d",
         text_buf->name(), color_attachment_name);
FSP_CHECK_FOR_GL_ERROR();
if (color_attachment_name != text_buf->name()) { // 不将GL_COLOR_ATTACHMENT0换成tex 0的话，这里dbg1只走一次，然后一直走dbg2
    // Set the data from GLTexture object.
    FSP_CHECK_FOR_GL_ERROR();
    glViewport(0, 0, gpu_buffer.width(), gpu_buffer.height());
    FSP_CHECK_FOR_GL_ERROR();
    glFramebufferTexture2D(GL_FRAMEBUFFER, GL_COLOR_ATTACHMENT0,
                           false ? GL_TEXTURE_EXTERNAL_OES : GL_TEXTURE_2D, text_buf->name(), 0);
    FSP_CHECK_FOR_GL_ERROR();
    glReadPixels(0, 0, gpu_buffer.width(), gpu_buffer.height(), GL_RGBA,
                 GL_UNSIGNED_BYTE, out_ptr);
    FSP_CHECK_FOR_GL_ERROR();
    LOG("debug 1");
} else {
    FSP_CHECK_FOR_GL_ERROR();
    glReadPixels(0, 0, gpu_buffer.width(), gpu_buffer.height(), GL_RGBA,
                 GL_UNSIGNED_BYTE, out_ptr);
    FSP_CHECK_FOR_GL_ERROR();
    LOG("debug 2");
}

static int cnt = 0;
LOG("debug cnt:%d", cnt);

cv::Mat dump_result(gpu_buffer.height(), gpu_buffer.width(), CV_8UC4, output.data());
cv::putText(dump_result, std::to_string(int(cnt)), cv::Point(50, 50),
            cv::FONT_HERSHEY_SIMPLEX, 1, cv::Scalar(0, 0, 255), 2, 8);

fsp::DumpMat(fsp::StrCat("/data/local/tmp/",
                         std::to_string(cache_timestamp_),
                         "_debug_out_" + std::to_string(cnt) +
                         ".jpg"), dump_result, cv::COLOR_RGBA2BGR);


cnt++;
// 如果这个地方注释的话，那么每次换bind debug_fbo，该debug_fbo的附件不会消失，下次来还仍存在
// glFramebufferTexture2D(GL_FRAMEBUFFER, GL_COLOR_ATTACHMENT0,
//                        false ? GL_TEXTURE_EXTERNAL_OES : GL_TEXTURE_2D, 0, 0);
// 最后换回bind 0
glBindFramebuffer(GL_FRAMEBUFFER, 0);

```

