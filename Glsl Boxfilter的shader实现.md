最简单的滤波方式之一 ：3*3的boxfilter，某次卷积过程中，将原图每个元素和boxfilter里每个值相乘，然后叠加，再求平均值。

```glsl
static const char *box_combined_fsh = R"(
    uniform int radius; // radius*2+1为卷积核的宽度，可能为1、2、3、4...
	void main() {
    vec4 sum = vec4(0.0, 0.0, 0.0, 0.0);
    mediump int total = 0;
    for(int j=-radius;j<=radius;j++){
        for(int i=-radius;i<=radius;i++){
            // 对于center周边的8个元素，和boxfilter做卷积求平均
            vec2 each_coord= vec2(aTexCoordOut.x + float(i) * coordTexel.x, aTexCoordOut.y + float(j) * coordTexel.y);
            vec4 center_color = texture(aTexture, each_coord);
            total = total + 1;
            sum = sum + (center_color - sum)/float(total);
        }
    }
    // 这是另一种写法，和上面其实一样
    // highp int total = radius + radius + 1;
    // total = total * total;
    // fragColor0 = sum/float(total);
    fragColor0 = sum;
}
)";
```

