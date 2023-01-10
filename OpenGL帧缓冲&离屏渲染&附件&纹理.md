# OpenGL帧缓冲&离屏渲染&附件&纹理

### 1 帧缓冲

**目的**：在之前，所有的物体都是中规中矩的显示的，只考虑了光照对物体的影响，那假设想要显示特殊的效果该怎么操作呢？例如马赛克风、将所有的物体都显示为黑白色，就像上世纪80年代的灰白电视一样，又或者说将整个场景渲染到一张泛黄的纸上以体现出年代感……当然是修改着色器，事实上，很多地方都是这么做的，不过有些情况下，场景中的物体和对应的着色器都不少，**若是想要整个场景（视口）体现出某个效果**，就需要借助别的方法了。

**概况：想办法把当前的场景作为一张纹理存起来，然后再去全屏渲染这张“纹理”，在这种情况下，我们只需要给这张“纹理”编写一个独一无二的着色器就可以了。**

几种不同类型的屏幕缓冲，这几种缓冲结合起来叫做帧缓冲(Framebuffer)。

- 用于写入颜色值的**颜色缓冲**。
- 用于写入深度信息的**深度缓冲**。
- 以及允许我们基于一些条件丢弃指定片段的**模板缓冲**。

我们目前所做的渲染操作都是是在默认的帧缓冲之上进行的，**当你创建了你的窗口的时候默认帧缓冲就被创建和配置好了（GLFW为我们做了这件事）**。是的，GLFW已经为我们把这份代码写了，只不过我们是要自己再“客制化”一个想要的结果，**OpenGL给了我们自己定义帧缓冲的自由**，我们可以选择性的定义自己的颜色缓冲、深度和模板缓冲。和VBO、EBO一样，也可以用同样的方法创建和绑定一个FBO（帧缓冲）变量：

```glsl
GLuint FBO;
glGenFramebuffers(1, &FBO);
glBindFramebuffer(GL_FRAMEBUFFER, FBO);
```

绑定的目标有以下3种，一般都是第一种：

- GL_FRAMEBUFFER：绑定到目标后，接下来所有的读、写帧缓冲的操作都会影响到当前绑定的帧缓冲。
- GL_READ_FRAMEBUFFER：允许读取操作。
- GL_DRAW_FRAMEBUFFER：允许进行渲染、清空和其他的写入操作。

构建一个完整的帧缓冲必须满足以下条件，如果不满足条件，则属于无效构建：

- 必须往里面加入至少一个附件（颜色、深度、模板缓冲）。
- 必须要有至少一个为颜色附件。
- 所有的附件都应该已经存储在内存中的。
- 每个缓冲都应该有同样数目的样本。

样本是什么暂时不管，后面会有讲到，总而言之，因为帧缓冲的构建需要条件，所以我们在后面需要用 glCheckFramebufferStatus 函数来检查是否真的满足所有的条件，如果返回的值是 GL_FRAMEBUFFER_COMPLETE 就说明创建成功了。

```glsl
if (glCheckFramebufferStatus(GL_FRAMEBUFFER) != GL_FRAMEBUFFER_COMPLETE)
    cout << "ERROR::FRAMEBUFFER:: Framebuffer is not complete!" << endl;
```

### 2 离屏渲染

- On-Screen Rendering：当前屏幕渲染，在当前用于显示的屏幕缓冲区进行渲染操作。
- Off-Screen Rendering：离屏渲染，在当前屏幕缓冲区以外新开辟一个缓冲区进行渲染操作。

......

- 回到上面的帧缓冲代码，通过glBindFramebuffer方法，就相当于切换了当时的缓冲区，后续所有渲染操作将渲染到**当前绑定的帧缓冲的附加缓冲中（即离屏渲染）**，如果当前激活的帧缓冲**不是默认的帧缓冲**，**渲染命令对窗口的视频输出就不会产生任何影响**。
- 因此后面**若是要渲染到主窗口，就必须要通过 glBindFramebuffer(GL_FRAMEBUFFER, 0) 来使默认帧缓冲被激活。**

当我们做完所有帧缓冲操作后，不要忘记删除帧缓冲对象：

```glsl
glDeleteFramebuffers(1, &fbo);
```

好了，现在回到最初的目的：

