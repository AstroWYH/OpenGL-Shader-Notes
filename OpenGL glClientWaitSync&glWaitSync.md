```cpp
// mediapipe source code 
// gl fence for gl mt synchronize, only support for gles 3.2 and above
GLSimpleFenceSyncPoint() : GlSimpleSyncPoint(EGL_NO_CONTEXT) {
    sync_ = glFenceSync(GL_SYNC_GPU_COMMANDS_COMPLETE, 0);
    glFlush();
}

~GLSimpleFenceSyncPoint() {
    if (sync_) {
        glDeleteSync(sync_);
        sync_ = nullptr;
    }
}

// Waits until the GPU has executed all commands up to the sync point.
// This blocks the CPU, and ensures the commands are complete from the
// point of view of all threads and contexts.
virtual void Wait() = 0;

// Ensures that the following commands on the current OpenGL context will
// not be executed until the sync point has been reached.
// This does not block the CPU, and only affects the current OpenGL context.
virtual void WaitOnGpu() { Wait(); }

void Wait() {
    if (!sync_) return;
    GLenum result =
        glClientWaitSync(sync_, 0, std::numeric_limits<uint64_t>::max());
    if (result == GL_ALREADY_SIGNALED || result == GL_CONDITION_SATISFIED) {
        glDeleteSync(sync_);
        sync_ = nullptr;
    }
}

void WaitOnGpu() {
    if (!sync_) return;
    glWaitSync(sync_, 0, GL_TIMEOUT_IGNORED);
}
```

### **OpenGL的三种同步方案**

OpenGL中有三种可以实现同步的方案，glFinish，glFlush，glFenceSync。

1. glFinish：足够粗暴，它让当前线程的所有命令都在GPU执行完成后才返回。
2. glFlush：轻量些，它会让当前线程的所有命令都发送后才返回，此时命令在GPU的CommandQueue上，一般切换上下文要flush一下确保执行的命令顺序万无一失，但是在TBR/TBDR架构的上屏上下文中flush，会强行同步一次[tile](https://www.zhihu.com/search?q=tile&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"article"%2C"sourceId"%3A"490444832"})和FBO，所以也要尽量少用。
3. **glFenceSync**：**最灵活且轻量，他会生成一个信号量，当GPU执行到它时，信号量会解锁**，这样就可以监控GPU的运行状态了。可以选择**glClientWaitSync/glWaitSync来实现同步**。

### **如何选择**

1. 如果要同步多线程中的GPU资源，比如纹理时，只用glFlush是不够的，因为不能保证渲染命令在GPU被执行，只用glFlush会有黑屏或闪屏、Crash的问题，只能用glFinish/glFenceSync。
2. glFinish只适合不关心当前CPU和GPU执行状态的情况，比如一次提交了很多命令需要清空CommandQueue时。这在调试某一条指令时非常有用，可以在这个指令前后各加一个glFinish，来确保GPU当前只在执行这条指令，避免其他指令的干扰。
3. glFenceSync可以监控GPU的执行状态，可以用它实现关于GPU状态的回调。实现同步的方式比较灵活，可以选择是卡CPU还是GPU。比如如果需要在CPU上使用渲染结果的话，需要glClientWaitSync，如果只是为了同步执行顺序的话，glWaitSync就可以了。不过要注意的是**glFenceSync**命令后面要加一个**glFlush**, 以免产生死锁（sync信号量还没有发送，当前线程就锁住了GPU，导致GPU没有机会解锁）。