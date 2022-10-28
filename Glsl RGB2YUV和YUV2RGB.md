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
```

