### **VBO 和 EBO**

- VBO（Vertex Buffer Object）是指顶点缓冲区对象，而 EBO（Element Buffer Object）是指图元索引缓冲区对象**，VBO 和 EBO 实际上是对同一类 Buffer 按照用途的不同称呼。**
- **OpenGL ES 2.0 编程中，用于绘制的顶点数组数据首先保存在 CPU 内存**，在调用 glDrawArrays 或者 glDrawElements 等进行绘制时，需要将顶点数组数据从 CPU 内存拷贝到显存。
- **但是很多时候我们没必要每次绘制的时候都去进行内存拷贝**，如果可以在显存中缓存这些数据，就可以在很大程度上降低内存拷贝带来的开销。
- OpenGL ES 3.0 编程中， VBO 和 EBO 的出现就是为了解决这个问题。
- VBO 和 EBO 的作用是在显存中提前开辟好一块内存**，用于缓存顶点数据或者图元索引数据，从而避免每次绘制时的 CPU 与 GPU 之间的内存拷贝，可以改进渲染性能，降低内存带宽和功耗。**
- **OpenGL ES 3.0 支持两类缓冲区对象：顶点数组缓冲区对象、图元索引缓冲区对象。**
- GL_ARRAY_BUFFER 标志指定的缓冲区对象用于保存顶点数组，GL_ELEMENT_ARRAY_BUFFER 标志指定的缓存区对象用于保存图元索引。

VBO（EBO）的创建和更新：

```cpp
// 创建 2 个 VBO（EBO 实际上跟 VBO 一样，只是按照用途的另一种称呼）
glGenBuffers(2, m_VboIds);

// 绑定第一个 VBO，拷贝顶点数组到显存
glBindBuffer(GL_ARRAY_BUFFER, m_VboIds[0]);
glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);

// 绑定第二个 VBO（EBO），拷贝图元索引数据到显存
glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, m_VboIds[1]);
glBufferData(GL_ELEMENT_ARRAY_BUFFER, sizeof(indices), indices, GL_STATIC_DRAW);
```

GL_STATIC_DRAW 标志标识缓冲区对象数据被修改一次，使用多次，用于绘制。

本例中顶点着色器和片段着色器增加 color 属性：

```glsl
//顶点着色器
#version 300 es                            
layout(location = 0) in vec4 a_position;   // 位置变量的属性位置值为 0 
layout(location = 1) in vec3 a_color;      // 颜色变量的属性位置值为 1
out vec3 v_color;                          // 向片段着色器输出一个颜色                          
void main()                                
{                                          
    v_color = a_color;                     
    gl_Position = a_position;              
};
//片段着色器
#version 300 es
precision mediump float;
in vec3 v_color;
out vec4 o_fragColor;
void main()
{
    o_fragColor = vec4(v_color, 1.0);
}
```

顶点数组数据和图元索引数据：

```cpp
// 4 vertices, with(x,y,z) ,(r, g, b, a) per-vertex
GLfloat vertices[] =
        {
                -0.5f,  0.5f, 0.0f,       // v0
                1.0f,  0.0f, 0.0f,        // c0
                -0.5f, -0.5f, 0.0f,       // v1
                0.0f,  1.0f, 0.0f,        // c1
                0.5f, -0.5f, 0.0f,        // v2
                0.0f,  0.0f, 1.0f,        // c2
                0.5f,  0.5f, 0.0f,        // v3
                0.5f,  1.0f, 1.0f,        // c3
        };
// Index buffer data
GLushort indices[6] = { 0, 1, 2, 0, 2, 3};
```

