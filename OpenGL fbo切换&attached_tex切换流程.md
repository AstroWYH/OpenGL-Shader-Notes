```cpp
// 查询当前cur_fbo，当前是否有bind fbo（2种情况，有or无）
GLint cur_fbo;
glGetIntegerv(GL_FRAMEBUFFER_BINDING, &cur_fbo);
LOGD("WatermarkRender::drawWatermark cur_fbo:%d", cur_fbo);

// 根据cur_fbo情况，考虑是否生成watermark_fbo_
if (cur_fbo == GL_NONE) {
    if (watermark_fbo_ == GL_NONE) {
        glGenFramebuffers(1, &watermark_fbo_);
        LOGD("WatermarkRender::drawWatermark gen watermark_fbo_:%d", watermark_fbo_);
    }
    glBindFramebuffer(GL_FRAMEBUFFER, watermark_fbo_);
}
LOGD("WatermarkRender::drawWatermark watermark_fbo_:%d", watermark_fbo_);

// 与上面告一段落，查询当前fbo(之前就有的，or新生成water_fbo_)附件bind的fbo_attached_tex
// 2种情况（和dst不同，or和dst相同）如果是新生成的water_fbo_，则此处fbo_attached_tex应该是0
GLint fbo_attached_tex;
glGetFramebufferAttachmentParameteriv(GL_FRAMEBUFFER, GL_COLOR_ATTACHMENT0,
                                      GL_FRAMEBUFFER_ATTACHMENT_OBJECT_NAME,
                                      &fbo_attached_tex);
LOGD("WatermarkRender::drawWatermark fbo_attached_tex:%d dst_tex:%d", fbo_attached_tex, dst_tex);
if (dst_tex != GL_NONE && fbo_attached_tex != dst_tex) {
    glFramebufferTexture2D(GL_FRAMEBUFFER, GL_COLOR_ATTACHMENT0,
                           is_oes ? GL_TEXTURE_EXTERNAL_OES : GL_TEXTURE_2D, dst_tex, 0);
}

// 正常bind input tex流程
glActiveTexture(GL_TEXTURE0);
glBindTexture(GL_TEXTURE_2D, watermark_tex_);
glUniform1i(mTextureLocation, 0);

glDrawArrays(GL_TRIANGLE_STRIP, 0, 4);
glDisableVertexAttribArray(mPositionLocation);
glDisableVertexAttribArray(mTextureCoordinateLocation);
glBindTexture(GL_TEXTURE_2D, 0);

// 根据fbo_attached_tex!=dst_tex的判断，这里进行fbo的还原bind fbo_attached_tex，or不进行
if (dst_tex != GL_NONE && fbo_attached_tex != dst_tex) {
    glFramebufferTexture2D(GL_FRAMEBUFFER, GL_COLOR_ATTACHMENT0,
                           is_oes ? GL_TEXTURE_EXTERNAL_OES : GL_TEXTURE_2D, fbo_attached_tex, 0);
}

// 根据刚进来是否有fbo的判断，这里进行fbo bind 0，or不进行
if (cur_fbo == GL_NONE) {
    glBindFramebuffer(GL_FRAMEBUFFER, 0);
}

glDisable(GL_BLEND);
return RETCODE_OK;
```

