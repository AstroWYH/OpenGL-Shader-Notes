### Hardware Buffer的概念

- 如果不使用Hardware Buffer，则图像在CPU上访问内存，在GPU访问显存。
- 虽然手机上只有显存的逻辑概念，并无物理概念，即显存其实用的是内存。
- 但从CPU到GPU使用glTexImage2D传输图像构建纹理，和从GPU到CPU读回图像使用glReadPixels（内含glfinish）获取buffer，是一个明显耗时的流程。
- 因此，Hardware Buffer就是手机内存里一块特殊的内存。
- 当从普通Buffer构建了Hardware Buffer后，这块内存可以被CPU和GPU共用。
- 当需要转成texture时，直接用Hardware Buffer的流程转换。
- 当需要转回buffer时，直接从那块Buffer拷贝到output。
- CPU和GPU对这块内存的修改，会直接影响这块内存，不用再经过两边的传输过程。

```cpp
// 输入为Yuv Buffer
void WriteBuffer(const void *buffer, int stride, int scanline) {
    // 对宽、高进行64位内存对齐计算，构建HardwareBuffer需要构建64位内存对齐的Buffer。
    int paddingW = (mWidth + 63) / 64 * 64;
    int paddingH = (mHeight + 63) / 64 * 64;
    LOG("WriteBuffer: paddingH: %d mHeight: %d paddingW: %d mWidth: %d stride: %d, scanline: %d",
                paddingH, mHeight, paddingW, mWidth, stride, scanline);

    if (scanline == paddingH && stride == paddingW) {
        // 如果传入的Yuv Buffer已经做了正确的内存对齐（Padding），则直接考入HardwareBuffer目标地址addr即可。
        memcpy(addr, buffer, mWidth * mHeight * 3 / 2);
    } else {
        // 否则，需要逐行进行拷贝。
        // 拷贝过程中，总共只拷贝图像实际内容的高度mHeight，每行只拷贝图像实际内容的宽度mWidth。
        // addr为paddingW*paddingH，输入图像为stride*scanline，输入图像实际内容为mWidth*mHeight。
        // 右边和下边都存在未拷贝的部分，直接保留原始内容即可，作为padding不需要关心，只用来内存对齐。
        // 先对Y的部分进行拷贝。
        for (int index = 0; index < mHeight; ++index) {
            memcpy((uint8_t *) addr + index * paddingW, (uint8_t *) buffer + index * stride, mWidth);
        }

        uint8_t *dstUV = (uint8_t *) addr + paddingW * paddingH;
        uint8_t *uv = (uint8_t *) buffer + stride * scanline;
        // 再对UV的部分进行拷贝。
        for (int index = 0; index < mHeight / 2; ++index) {
            memcpy(dstUV + index * paddingW, uv + index * stride, mWidth);
        }
    }
}
void ReadBuffer(void *buffer, void *vu_buffer, int stride, int scanline) {
    int paddingW = (mWidth + 63) / 64 * 64;
    int paddingH = (mHeight + 63) / 64 * 64;

    LOGD("ReadBuffer: paddingH: %d mHeight: %d paddingW: %d mWidth: %d stride: %d, scanline: %d",
                paddingH, mHeight, paddingW, mWidth, stride, scanline);

    // 读取过程反过来，从addr拷贝回buffer、vu_buffer
    if (scanline == mHeight && stride == mWidth) {
        memcpy(buffer, addr, mWidth * mHeight);
        memcpy(vu_buffer, addr + mWidth * mHeight, mWidth * mHeight / 2);
    } else {
        for (int index = 0; index < mHeight; ++index) {
            memcpy((uint8_t *) buffer + index * stride, (uint8_t *) addr + index * paddingW, mWidth);
        }

        uint8_t *uv = (uint8_t *) addr + paddingW * mHeight;
        for (int index = 0; index < mHeight / 2; ++index) {
            memcpy((uint8_t *)vu_buffer + index * stride, uv + index * paddingW, mWidth);
        }
    }
}
```