![image-20221221184721607](https://hanbabang-1311741789.cos.ap-chengdu.myqcloud.com/Pics/image-20221221184721607.png)

由于顶点位置和颜色数据在同一个数组里，一起更新到 VBO 里面，所以需要知道 2 个属性的步长和偏移量。

为获得数据队列中下一个属性值（比如位置向量的下个 3 维分量）我们必须向右移动 6 个 float ，其中 3 个是位置值，另外 3 个是颜色值，那么步长就是 6 乘以 float 的字节数（= 24 字节）。

同样，也需要指定顶点位置属性和颜色属性在 VBO 内存中的偏移量。

对于每个顶点来说，位置顶点属性在前，所以它的偏移量是 0 。而颜色属性紧随位置数据之后，所以偏移量就是 3 * sizeof(GLfloat) ，用字节来计算就是 12 字节。

使用 VBO 和 EBO 进行绘制。

```cpp
glUseProgram(m_ProgramObj);

//不使用 VBO 的绘制
glEnableVertexAttribArray(0);
glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, (3+3)*sizeof(GLfloat), vertices);

glEnableVertexAttribArray(1);
glVertexAttribPointer(1, 3, GL_FLOAT, GL_FALSE, (3+3)*sizeof(GLfloat), (vertices + 3));

glDrawElements(GL_TRIANGLES, 6, GL_UNSIGNED_SHORT, indices);

//使用 VBO 的绘制
glBindBuffer(GL_ARRAY_BUFFER, m_VboIds[0]);
glEnableVertexAttribArray(0);
glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, (3+3)*sizeof(GLfloat), (const void *)0);
glEnableVertexAttribArray(1);
glVertexAttribPointer(1, 3, GL_FLOAT, GL_FALSE, (3+3)*sizeof(GLfloat), (const void *)(3 *sizeof(GLfloat)));

glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, m_VboIds[1]);

glDrawElements(GL_TRIANGLES, 6, GL_UNSIGNED_SHORT, (const void *)0);
```

### **VAO**

- **VAO（Vertex Array Object）是指顶点数组对象，主要用于管理 VBO 或 EBO** ，减少 glBindBuffer 、glEnableVertexAttribArray、 glVertexAttribPointer 这些调用操作，**高效地实现在顶点数组配置之间切换**。

![image-20221221185311374](https://hanbabang-1311741789.cos.ap-chengdu.myqcloud.com/Pics/image-20221221185311374.png)

基于上小节的例子创建 VAO ：

```cpp
// 创建并绑定 VAO
glGenVertexArrays(1, &m_VaoId);
glBindVertexArray(m_VaoId);

// 在绑定 VAO 之后，操作 VBO ，当前 VAO 会记录 VBO 的操作
glBindBuffer(GL_ARRAY_BUFFER, m_VboIds[0]);
glEnableVertexAttribArray(0);
glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, (3+3)*sizeof(GLfloat), (const void *)0);
glEnableVertexAttribArray(1);
glVertexAttribPointer(1, 3, GL_FLOAT, GL_FALSE, (3+3)*sizeof(GLfloat), (const void *)(3 *sizeof(GLfloat)));

glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, m_VboIds[1]);

glBindVertexArray(GL_NONE);
```

使用 VAO 进行绘制：

```cpp
// 是不是精简了很多？
glUseProgram(m_ProgramObj);

glBindVertexArray(m_VaoId);

glDrawElements(GL_TRIANGLES, 6, GL_UNSIGNED_SHORT, (const void *)0)
```

### **UBO**

- UBO，**Uniform Buffer Object** 顾名思义，**就是一个装载 uniform 变量数据的缓冲区对象，本质上跟 OpenGL ES 的其他缓冲区对象没有区别**，创建方式也大致一致，都是显存上一块用于储存特定数据的区域。
- **当数据加载到 UBO ，那么这些数据将存储在 UBO 上，而不再交给着色器程序，所以它们不会占用着色器程序自身的 uniform 存储空间**，UBO 是一种新的从内存到显存的数据传递方式，另外 UBO 一般需要与 uniform 块配合使用。
- 本例将 MVP 变换矩阵设置为一个 uniform 块，即我们后面创建的 UBO 中将保存 3 个矩阵。

```glsl
#version 310 es
layout(location = 0) in vec4 a_position;
layout(location = 1) in vec2 a_texCoord;

layout (std140) uniform MVPMatrix
{
    mat4 projection;
    mat4 view;
    mat4 model;
};

out vec2 v_texCoord;
void main()
{
    gl_Position = projection * view * model * a_position;
    v_texCoord = a_texCoord;
}
```

设置 uniform 块的绑定点为 0 ，生成一个 UBO 。

```cpp
GLuint uniformBlockIndex = glGetUniformBlockIndex(m_ProgramObj, "MVPMatrix");
glUniformBlockBinding(m_ProgramObj, uniformBlockIndex, 0);

glGenBuffers(1, &m_UboId);
glBindBuffer(GL_UNIFORM_BUFFER, m_UboId);
glBufferData(GL_UNIFORM_BUFFER, 3 * sizeof(glm::mat4), nullptr, GL_STATIC_DRAW);
glBindBuffer(GL_UNIFORM_BUFFER, 0);

//定义绑定点为 0 buffer 的范围
glBindBufferRange(GL_UNIFORM_BUFFER, 0, m_UboId, 0, 3 * sizeof(glm::mat4));
```

绘制的时候更新 Uniform Buffer 的数据，更新三个矩阵的数据，注意偏移量。

```cpp
glBindBuffer(GL_UNIFORM_BUFFER, m_UboId);
glBufferSubData(GL_UNIFORM_BUFFER, 0, sizeof(glm::mat4), &m_ProjectionMatrix[0][0]);
glBufferSubData(GL_UNIFORM_BUFFER, sizeof(glm::mat4), sizeof(glm::mat4), &m_ViewMatrix[0][0]);
glBufferSubData(GL_UNIFORM_BUFFER, 2 *sizeof(glm::mat4), sizeof(glm::mat4), &m_ModelMatrix[0][0]);
glBindBuffer(GL_UNIFORM_BUFFER, 0);
```

### **FBO**

- **FBO（Frame Buffer Object）即帧缓冲区对象，实际上是一个可添加缓冲区的容器，可以为其添加纹理或渲染缓冲区对象（RBO）。**
- **FBO 本身不能用于渲染，只有添加了纹理或者渲染缓冲区之后才能作为渲染目标，它仅且提供了 3 个附着（Attachment），分别是颜色附着、深度附着和模板附着。**
- RBO（Render Buffer Object）即渲染缓冲区对象，是一个由应用程序分配的 2D 图像缓冲区。渲染缓冲区可以用于分配和存储颜色、深度或者模板值，可以用作 FBO 中的颜色、深度或者模板附着。
- 使用 FBO 作为渲染目标时，首先需要为 FBO 的附着添加连接对象，如颜色附着需要连接纹理或者渲染缓冲区对象的颜色缓冲区。

![image-20221221190159876](https://hanbabang-1311741789.cos.ap-chengdu.myqcloud.com/Pics/image-20221221190159876.png)

### **TBO**

- **纹理缓冲区对象，即 TBO（Texture Buffer Object），是 OpenGL ES 3.2 引入的概念，因此在使用时首先要检查 OpenGL ES 的版本，Android 方面需要保证 API >= 24 。**
- TBO 需要配合缓冲区纹理（Buffer Texture）一起使用，**Buffer Texture 是一种一维纹理，其存储数据来自纹理缓冲区对象（TBO），用于允许着色器访问由缓冲区对象管理的大型内存表**。
- **在 GLSL 中，只能使用 texelFetch 函数访问缓冲区纹理，缓冲区纹理的采样器类型为 samplerBuffer 。**
- 生成一个 TBO 的方式跟 VBO 类似，只需要绑定到 GL_TEXTURE_BUFFER ，而生成缓冲区纹理的方式与普通的 2D 纹理一样。
- TBO相比传统的纹理方式，可以大大的提高性能。

```cpp
//生成一个 Buffer Texture 
glGenTextures(1, &m_TboTexId);

float *bigData = new float[BIG_DATA_SIZE];
for (int i = 0; i < BIG_DATA_SIZE; ++i) {
    bigData[i] = i * 1.0f;
}

//生成一个 TBO ，并将一个大的数组上传至 TBO 
glGenBuffers(1, &m_TboId);
glBindBuffer(GL_TEXTURE_BUFFER, m_TboId);
glBufferData(GL_TEXTURE_BUFFER, sizeof(float) * BIG_DATA_SIZE, bigData, GL_STATIC_DRAW);

delete [] bigData;
```

使用纹理缓冲区的片段着色器，**需要引入扩展 texture buffer ，注意版本声明为 #version 320 es 。**

```glsl
#version 320 es
#extension GL_EXT_texture_buffer : require
in mediump vec2 v_texCoord;
layout(location = 0) out mediump  vec4 outColor;
uniform mediump samplerBuffer u_buffer_tex; // samplerBuffer
uniform mediump sampler2D u_2d_texture;
uniform mediump int u_BufferSize;
void main()
{
    mediump int index = int((v_texCoord.x +v_texCoord.y) /2.0 * float(u_BufferSize - 1));
    mediump float value = texelFetch(u_buffer_tex, index).x; // texelFetch
    mediump vec4 lightColor = vec4(vec3(vec2(value / float(u_BufferSize - 1)), 0.0), 1.0);
    outColor = texture(u_2d_texture, v_texCoord) * lightColor;
}
```

绘制时如何使用缓冲区纹理和 TBO ?

```cpp
glActiveTexture(GL_TEXTURE0);
glBindTexture(GL_TEXTURE_BUFFER, m_TboTexId);
glTexBuffer(GL_TEXTURE_BUFFER, GL_R32F, m_TboId); // glTexBuffer
GLUtils::setInt(m_ProgramObj, "u_buffer_tex", 0);
```

跟普通纹理的使用方式大致一样，只不过需要使用 glTexBuffer 绑定 TBO 到缓冲区纹理。

### **PBO**

- PBO （**Pixel Buffer Object**）是 OpenGL ES 3.0 的概念，称为像素缓冲区对象，主要被用于异步像素传输操作。PBO 仅用于执行像素传输，不连接到纹理，且与 FBO （帧缓冲区对象）无关。
- **PBO 类似于 VBO（顶点缓冲区对象），PBO 开辟的也是 GPU 缓存，而存储的是图像数据。**
- PBO 可以在 GPU 的缓存间快速传递像素数据，不影响 CPU 时钟周期，除此之外，PBO 还支持异步传输。
- **PBO 类似于“以空间换时间”策略，在使用一个 PBO 的情况下，性能无法有效地提升，通常需要多个 PBO 交替配合使用。**

![image-20221221190502977](https://hanbabang-1311741789.cos.ap-chengdu.myqcloud.com/Pics/image-20221221190502977.png)

如上图所示，利用 2 个 PBO 从帧缓冲区读回图像数据，使用 glReadPixels 通知 GPU 将图像数据从帧缓冲区读回到 PBO1 中，同时 CPU 可以直接处理 PBO2 中的图像数据。



参考链接：[面试中经常被问到的 OpenGL ES 对象，你知道的有哪些？（校正） (qq.com)](https://mp.weixin.qq.com/s/I-HJuzoEQfLJLP7AWjwrDw)