## 1 简介

- EGL 是**渲染 API（如 OpenGL ES）**和**原生窗口系统**之间的**接口**。

- 通常来说，OpenGL 是一个操作 GPU 的 API，它通过驱动向 GPU 发送相关指令，控制图形渲染管线状态机的运行状态，但是当涉及到与本地窗口系统进行交互时，就需要这么一个中间层，且它最好是与平台无关的。

- 因此 EGL 被设计出来，作为 OpenGL 和原生窗口系统之间的桥梁。

EGL API 是独立于 OpenGL ES 各版本标准的独立的一套 API，其主要作用是为 OpenGL 指令 创建 Context 、绘制目标 Surface 、配置 FrameBuffer 属性、Swap 提交绘制结果等。EGL 提供如下机制：

- 与设备原生窗口通信
- 查询绘制 surface 的可用类型和配置
- 创建绘制 surface
- 在 OpenGL ES 3.0 或其他渲染 API 之间同步渲染
- 管理纹理贴图等渲染资源

## 2 流程概述

EGL 是 OpenGL 和原生窗口系统之间的桥梁接口。

#### 1 创建连接

```cpp
EGLDisplay eglDisplay(EGLNativeDisplayType displayId); // eglGetDisplay
```

#### 2 初始化连接

```cpp
EGLBoolean eglInitialize(EGLDisplay display, EGLint *majorVersion, EGLint *minorVersion);
```

#### 3 获取配置

```cpp
EGLBoolean eglChooseConfig(EGLDispay display, 
                           const EGLint *attribList,
                           EGLConfig *config,
                           EGLint maxReturnConfigs,
                           ELGint *numConfigs );
```

#### 4 创建渲染区域

```cpp
EGLSurface eglCreateWindowSurface(EGLDisplay display,
                                  EGLConfig config,
                                  EGLNatvieWindowType window,
                                  const EGLint *attribList);
```

#### 5 创建渲染上下文

```cpp
EGLContext eglCreateContext(EGLDisplay display,
                            EGLConfig config,
                            EGLContext shareContext,
                            const EGLint* attribList);
```

#### 6 关联上下文和渲染区域

```cpp
EGLBoolean eglMakeCurrent(EGLDisplay display,
                          EGLSurface draw,
                          EGLSurface read,
                          EGLContext context);
```

#### 7 使用 OpenGL 相关 API 进行绘制

然后就是 OpenGL 里的东西了。

## 3 流程详情

#### 0 检查错误的方法

EGL 中大部分函数成功时都是返回 `EGL_TRUE`，失败返回 `EGL_FALSE`。至于故障原因，需要调用如下函数获取。返回最近调用 EGL 函数的错误代码，如果返回 `EGL_SUCCESS` 说明没有错误。

```cpp
EGLint eglGetError();
```

#### 1 创建，建立本地系统和 OpenGL ES 的连接

`displayId` 指定显示连接，一般使用默认的 `EGL_DEFAULT_DISPLAY`，即返回与默认原生窗口的连接。

```cpp
EGLDisplay eglDisplay(EGLNativeDisplayType displayId); // eglGetDisplay
```

相关错误码：

- EGL_NO_DISPLAY ：连接不可用

#### 2 初始化

```cpp
EGLBoolean eglInitialize(EGLDisplay display, // 创建步骤时返回的对象
                         EGLint *majorVersion, // 返回 EGL 主版本号
                         EGLint *minorVersion); // 返回 EGL 次版本号
```

相关错误码：

- EGL_NO_DISPLAY ：EGL 不能初始化
- EGL_BAD_DISPLAY ：没有指定有效的 display

示例：

```cpp
EGLint majorVersion;
EGLint minorVersion;
EGLDisplay display;
display = eglGetDisplay(EGL_DEFAULT_DISPLAY);
if (display == EGL_NO_DISPLAY) {
    // Unable to open connection to local windowing system
}
if (!eglInitialize(display, &majorVersion, &minorVersion)) {
    // Unable to initialize EGL. Handle and recover
}
```

#### 3 确定可用的 Surface 的配置

一旦初始化了EGL，就可以确定可用渲染表面的类型和配置了。有两种方法：

- 先使用 eglGetConfigs 查询每个配置，再使用 eglGetConfigAttrib 找出最好的选择。
- 指定一组需求，使用 eglChooseChofig 让 EGL 推荐最佳配置。

通常使用第二种方式更简单。两种方法均得到 EGLConfig 对象。EGLConfig 包含了渲染表面的所有信息，包括可用颜色、缓冲区等其他特性。

获取所有配置的函数：

```cpp
EGLBoolean eglGetConfigs(EGLDisplay display, // 指定显示的连接
                         EGLConfig *configs, // 指定 GLConfig 列表
                         EGLint maxReturnConfigs, // 最多返回的 GLConfig 数
                         EGLint *numConfigs); // 实际返回的 GLConfig 数
```

查询 EGLConfig 配置：

```cpp
EGLBoolean eglGetConfigAttrib(EGLDisplay display, // 指定显示的连接
                              EGLConfig config, // 指定要查询的 GLConfig
                              EGLint attribute, // 返回特定属性
                              EGLint *value); // 返回值
```

让 EGL 选择配置：

