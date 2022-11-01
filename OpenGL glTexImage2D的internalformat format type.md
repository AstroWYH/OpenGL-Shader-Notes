```cpp
void glTexImage2D(GLenum target,
　　　　　　　　　　GLint level,
　　　　　　　　　　GLint internalFormat,
　　　　　　　　　　GLsizei width,
　　　　　　　　　　GLsizei height,
　　　　　　　　　　GLint border,
　　　　　　　　　　GLenum format,
　　　　　　　　　　GLenum type,
　　　　　　　　　　const GLvoid * data);

glTexImage2D(GL_TEXTURE_2D,　　  //此纹理是一个2D纹理
　　　　　　　　0,　　　　　　　　　　//代表图像的详细程度, 默认为0即可 
　　　　　　　　3,　　　　　　　　　　//颜色成分R(红色分量)、G(绿色分量)、B(蓝色分量)三部分，若为4则是R(红色分量)、G(绿色分量)、B(蓝色分量)、Alpha
             					//指OpenGL内部存储这个纹理时所用的格式
　　　　　　　　TextureImage[0]->sizeX,　　//纹理的宽度
　　　　　　　　TextureImage[0]->sizeY,　　//纹理的高度
　　　　　　　　0,　　　　　　　　　　       //边框的值
　　　　　　　　GL_RGB,　　　　　　         //告诉OpenGL图像数据由红、绿、蓝三色数据组成
　　　　　　　　GL_UNSIGNED_BYTE,　       //组成图像的数据是无符号字节类型
             						   //format 和 type 指定的是client内存中存储的格式，也就是data指针中的所存放的texture的格式
　　　　　　　　TextureImage[0]->data);　　//告诉OpenGL纹理数据的来源,此例中指向存放在TextureImage[0]记录中的数据

这里关系到OpenGL中的pixel transfer操作，pixel transfer 表示从内存到显存(unpack)或者从显存到内存(pack)的过程。显然glTexIamge2D是unpack操作。

internalformat是指OpenGL内部存储这个纹理时所用的格式，同时指定了哪些分量以及每个分量对应的bit数，可以理解为显存中存储的格式，形如GL_RGB, GL_RGB32F, GL_COMPRESSED_RGB。格式形如GL_R32F，表示这个纹理只有一个分量，分量为32位浮点数，OpenGL网站上有详细说明。值得注意的是，如果该格式为GL_RGB，GL_RGBA，GL_DEPTH_COMPONENT等这种只指定分量个数的，那么OpenGL中会自动对每个分量进行截断（clamp），保证其范围为[0, 1]。因此如果不需要OpenGL自动截断，则应指定internalformat为GL_RGB16F类似的格式。

format 和 type 指定的是client内存中存储的格式，也就是data指针中的所存放的texture的格式。format形如GL_RG, GL_RED等，即指定哪些颜色分量。type是像素数据的bit depth，包括GL_UNSIGNED_BYTE，GL_FLOAT，GL_UNSIGNED_SHORT_5_6_5等。

类似的，glReadPixels()函数中也有一个format和type，和上述的type，format含义相同，即按照指定的格式从OpenGL显存内部读出到buffer中，也就是 pack 操作的格式。
```

