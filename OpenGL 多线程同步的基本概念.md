### **基本概念**

- Client/Server: 在OpenGL的设计模式是Client/Server，这里Client指CPU，Server指GPU。我们90%的时间是在CPU上写逻辑，而CPU操作的OpenGL对象都是Server返回的句柄。
- CommandQueue: 在CPU和GPU分别有各自的CommandQueue，当一个OpenGL命令在CPU执行时，会先添加到CPU的CommandQueue，在CPU切换到[burst mode](https://www.zhihu.com/search?q=burst mode&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"article"%2C"sourceId"%3A"490444832"})后，CPU CommandQueue中的命令被依次发送到GPU CommandQueue，GPU会在合适的时机执行CommandQueue里的命令。
- GLContext: OpenGL的[状态机](https://www.zhihu.com/search?q=状态机&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"article"%2C"sourceId"%3A"490444832"})，保存有渲染管线用到的状态和资源，OpenGL命令的宿主环境。GLContext是和线程强相关的，这是因为在绝大部分驱动的设计中，都是一个线程一个CommandQueue，而这个线程中的当前GLContext中发生的命令才会被添加到CommandQueue。
- GL fenceSync object: OpenGL的一个信号量，当GPU执行到它时会自动解锁，sync对象不属于状态机，所以可以跨context获取。
- Client Wait: 卡住CPU等待信号量解锁。
- Server Wait : 卡住GPU等待信号量解锁，CPU继续执行。

### **OpenGL命令的执行方式**

- 绝大多数OpenGL命令在CPU上是异步执行的，在GPU上是同步执行的。这一切都得益于CommandQueue的存在，让CPU可以更高效地完成命令发送，毕竟总线的资源是珍贵的，尤其在移动端的总线带宽是非常小的（内存64/128bit，显存128/256bit）。
- 但这样的代价是在渲染有问题时，在CPU无法定位到真正的现场，尤其是iOS GPU架构基于TBR/TBDR的情况下，你无法知道GPU上到底是什么时候发生问题的。

### OpenGL命令的状态

一条GPU命令在CPU执行后，会有三个状态：

- 未发送：GPU命令在CPU执行，会发送到当前CPU的CommanQueue，底层驱动会在合适的时机发送CPU CommandQueue中的命令到GPU CommandQueue。
- 发送未完成：命令在GPU的ComamndQueue中，但还没有被GPU执行。
- 完成：命令在GPU上执行了，并且不再当前渲染管线中。

### 硬件层面的指令执行

![image-20221129181433314](https://hanbabang-1311741789.cos.ap-chengdu.myqcloud.com/Pics/image-20221129181433314.png)

### **OpenGL的三种同步方案**

OpenGL中有三种可以实现同步的方案，glFinish，glFlush，glFenceSync。

1. glFinish：足够粗暴，它让当前线程的所有命令都在GPU执行完成后才返回。
2. glFlush：轻量些，它会让当前线程的所有命令都发送后才返回，此时命令在GPU的CommandQueue上，一般切换上下文要flush一下确保执行的命令顺序万无一失，但是在TBR/TBDR架构的上屏上下文中flush，会强行同步一次[tile](https://www.zhihu.com/search?q=tile&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"article"%2C"sourceId"%3A"490444832"})和FBO，所以也要尽量少用。
3. **glFenceSync**：**最灵活且轻量，他会生成一个信号量，当GPU执行到它时，信号量会解锁**，这样就可以监控GPU的运行状态了。可以选择**glClientWaitSync/glWaitSync来实现同步**。

### **如何选择**

1. 如果要同步多线程中的GPU资源，比如纹理时，只用glFlush是不够的，因为不能保证渲染命令在GPU被执行，只用glFlush会有黑屏或闪屏、Crash的问题，只能用glFinish/glFenceSync。
2. glFinish只适合不关心当前CPU和GPU执行状态的情况，比如一次提交了很多命令需要清空CommandQueue时。这在调试某一条指令时非常有用，可以在这个指令前后各加一个glFinish，来确保GPU当前只在执行这条指令，避免其他指令的干扰。
3. glFenceSync可以监控GPU的执行状态，可以用它实现关于GPU状态的回调。实现同步的方式比较灵活，可以选择是卡CPU还是GPU。比如如果需要在CPU上使用渲染结果的话，需要glClientWaitSync，如果只是为了同步执行顺序的话，glWaitSync就可以了。不过要注意的是**glFenceSync**命令后面要加一个**glFlush**, 以免产生死锁（sync信号量还没有发送，当前线程就锁住了GPU，导致GPU没有机会解锁）。

### command queue

cpu有多少个gl线程，就有多少个command queue

### gl线程

cpu上需要render的线程

#### 参考链接

[(7 封私信 / 84 条消息) glClientWaitSync - 搜索结果 - 知乎 (zhihu.com)](https://www.zhihu.com/search?type=content&q=glClientWaitSync)