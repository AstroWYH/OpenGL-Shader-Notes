```cpp
#include <android/hardware_buffer.h>

bool HardwareBufferUtils::Buffer2Texture(void* buffer, AncHumBufferType type, GLuint *texture) {
    if (buffer == nullptr) {
        ANC_LOGE("input buffer is null!");
        return false;
    }

    EGLClientBuffer eglClientBuffer;
    if (type == ANC_HUM_BUFFER_TYPE_HARDWAREBUFFER)
        eglClientBuffer = eglGetNativeClientBufferANDROID(static_cast<AHardwareBuffer *>(buffer));
    else
        eglClientBuffer = buffer;

    if (eglClientBuffer == nullptr) {
        ANC_LOGE("eglClientBuffer is null!");
        return false;
    }

    if (eglGetError() != EGL_SUCCESS) {
        ANC_LOGE("eglCreateImageKHR error!");
        return false;
    }

    EGLint attrib_list[] = {EGL_IMAGE_PRESERVED_KHR,
                      EGL_TRUE,
                      EGL_NONE,
                      EGL_NONE};

    EGLImageKHR egl_image = eglCreateImageKHR(eglGetCurrentDisplay(),
                                              EGL_NO_CONTEXT,
                                              EGL_NATIVE_BUFFER_ANDROID, // EGL_NATIVE_BUFFER_ANDROID
                                              eglClientBuffer,
                                              attrib_list);

    if (EGL_BAD_PARAMETER == eglGetError())
        ANC_LOGD("eglCreateImageKHR not support");


    if (!(*texture))
        glGenTextures(1, texture);
    ANC_LOGD("eglCreateImageKHR CreateTexture texture %d", *texture);

    glBindTexture(GL_TEXTURE_EXTERNAL_OES, *texture);
    glTexParameteri(GL_TEXTURE_EXTERNAL_OES, GL_TEXTURE_MIN_FILTER, GL_NEAREST);
    glTexParameteri(GL_TEXTURE_EXTERNAL_OES, GL_TEXTURE_MAG_FILTER, GL_NEAREST);
    glTexParameterf(GL_TEXTURE_EXTERNAL_OES, GL_TEXTURE_WRAP_S, GL_CLAMP_TO_EDGE);
    glTexParameterf(GL_TEXTURE_EXTERNAL_OES, GL_TEXTURE_WRAP_T, GL_CLAMP_TO_EDGE);

    glEGLImageTargetTexture2DOES(GL_TEXTURE_EXTERNAL_OES, egl_image);
    eglDestroyImageKHR(eglGetCurrentDisplay(), egl_image);

    return true;
}
```

