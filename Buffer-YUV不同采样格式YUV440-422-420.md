# 一、[YUV](https://so.csdn.net/so/search?q=YUV&spm=1001.2101.3001.7020)和YCbCr

## 1、定义

YUV，是一种颜色编码方法，常使用在各个[视频处理](https://so.csdn.net/so/search?q=视频处理&spm=1001.2101.3001.7020)组件中。Y表示明亮度（Luminance或Luma），也就是灰阶值，U和V表示的则是色度（Chrominance或Chroma），作用是描述影像色彩及饱和度，用于指定像素的颜色。

[YCbCr](https://so.csdn.net/so/search?q=YCbCr&spm=1001.2101.3001.7020)，这里的Y和YUV的Y相同，而Cb和Cr则为蓝色和红色的浓度偏移量成份，也是用来表示色彩的。

## 2、区别和联系

本质上来说两者没有什么区别，YCbCr 是在世界数字组织视频标准研制过程中作为ITU - R BT.601 建议的一部分，其实是YUV经过缩放和偏移的翻版。

YUV 主要是用在彩色电视中，用于模拟信号表示。YCbCr 是用在数字视频、图像的压缩和传输，例如H264、HEVC、JPEG、MPEG均采用此格式。一般人们所讲的YUV大多是指YCbCr。

# 二、不同的[采样](https://so.csdn.net/so/search?q=采样&spm=1001.2101.3001.7020)格式

写在前面：本部分参考[如何理解 YUV ？](https://zhuanlan.zhihu.com/p/85620611)、[Chrominance Subsampling in Digital Images](http://dougkerr.net/Pumpkin/articles/Subsampling.pdf)

一张图片是由一个个像素点组成，1920x1080即可理解为水平方向有1920个像素点，竖直方向有1080个像素点，每个像素都应当包含Y、U、V三个分量，但是在对色度二次采样的时候，会让Y分量共用UV分量，才出现了YUV422、YUV420等名称。

长图不好截图，裁成了很多段，下图说明了两种采样表示模式。
左边使用一个方框来表示一个像素点，每个小方块不管有没有涂黑都包含一个Y分量，被涂黑的方框内共用一组色度分量（即U+V分量），而那个小黑点就代表色度中心（没啥用，方便看而已）。

右边是**二次采样模式标记法**，即**J：a：b**表示法。图中的矩形框就是一个**参考块**，框里的圆点代表一个像素点，涂黑了就代表Y、U、V都有，没涂黑就只含有Y分量，至于左上角那个直角符号就是说明这个参考块取在最左上角。这个参考块的大小呢，一般取高度为2个像素点，宽度也就是J一般取4像素点，仔细看会发现参考块的左边有两个数字，这两个数字就代表每行黑点的个数。

其实，YUV420这些后面的数字就是通过4x2的参考块来确定的。可以观察一下下面参考块左边的小数字。

![image-20221227170111188](https://hanbabang-1311741789.cos.ap-chengdu.myqcloud.com/Pics/image-20221227170111188.png)

## 1、YUV444

![image-20221227170133579](https://hanbabang-1311741789.cos.ap-chengdu.myqcloud.com/Pics/image-20221227170133579.png)

上图是YUV444的采样模型，左图和右图都可以看出来每个像素点都有一对UV分量，这就相当于压根没有做色度二次采样。即每一个Y对应一组UV分量。

## 2、YUV440

![image-20221227170158157](https://hanbabang-1311741789.cos.ap-chengdu.myqcloud.com/Pics/image-20221227170158157.png)

上图是YUV440的采样模型，左图可以看出来在水平方向上每一个像素都有UV分量，而在竖直方向上每两个像素共用一对UV分量；在右图的表现就是第一行4个全黑，第二行四个全白。即每两个Y对应一组UV分量。

## 3、YUV422

![image-20221227170210890](https://hanbabang-1311741789.cos.ap-chengdu.myqcloud.com/Pics/image-20221227170210890.png)

上图是YUV422的采样模型，左图可以看出来在竖直方向上每一个像素都有UV分量，而在水平方向上每两个像素共用一对UV分量；在右图的表现就是每行两个黑两个白。即每两个Y对应一组UV分量。

## 4、YUV420

![image-20221227170223787](https://hanbabang-1311741789.cos.ap-chengdu.myqcloud.com/Pics/image-20221227170223787.png)

上图是YUV420的采样模型，左图可以看出来四个像素点共用一对UV分量，在水平方向上每两个像素共用一对UV分量，在竖直方向上也是每两个像素包含一对UV分量；在右图的表现就是第一行两个黑两个白，第二行全白。即每四个Y对应一组UV分量。

## 5、YUV411

![image-20221227170247458](https://hanbabang-1311741789.cos.ap-chengdu.myqcloud.com/Pics/image-20221227170247458.png)

上图是YUV411的采样模型，左图可以看出来在水平方向上每四个像素点共用一对UV分量，而在竖直方向上每个像素都包含一对UV分量；在右图的表现就是两行都是一个黑三个白。即每四个Y对应一组UV分量。

## 6、YUV410

![image-20221227170300892](https://hanbabang-1311741789.cos.ap-chengdu.myqcloud.com/Pics/image-20221227170300892.png)

上图是YUV410的采样模型，左图可以看出来8个像素点共用一对UV分量，在水平方向上每四个像素点共用一对UV分量，在竖直方向上每两个像素点共用一对UV分量；在右图的表现就是只有第一行有一个黑其他全白。即每八个Y对应一组UV分量。



#### 参考链接

[(40条消息) 关于YUV格式的一些总结_dongfangxingyu1的博客-CSDN博客_p010格式](https://blog.csdn.net/u012794472/article/details/124932566?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_baidulandingword~default-1-124932566-blog-117090757.pc_relevant_recovery_v2&spm=1001.2101.3001.4242.2&utm_relevant_index=4)