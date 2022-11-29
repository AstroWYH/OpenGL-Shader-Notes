### cpu到gpu之间的通讯过程

c层写入gl的一些gpu的操作时，这些指令会在驱动层，驱动层会将系统从protect模式切换到unprotect模式，从而将指令传输给gpu，gpu来执行。但切换操作系统模式会很消耗性能，因此，driver层开辟一个buffer，用来存储gl的命令，等存储到一定后一起送往gpu，以来提高性能，如下图：

![image-20221129182125084](https://hanbabang-1311741789.cos.ap-chengdu.myqcloud.com/Pics/image-20221129182125084.png)

### glflush操作

```cpp
Name
glFlush — force execution of GL commands in finite time

C Specification
void glFlush();

Description
Different GL implementations buffer commands in several different locations, including network buffers and the graphics accelerator itself. glFlush empties all of these buffers, causing all issued commands to be executed as quickly as they are accepted by the actual rendering engine. Though this execution may not be completed in any particular time period, it does complete in finite time.

Because any GL program might be executed over a network, or on an accelerator that buffers commands, all programs should call glFlush whenever they count on having all of their previously issued commands completed. For example, call glFlush before waiting for user input that depends on the generated image.

Notes
glFlush can return at any time. It does not wait until the execution of all previously issued GL commands is complete.

Errors
GL_INVALID_OPERATION is generated if glFlush is executed between the execution of glBegin and the corresponding execution of glEnd.

glFlush()清空缓冲区，将指令送往缓硬件立即执行，但是它是将命令传送完毕之后立即返回，不会等待指令执行完毕。这些指令会在有限时间内执行完毕。
如果直接绘制到前缓冲，那么OpenGL的绘制将不会有任何延迟。设想有一个复杂的场景，有很多物体需要绘制。当调用glFlush时，物体会一个一个地出现在屏幕上。但是，如果使用双缓冲，这个函数将不会有什么影响，因为直到交换缓冲区的时候变化才显现出来。
```

glflush操作是将 driver buffer种的gl指令集强行送入gpu，在这之前，处于堵塞状态，送入gpu后，就会返回结果值。

### glfinish操作

```cpp
Name
glFinish — block until all GL execution is complete

C Specification
void glFinish(void);

Description
glFinish does not return until the effects of all previously called GL commands are complete. Such effects include all changes to GL state, all changes to connection state, and all changes to the frame buffer contents.

Notes
glFinish requires a round trip to the server.

Errors
GL_INVALID_OPERATION is generated if glFinish is executed between the execution of glBegin and the corresponding execution of glEnd.

glFinish()将缓冲区的指令立即送往硬件执行，但是要一直等到硬件执行完这些指令之后才返回。
如果直接绘制到前缓冲，那么在你想保存屏幕截图之前，就需要调用这个函数，确保绘制完毕。
如果使用双缓冲，则这个函数不会有太大作用。
```

glfinish操作是将driver buffer的数据全部送入gpu，并且gpu执行完毕所以指令后返回值，在这之前会堵塞。

### glFinish和glFlush的区别

**glFinish会造成性能下降**

- 如果调用glFinish，通常会带来性能上的损失。因为它会是的GPU和CPU之间的并行性丧失。
- 一般，我们提交给驱动的任务被分组，然后被送到硬件上（在缓冲区交换的时候）。如果调用glFinish，就强制驱动将命令送到GPU。然后CPU等待直到被传送的命令全部执行完毕。这样在GPU工作的整个期间内，CPU没有工作（至少在这个线程上）。而在CPU工作时（通常是在对命令分组），GPU没有工作。因此造成性能上的下降。
- 因此，应该尽量减少使用此函数。此函数的一个应用是：调试bug。如果我传输到硬件的某条命令造成了GPU的崩溃，找出使得GPU崩溃的那条指令的简单方法是在每个绘制操作之后调用这个函数。这样就可以准确找出造成崩溃的命令。

#### 参考链接

[glFlush 和 glFinish的区别_Lammyzp的博客-CSDN博客_glfinish](https://blog.csdn.net/zhangpengzp/article/details/88836542?ops_request_misc=%7B%22request%5Fid%22%3A%22166970397416782395344178%22%2C%22scm%22%3A%2220140713.130102334.pc%5Fall.%22%7D&request_id=166970397416782395344178&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~hot_rank-4-88836542-null-null.142^v67^wechat_v2,201^v3^control_2,213^v2^t3_control2&utm_term=glfinish&spm=1018.2226.3001.4187)

[glFinish()和glFlush()函数详解_谢灵枢的博客-CSDN博客_glfinish glflush](https://blog.csdn.net/XB554790401/article/details/38704493?ops_request_misc=%7B%22request%5Fid%22%3A%22166970397416782395344178%22%2C%22scm%22%3A%2220140713.130102334.pc%5Fall.%22%7D&request_id=166970397416782395344178&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~hot_rank-2-38704493-null-null.142^v67^wechat_v2,201^v3^control_2,213^v2^t3_control2&utm_term=glfinish&spm=1018.2226.3001.4187)