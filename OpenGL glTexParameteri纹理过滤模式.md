### GL_TEXTURE_WRAP_S & GL_TEXTURE_WRAP_T

纹理的坐标系统叫做ST坐标系统，和xy坐标系统一样，s对应x，t对应y，因此GL_TEXTURE_WRAP_S和GL_TEXTURE_WRAP_T表示超出范围的纹理处理方式，可以设置的值如下：GL_CLAMP_TO_EDGE、GL_REPEAT、GL_MIRRORED_REPEAT。

**将纹理的坐标值设置超出界限**，纹理坐标如下：后面的部分使用纹理的最后像素的颜色值。

```java
var texBuffer = GLTools.array2Buffer(
            floatArrayOf(
                0.0f, 0.0f,
                0.0f, 1.0f,
                2.0f, 1.0f,
                2.0f, 0.0f
            )
        )
```

- GL_CLAMP_TO_EDGE：采样纹理边缘，即剩余部分显示纹理临近的边缘颜色值。效果如下：

![image-20221125103207085](https://hanbabang-1311741789.cos.ap-chengdu.myqcloud.com/Pics/image-20221125103207085.png)

- GL_REPEAT：重复纹理。效果如下：

![image-20221125103522956](https://hanbabang-1311741789.cos.ap-chengdu.myqcloud.com/Pics/image-20221125103522956.png)

- GL_MIRRORED_REPEAT:镜像重复，效果如下：

![image-20221125103537121](https://hanbabang-1311741789.cos.ap-chengdu.myqcloud.com/Pics/image-20221125103537121.png)

### GL_TEXTURE_MIN_FILTER & GL_TEXTURE_MAG_FILTER

当纹理的大小和渲染屏幕的大小不一致时会出现两种情况：

- 第一种情况：纹理大于渲染屏幕（**纹理需要缩小，对应MIN**），将会有一部分像素无法映射到屏幕上，对应于GL_TEXTURE_MIN_FILTER。

- 第二种情况：纹理小于渲染屏幕（**纹理需要放大，对应MAG**），没有足够的像素映射到屏幕上，GL_TEXTURE_MAG_FILTER。


可设置的值为GL_NEAREST 、GL_LINEAR。（此处暂不讨论mipmap）。

- GL_NEAREST：使用纹理中坐标最接近的一个像素的颜色作为需要绘制的像素颜色。

- GL_LINEAR：使用纹理中坐标最接近的若干个颜色，通过加权平均算法得到需要绘制的像素颜色，与GL_NEAREST比较速度较慢。

#### 邻近过滤GL_NEAREST （Nearest Neighbor Filtering）

OpenGL默认的纹理过滤方式。当设置为GL_NEAREST的时候，OpenGL会选择中心点最接近纹理坐标的那个像素。下图中**你可以看到四个像素，加号代表纹理坐标**（说明**此时是纹理小于渲染屏幕，纹理需要放大MAG**）。左上角那个纹理像素的中心距离纹理坐标最近，会被选择为样本颜色

```cpp
我的理解（可能有误）：渲染屏幕的这4个像素，都会被渲染成这个颜色。即使有误，不管怎么说，不管是需要放大还是缩小，考虑多纹理1像素，或多像素1纹理，就能想通。
```

![image-20221125105043403](https://hanbabang-1311741789.cos.ap-chengdu.myqcloud.com/Pics/image-20221125105043403.png)

#### 线性过滤GL_LINEAR（(Bi)linear Filtering）

会基于纹理坐标附近的纹理像素，计算出一个插值，近似出这些纹理像素之间的颜色。一个纹理像素的中心距离纹理坐标越近，那么这个纹理像素的颜色对最终的样本颜色的贡献越大。返回的颜色是邻近像素的混合色：

![image-20221125105109104](https://hanbabang-1311741789.cos.ap-chengdu.myqcloud.com/Pics/image-20221125105109104.png)

那么这两种纹理过滤方式有怎样的视觉效果呢？
比如让我们看看在一个很大的物体上应用一张低分辨率的纹理会发生什么吧（纹理被放大了，每个纹理像素都能看到）：

![image-20221125105134930](https://hanbabang-1311741789.cos.ap-chengdu.myqcloud.com/Pics/image-20221125105134930.png)

- GL_NEAREST产生了**颗粒状**的图案，我们能够**清晰**看到组成纹理的像素。
- 而GL_LINEAR能够产生**更平滑**的图案，很难看出单个的纹理像素。
- GL_LINEAR可以产生更真实的输出，但有些开发者更喜欢8-bit风格，所以他们会用GL_NEAREST选项。
- GL_LINEAR比GL_NEAREST性能慢一些。