```cpp
EGLBoolean eglChooseConfig(EGLDispay display, // 指定显示的连接
                           const EGLint *attribList, // 指定 configs 匹配的属性列表，可以为 NULL
                           EGLConfig *config,   // 调用成功，返会符合条件的 EGLConfig 列表
                           EGLint maxReturnConfigs, // 最多返回的符合条件的 GLConfig 数
                           ELGint *numConfigs );  // 实际返回的符合条件的 EGLConfig 数
```

如果 `eglChooseChofig` 成功返回，则返回一组匹配你的标准的 `EGLConfig`。

示例：

```cpp
EGLint attribList[] = {
    EGL_RENDERABLE_TYPE, EGL_OPENGL_ES3_BIT,
    EGL_RED_SIZE, 5,
    EGL_GREEN_SIZE, 6,
    EGL_BLUE_SIZE, 5,
    EGL_DEPTH_SIZE, 1,
    EGL_NONE
};

const EGLint MaxConfigs = 10;
EGLConfig configs[MaxConfigs]; // We'll only accept 10 configs
EGLint numConfigs;
if (!eglChooseConfig(dpy, attribList, configs, MaxConfigs, &numConfigs)) {
    // Something didn't work … handle error situation
} else {
    // Everything's okay. Continue to create a rendering surface
}
```

#### 4 创建渲染区域 Surface

当有了符合条件的 `EGLConfig` 后，就可以通过 `eglCreateWindowSurface` 函数创建渲染区域。

```cpp
EGLSurface eglCreateWindowSurface(EGLDisplay display, // 指定显示的连接
                                  EGLConfig config, // 符合条件的 EGLConfig
                                  EGLNatvieWindowType window, // 指定原生窗口
                                  const EGLint *attribList); // 指定窗口属性列表，可为 NULL
```

eglCreateWindowSurface 方法在很多情况下可能失败，失败返回 EGL_NO_SURFACE。我们可以通过 eglGetError 获取相关错误。

相关错误码：

- EGL_BAD_MATCH ：提供了与窗口属性不匹配的 EGLConfig，或该 EGLConfig 不支持渲染到窗口
- EGL_BAD_CONFIG ：提供的 EGLConfig 没有得到系统支持
- EGL_BAD_NATIVE_WINDOW ：提供的原生窗口句柄无效
- EGL_BAD_ALLOC ：无法为新的窗口分配资源，或已经有和提供的原生窗口关联的 EGLConfig

示例：

```cpp
EGLint attribList[] = {
  EGL_RENDER_BUFFER, EGL_BACK_BUFFER,
  EGL_NONE
);

// 这里先省略了创建原生窗口的过程
// android工程里可能是：eglCreatePbufferSurface
EGLRenderSurface window = eglCreateWindowSurface(display, config, nativeWindow, attribList);

if (window == EGL_NO_SURFACE) {
    switch (eglGetError()) {
    case EGL_BAD_MATCH:
        // Check window and EGLConfig attributes to determine
        // compatibility, or verify that the EGLConfig
        // supports rendering to a window,
        break;
    case EGL_BAD_CONFIG:
        // Verify that provided EGLConfig is valid
        break;
    case EGL_BAD_NATIVE_WINDOW:
        // Verify that provided EGLNativeWindow is valid
        break;
    case EGL_BAD_ALLOC:
        // Not enough resources available. Handle and recover
        break;
    }
}
```

#### 5 创建上下文

上下文包含了操作所需的所有状态信息，OpenGL ES 3.0 必须有一个可用的上下文才能进行绘图。

```cpp
EGLContext eglCreateContext(EGLDisplay display, // 指定显示的连接
                            EGLConfig config, // 前面选好的 EGLConfig
                            EGLContext shareContext, // 允许其它 EGLContext 共享数据，使用 EGL_NO_CONTEXT 表示不共享
                            const EGLint* attribList); // 指定操作的属性列表，只能接受一个属性 EGL_CONTEXT_CLIENT_VERSION
```

示例：

```cpp
const ELGint attribList[] = {
    EGL_CONTEXT_CLIENT_VERSION, 3,
    EGL_NONE
};

EGLContext context = eglCreateContext(display, config, EGL_NO_CONTEXT, attribList);

if (context == EGL_NO_CONTEXT) {
    EGLError error = eglGetError();
    if (error == EGL_BAD_CONFIG) {
        // Handle error and recover
    }
}
```

#### 6 关联上下文

指定某个 `EGLContext` 为当前上下文，关联特定的 `EGLContext` 和 `EGLSurface`。

```cpp
EGLBoolean eglMakeCurrent(EGLDisplay display, // 指定显示的连接
                          EGLSurface draw, // EGL 绘图表面
                          EGLSurface read, // EGL 读取表面
                          EGLContext context); // 指定连接到该表面的上下文
```

这里存在两个 `EGLSurface`，具有更好的灵活性，在后续一些高级的 EGL 用法中将利用它。目前我们先把它们设置为同一个值。

#### 7 使用 OpenGL 相关 API 进行绘制

这个就是 OpenGL 里的东西了，这里不做介绍。



#### 参考链接

[(36条消息) EGL 介绍和使用_阿飞__的博客-CSDN博客_egl](https://blog.csdn.net/afei__/article/details/84867104?ops_request_misc=%7B%22request%5Fid%22%3A%22166178963816782248571467%22%2C%22scm%22%3A%2220140713.130102334.pc%5Fall.%22%7D&request_id=166178963816782248571467&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~hot_rank-4-84867104-null-null.142^v42^pc_rank_34,185^v2^control&utm_term=egl&spm=1018.2226.3001.4187)