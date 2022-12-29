```cpp
void yuvnv212rgb(const unsigned char *__restrict__ src, unsigned char *__restrict__ dst, int image_width, int image_height) {
    for (int i = 0; i < image_height; i++) {
        for (int j = 0; j < image_width; j++) {
            int index = i * image_width + j;
            int y = (int) src[index];
            int u = (int) src[image_height * image_width + (i / 2) * image_width + j / 2 * 2 + 1];
            int v = (int) src[image_height * image_width + (i / 2) * image_width + j / 2 * 2 + 0];

            int r = y + ((359 * (v - 128)) >> 8);
            int g = y - ((88 * (u - 128) + 183 * (v - 128)) >> 8);
            int b = y + ((454  * (u - 128)) >> 8);

            dst[index * 3 + 0] = (uint8_t)clamp(r, 0, 255);
            dst[index * 3 + 1] = (uint8_t)clamp(g, 0, 255);
            dst[index * 3 + 2] = (uint8_t)clamp(b, 0, 255);
        }
    }
}

void rgb2yuv420p(const unsigned char *__restrict__ src, unsigned char *__restrict__ dst, int image_width, int image_height, bool isNV12) {
    int y = 0, u = 0, v = 0;
    int yuv_size = image_width * image_height;
    for (int i = 0; i < image_height; i++) {
        for (int j = 0; j < image_width; j++) {
            int R = src[(i * image_width + j) * 3 + 0];
            int G = src[(i * image_width + j) * 3 + 1];
            int B = src[(i * image_width + j) * 3 + 2];

            y = ((66 * R + 129 * G + 25 * B + 128) >> 8) + 16;
            u = ((-38 * R - 74 * G + 112 * B + 128) >> 8) + 128;
            v = ((112 * R - 94 * G - 18 * B + 128) >> 8) + 128;
            y = y < 16 ? 16 : (y > 255 ? 255 : y);
            u = u < 0 ? 0 : (u > 255 ? 255 : u);
            v = v < 0 ? 0 : (v > 255 ? 255 : v);

            dst[i * image_width + j] = y;
            dst[yuv_size + (i >> 1) * image_width + (j & ~1) + 0] = isNV12 ? u : v;
            dst[yuv_size + (i >> 1) * image_width + (j & ~1) + 1] = isNV12 ? v : u;
        }
    }
}

// 对于(j & ~1)的测试验证说明：
int main()
{
	system("CHCP 65001");
	cout << ~1 << endl; // -2，11111110，怀疑是i8类型，第一个1是符号位（负），所以是-2
	int i = 5 & ~1; // 4，00000101 & 11111110 = 00000100
	cout << i << endl; // “j & ~1”目的是取j的最后一位为0，比如是j为56/57时，j & ~1都是56，给下面u/v用的
	return 0;
}

// dst[yuv_size + (i >> 1) * image_width + (j & ~1) + 0] = isNV12 ? u : v;
// dst[yuv_size + (i >> 1) * image_width + (j & ~1) + 1] = isNV12 ? v : u;
```