```
想办法把当前的场景作为一张纹理存起来，然后再去全屏渲染这张“纹理”，在这种情况下，我们只需要给这张“纹理”编写一个独一无二的着色器就可以了。
```

在此，目的就变成了：

1. 自定义一个帧缓冲并且启用，然后走之前正常的渲染逻辑，这个时候，**本应该在屏幕中展示的图像就被渲染到了自定义的帧缓冲中**。
2. 接下来，我们再想办法**将自定义帧缓冲中的数据转变为纹理**，并且**对这张纹理在默认帧缓冲（主窗口）中进行渲染显示**。

接下来，就是想办法往帧缓冲里添加附件，**一个附件就是一个内存地址**，这个内存地址里面包含一个为帧缓冲准备的缓冲，它可以是个图像。当创建一个附件的时候我们有两种方式可以采用：

- 纹理。
- 渲染缓冲(renderbuffer)对象。

### 3 纹理附件

先创建一个帧缓冲的纹理：

```glsl
GLuint getAttachmentTexture()
{
    GLuint textureID;
    glGenTextures(1, &textureID);
    glBindTexture(GL_TEXTURE_2D, textureID);
    glTexImage2D(GL_TEXTURE_2D, 0, GL_RGB, WIDTH, HEIGHT, 0, GL_RGB, GL_UNSIGNED_BYTE, NULL); // 与普通纹理不同，data处传递NULL。
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR);
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR);
    glBindTexture(GL_TEXTURE_2D, 0);
    return textureID;
}
```

这里和之前的定义纹理有三点不同：

1. 纹理的大小为屏幕的大小（测试是800 x 600）。
2. **不需要传递data参数，只分配内存而不去填充**，纹理填充会在渲染到帧缓冲的时候去做。
3. 不再关心环绕方式或者Mipmap。

创建完纹理后，紧接着就是把它附加到帧缓冲上：

```C++
glFramebufferTexture2D(GL_FRAMEBUFFER, GL_COLOR_ATTACHMENT0, GL_TEXTURE_2D, textureColorbuffer, 0);

glFramebufferTexture2D 函数参数：
- target：帧缓冲类型的目标
- attachment：附加的附件的类型，目前附加的是一个颜色附件
- textarget：你希望附加的纹理类型，目前总是GL_TEXTURE_2D
- texture：附加的实际纹理，用上面生成的texture
- level：Mipmap level

扩展：如果想要附加深度缓冲和模板缓冲，就需要在生成纹理时是用 GL_DEPTH24_STENCIL8 的纹理格式和内部类型，这样的纹理每32位数值就包含了24位的深度信息和8位的模板信息，不过这里暂时不需要考虑，大致用法如下：

glTexImage2D(GL_TEXTURE_2D, 0, GL_DEPTH24_STENCIL8, WIDTH, HEIGHT, 0, GL_DEPTH_STENCIL, GL_UNSIGNED_INT_24_8, NULL);
glFramebufferTexture2D(GL_FRAMEBUFFER, GL_DEPTH_STENCIL_ATTACHMENT, GL_TEXTURE_2D, texture, 0);
```

### 4 渲染缓冲对象（RBO）

- 纹理是一个帧缓冲的可行附件类型，除此之外，OpenGL还有渲染缓冲对象(Renderbuffer objects)，**渲染缓存是为离线渲染而新引进的，它容许将一个场景直接渲染到一个渲染缓存对象中，而不是渲染到纹理对象中**。RBO并不能单独使用，必须配合FBO，与opengl缓冲区对应，RBO可以存放颜色、深度、模板数据。
- 和纹理图像一样，渲染缓冲对象也是一个缓冲，它可以是一堆字节、整数、像素或者其他东西。渲染缓冲对象的一大优点是，它以OpenGL原生渲染格式储存它的数据，因此在离屏渲染到帧缓冲的时候，这些数据时已经被优化过的了，这样的话，写入或把它们的数据简单地到其他缓冲的时候**非常快**，之前提到的函数glfwSwapBuffers交换缓冲区，同样以渲染缓冲对象实现。

```
glGenRenderbuffers(1, &RBO);
glBindRenderbuffer(GL_RENDERBUFFER, RBO);
glRenderbufferStorage(GL_RENDERBUFFER, GL_DEPTH24_STENCIL8, WIDTH, HEIGHT);
glBindRenderbuffer(GL_RENDERBUFFER, 0);
glFramebufferRenderbuffer(GL_FRAMEBUFFER, GL_DEPTH_STENCIL_ATTACHMENT, GL_RENDERBUFFER, RBO);
```

