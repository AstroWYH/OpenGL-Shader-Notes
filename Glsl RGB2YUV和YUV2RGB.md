```glsl
const lowp mat3 mat_rgb_yuv = mat3(
    0.299, -0.168736, 0.5,
    0.587, -0.331264, -0.418688,
    0.114, 0.5, -0.081312);

const lowp mat3 mat_yuv_rgb = mat3(
    1.0, 1.0, 1.0,
    0.0, -0.34413629, 1.772,
    1.402, -0.71413629, 0.0);

const lowp vec3 yuv_rgb_offset = vec3(0.0, 0.5, 0.5);

vec3 rgb2yuv(vec3 rgb) {
    vec3 yuv = mat_rgb_yuv * rgb + yuv_rgb_offset;
    return clamp(yuv, vec3(0.0), vec3(1.0));
}

vec3 yuv2rgb(vec3 yuv) {
    vec3 rgb = mat_yuv_rgb * (yuv - yuv_rgb_offset);
    return clamp(rgb, vec3(0.0), vec3(1.0));
}

Y = 0.299 R + 0.587 G + 0.114 B
U = -0.1687 R - 0.3313 G + 0.5 B + 128
V = 0.5 R - 0.4187 G - 0.0813 B + 128
R = Y + 1.402 (V-128)
G = Y - 0.34414 (U-128) - 0.71414 (V-128)
B = Y + 1.772 (U-128)
    
红色：
1.0F 0.0F 0.0F 1.0F
0.299F, 0.3313F, 1.0F, 1.0F
蓝色：
0.0F 0.0F 1.0F 1.0F
0.114F, 1.0F, 0.4187F, 1.0F
绿色：
0.0F 1.0F 0.0F 1.0F
0.587F, 0.1687F, 0.0813F, 1.0F
黄色：
1.0F 1.0F 0.0F 1.0F
0.886F, 0.0F, 0.5813F, 1.F
```

