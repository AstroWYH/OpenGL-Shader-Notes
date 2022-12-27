# 一、YUV数据排列格式

## 1、四种数据排列方式

### ①Planar Format

Planar的YUV格式，即平面存储格式先连续存储所有像素点的Y，紧接着存储所有像素点的U或V，最后存储剩下的U或者V。例如YU12（也叫I420），属于YUV420p，四个像素共用一组UV分量；它的数据排列方式为：
Y1Y2Y3Y4Y5Y6Y7Y8 U1U2 V1V2（如下图）

![image-20221227170529574](https://hanbabang-1311741789.cos.ap-chengdu.myqcloud.com/Pics/image-20221227170529574.png)

### ②Semi-Planar Format（NV21/NV12）

Semi-Planar的YUV格式，顾名思义，半平面存储格式，也就是先连续存储所有的Y分量，再交错存储U和V分量。例如**NV12**，属于YUV420sp，四个像素共用一组UV分量；它的数据排列方式为：
Y1Y2Y3Y4Y5Y6Y7Y8 U1V1 U2V2（如下图）
他还有个兄弟NV21，它的数据排列方式为：
Y1Y2Y3Y4Y5Y6Y7Y8 V1U1 V2U2

![image-20221227170557365](https://hanbabang-1311741789.cos.ap-chengdu.myqcloud.com/Pics/image-20221227170557365.png)

### ③Tiled Semi-Planar Format

Tiled Semi-Planar的YUV格式，Tiled SP格式不再以光栅扫描的顺序来排列数据，而是将图像以宏块划分（例如16x16像素为一个宏块），宏块划分是以从左到右、从上到下的顺序，如下图。宏块是一个正方形的像素区域，如果采样格式为YUV420sp，那么就会把这块像素区域以NV12/NV21的格式存储（这一点存疑，暂时没看到有文章具体说明），每一个宏块皆是如此，并且宏块之间的内存是连续的。

![image-20221227170638971](https://hanbabang-1311741789.cos.ap-chengdu.myqcloud.com/Pics/image-20221227170638971.png)

### ④Interleaved Format

Interleaved的YUV格式，又叫Packed Format，这种格式下YUV数据是交错存储的。举个栗子，UYVY，属于YUV422采样，水平方向每两个像素共用一组UV分量；它的数据排列方式为：
U1Y1V1Y2 U2Y3V2Y4



#### 参考链接

[(40条消息) 关于YUV格式的一些总结_dongfangxingyu1的博客-CSDN博客_p010格式](https://blog.csdn.net/u012794472/article/details/124932566?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_baidulandingword~default-1-124932566-blog-117090757.pc_relevant_recovery_v2&spm=1001.2101.3001.4242.2&utm_relevant_index=4)