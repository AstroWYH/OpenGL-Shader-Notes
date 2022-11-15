## 腐蚀

![image-20221111133747526](https://hanbabang-1311741789.cos.ap-chengdu.myqcloud.com/Pics/image-20221111133747526.png)

先要有原图，然后有卷积核（也叫filter、滤波器），可以设计成多种类型，本例采用这种类似“十字架”的核。

![image-20221111133956972](https://hanbabang-1311741789.cos.ap-chengdu.myqcloud.com/Pics/image-20221111133956972.png)

然后将“核”在”原图”上进行逐个卷积操作，对于”核”每次笼罩的9个像素里，我们只关心”核”为1笼罩的”原图”的4个像素（除去中心），这4个像素里，如果有”原图”出现为0的像素，那么就将”原图”中心的像素改为0，否则保持255。整个遍历完，黑的区域就会变大，就完成了腐蚀。

简单来说，你四周只要有一个黑的，那你（中心）就变成黑的。

```glsl
static const char *erode_combined_fsh = R"(
    uniform int radius; // radius*2+1为卷积核的宽度，可能为1、2、3、4...
	void main() {
    vec4 center_color = vec4(1.0);
    for(int j=-radius;j<=radius;j++){
        for(int i=-radius;i<=radius;i++){
            // 遍历当前center位置的周边9个像素（这里加上了自己）
            // 注意这里的float(i) * coordTexel.x，因为此处的aTexCoordOut.xy是0-1的范围，所以需要i/1920、j/1080等操作
            vec2 each_coord = vec2(aTexCoordOut.x + float(i) * coordTexel.x, aTexCoordOut.y + float(j) * coordTexel.y);
            vec4 each_color = texture(aTexture, each_coord);
            // 如果周边像素出现了0，则center也赋为0
            center_color = min(center_color, each_color);
        }
    }
    fragColor0 = center_color;
}
)";
```

## 膨胀

理解了腐蚀，膨胀就很简单，就是关心”核”为1笼罩的”原图”的4个像素（除去中心），有没有出现255的像素，如果有，就把”原图”中心的像素改为255。整个遍历完，白的区域就会变大，就完成了膨胀。