- 由于渲染缓冲对象通常是只写的，它们经常作为深度和模板附件来使用，因为大多数时候并不需要从深度和模板缓冲中读取数据。

```
glRenderbufferStorage：创建一个深度和模板渲染缓冲对象，第2个参数指定渲染缓冲区的颜色可渲染、深度可渲染或模板可渲染格式，后面两个参数为指定缓冲区的宽和高。
glFramebufferRenderbuffer：和glFramebufferTexture2D目的一样，将渲染缓冲区附加到当前帧缓冲区上。
```

到现在帧缓冲就做好了：绑定了颜色缓冲，也有了深度和模板缓冲。如果遗漏了某个缓冲，那么对应的测试就不会工作，因为当前绑定的帧缓冲里没有对应的缓冲。

![image-20220614163720375](https://hanbabang-1311741789.cos.ap-chengdu.myqcloud.com/Pics/image-20220614163720375.png)

好了，现在又回到之前的目的：

```
1）定义一个帧缓冲并且启用，然后走之前正常的渲染逻辑，这个时候，本应该在屏幕中展示的图像就被渲染到了自己定义的帧缓冲中。
2）接下来，我们再想办法将帧缓冲中的数据转变为纹理，并且对这张纹理在默认帧缓冲中进行渲染显示。
```

在此，目的就变成了：绑定为上面自己定义的帧缓冲，像往常那样渲染场景，之后绑定回默认帧缓冲，简单的绘制一个全屏四边形，用自己的帧缓冲的颜色缓冲作为他的纹理。（详情见OpenGL帧缓冲代码示例：framebuffers.cpp）

### 5 灰度图

根据上文的目的，整体的渲染流程就是这样的：

```glsl
while (!glfwWindowShouldClose(window))
{
    //……
    glBindFramebuffer(GL_FRAMEBUFFER, FBO); // glBindFramebuffer绑成自定义FBO
    glClearColor(0.0f, 0.0f, 0.0f, 1.0f);
    glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT | GL_STENCIL_BUFFER_BIT);
    
    //正常渲染流程……
 
    glBindFramebuffer(GL_FRAMEBUFFER, 0); // glBindFramebuffer绑成“主窗口”
    glClearColor(0.0f, 0.0f, 0.0f, 1.0f);
    glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT | GL_STENCIL_BUFFER_BIT);
    
    shaderScreen.Use();
    glBindVertexArray(quadVAO);
    glBindTexture(GL_TEXTURE_2D, textureColorbuffer); // 借机在主窗口输出“自定义shader的屏幕大小的纹理”
    glDrawArrays(GL_TRIANGLES, 0, 6);
 
    //……
}
```

也可以在渲染最后的“全场景”纹理时，**关闭深度测试和模板测试**，毕竟没有必要了。**不需要考虑包括矩阵变换**的很多东西，毕竟就是最简单的绘制四边形，如果代码是没问题的，那么就会出现和之前章节中一摸一样的场景，只不过处理的方式不一样

好了，既然这个时候整个场景就是一个纹理，并且有着专属的着色器，那么就可以很容易的实现下面的效果（图片变黑白）：

![image-20220614194732508](https://hanbabang-1311741789.cos.ap-chengdu.myqcloud.com/Pics/image-20220614194732508.png)

着色器代码如下：

```glsl
// .vs
#version 330 core
layout (location = 0) in vec2 position;
layout (location = 1) in vec2 texture;
out vec2 texIn;
void main()
{
    gl_Position = vec4(position.x, position.y, 0.0f, 1.0f);
    texIn = texture;
}

// .fs
#version 330 core
in vec2 texIn;
out vec4 color;
uniform sampler2D screenTexture;
void main()
{
    color = texture(screenTexture, texIn);
    float average = (color.r + color.g + color.b) / 3.0;
    // 人眼趋向于对绿色更敏感，对蓝色感知比较弱，所以为了获得更精确的符合人体物理的结果，往往使用下面的计算方法:
    // float average = 0.2126 * color.r + 0.7152 * color.g + 0.0722 * color.b;
    color = vec4(average, average, average, 1.0); // 三通道变单通道，即成为灰度图
}
```

### 6 Kernel矩阵

黑白反射和模糊的区别：

- 对于黑白和反色，我们只需要考虑当前的像素点，并且通过一定的运算获得。
- 而对于模糊和锐化，就不止需要考虑当前这一像素点了，还需要考虑其周围像素点的颜色，也就是说，需要从当前纹理值的周围采样多个纹理值，创造性地把它们结合起来参与计算，这个时候就需要用到Kernel矩阵。

一个简单的**模糊Kernel矩阵**，它代表着当前像素值 =（斜邻近4个像素的值 + 上下左右4个像素的值 * 2 + 自身像素的值 * 4）/ 16：

![image-20220614195507657](https://hanbabang-1311741789.cos.ap-chengdu.myqcloud.com/Pics/image-20220614195507657.png)

至于这个邻近的像素怎么取，主要是通过**纹理坐标+纹理坐标的差**获得，对应的片段着色器代码如下：

```glsl
#version 330 core
in vec2 texIn;
out vec4 color;
uniform sampler2D screenTexture;
const float offset = 1.0 / 500;
void main()
{
    vec2 offsets[9] = vec2[](
        vec2(-offset, offset),
        vec2(0.0f,    offset),
        vec2(offset,  offset),
        vec2(-offset, 0.0f),
        vec2(0.0f,    0.0f),
        vec2(offset,  0.0f),
        vec2(-offset, -offset),
        vec2(0.0f,    -offset),
        vec2(offset,  -offset)
    );
    float kernel[9] = float[](
        1.0 / 16, 2.0 / 16, 1.0 / 16,
        2.0 / 16, 4.0 / 16, 2.0 / 16,
        1.0 / 16, 2.0 / 16, 1.0 / 16  
    );
    vec3 sampleTex[9];
    for(int i = 0; i < 9; i++)
    {
        sampleTex[i] = vec3(texture(screenTexture, texIn.st + offsets[i]));
    }
    vec3 col;
    for(int i = 0; i < 9; i++)
        col += sampleTex[i] * kernel[i];
    color = vec4(col, 1.0);
}
```

可以看到，代码中设置了偏移量为0.002，逻辑很容易看懂。如果没问题的话，最终就可以得到下面的效果：

![image-20220614200040324](https://hanbabang-1311741789.cos.ap-chengdu.myqcloud.com/Pics/image-20220614200040324.png)

对应图片锐化的Kernel矩阵和效果如下：

![image-20220614200103659](https://hanbabang-1311741789.cos.ap-chengdu.myqcloud.com/Pics/image-20220614200103659.png)

![image-20220614200113078](https://hanbabang-1311741789.cos.ap-chengdu.myqcloud.com/Pics/image-20220614200113078.png)

可能会觉得效果和想象中的不太一样，毕竟这是最简单的Kernel矩阵，也有更多更大的Kernel矩阵可以得到一些更有意思的效果。就拿模糊来说，还有“运动模糊”、“醉酒模糊”等，它们都有对应的Kernel矩阵，在一些像Photoshop这样的软件中也是使用这些kernel作为图像操作工具的，对图像和kernel矩阵进行逐个元素相乘再求和的操作，就是类卷积的过程，一般来讲：

- 绝大多数的kernel矩阵，它们所有元素的和加起来等于1。
- kernel矩阵不一定是3 x 3，还可以是 5 x 5、9 x 9 或更大。

#### 参考链接

[(4条消息) OpenGL基础33：帧缓冲（上）之离屏渲染_Jaihk662的博客-CSDN博客_opengl离屏渲染](https://blog.csdn.net/Jaihk662/article/details/107320904)

[(4条消息) OpenGL基础34：帧缓冲（中）之附件_Jaihk662的博客-CSDN博客](https://blog.csdn.net/Jaihk662/article/details/107356789?spm=1001.2101.3001.6650.18&depth_1-utm_relevant_index=25)

[(4条消息) OpenGL基础35：帧缓冲（下）之简单图像处理_Jaihk662的博客-CSDN博客_opengl 图像处理](https://blog.csdn.net/Jaihk662/article/details/107362720?ops_request_misc=%7B%22request%5Fid%22%3A%22165519188416781483713273%22%2C%22scm%22%3A%2220140713.130102334.pc%5Fblog.%22%7D&request_id=165519188416781483713273&biz_id=0&spm=1018.2226.3001.4450)

