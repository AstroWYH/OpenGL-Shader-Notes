```cpp
#define fourcc_code(a, b, c, d) ((__u32)(a) | ((__u32)(b) << 8) | \
                ((__u32)(c) << 16) | ((__u32)(d) << 24))

#define DRM_FORMAT_NV12		fourcc_code('N', 'V', '1', '2') /* 2x2 subsampled Cr:Cb plane */
#define DRM_FORMAT_NV21		fourcc_code('N', 'V', '2', '1') /* 2x2 subsampled Cb:Cr plane */

bool Fd2Texture(int fd, int fd_offset, int width, int height, int stride,
                                        CommonImageType type, GLuint *texture) {
    EGLint attrib_type = type == IMG_NV21 ? DRM_FORMAT_NV21 : DRM_FORMAT_NV12;
    EGLint attrib_list[] = {
            EGL_WIDTH, width,
            EGL_HEIGHT, height,
            EGL_LINUX_DRM_FOURCC_EXT,
            attrib_type,
            EGL_DMA_BUF_PLANE0_FD_EXT, fd,
            EGL_DMA_BUF_PLANE0_OFFSET_EXT, fd_offset,
            EGL_DMA_BUF_PLANE0_PITCH_EXT, stride,
            EGL_DMA_BUF_PLANE1_FD_EXT, fd,
            EGL_DMA_BUF_PLANE1_OFFSET_EXT, width * height,
            EGL_DMA_BUF_PLANE1_PITCH_EXT, stride,
            EGL_NONE,
    };

    EGLImageKHR egl_image = eglCreateImageKHR(
            eglGetCurrentDisplay(),
            EGL_NO_CONTEXT,
            EGL_LINUX_DMA_BUF_EXT,
            (EGLClientBuffer)NULL,
            attrib_list);

    if (egl_image == EGL_NO_IMAGE)
        LOGE("eglCreateImageKHR egl_image is null");

    if (EGL_BAD_PARAMETER == eglGetError())
        LOGE("eglCreateImageKHR not support");

    if (!(*texture))
        glGenTextures(1, texture);
    LOGD("eglCreateImageKHR CreateTexture texture %d", *texture);

    glBindTexture(GL_TEXTURE_EXTERNAL_OES, *texture);
    glTexParameteri(GL_TEXTURE_EXTERNAL_OES, GL_TEXTURE_MIN_FILTER, GL_NEAREST);
    glTexParameteri(GL_TEXTURE_EXTERNAL_OES, GL_TEXTURE_MAG_FILTER, GL_NEAREST);
    glEGLImageTargetTexture2DOES(GL_TEXTURE_EXTERNAL_OES, egl_image);
    eglDestroyImageKHR(eglGetCurrentDisplay(), egl_image);

    return true;
}
```

