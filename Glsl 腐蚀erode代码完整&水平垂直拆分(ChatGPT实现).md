```glsl
以下是一个简单的GLSL程序，用于实现腐蚀操作（erode）。这个程序将在输入图像中搜索像素，并将输出像素的值设置为周围像素中最小的值。
uniform sampler2D texture;
uniform vec2 texelSize;
varying vec2 vUv;

void main() {
  vec4 color = texture2D(texture, vUv);
  vec4 result = vec4(1.0);

  // 3x3 kernel for erosion
  for (int i = -1; i <= 1; i++) {
    for (int j = -1; j <= 1; j++) {
      vec4 neighbor = texture2D(texture, vUv + vec2(float(i) * texelSize.x, float(j) * texelSize.y));
      result = min(result, neighbor);
    }
  }

  gl_FragColor = vec4(result.rgb, color.a);
}
这个程序使用一个3x3的内核来搜索周围的像素。在每个像素位置，它会遍历内核中的所有像素，并将结果设置为周围像素的最小值。在进行纹理采样时，程序还使用了一个纹理像素大小的向量来计算周围像素的位置。最后，程序将输出像素的颜色值设置为最小值，并保留输入像素的alpha值。注意，这个程序需要一个2D纹理和一个纹理像素大小的uniform变量作为输入。
///////////////////////////////////////////////////////////////////////////
以下是一个将腐蚀操作拆分为水平和垂直方向的两个GLSL程序。第一个程序将输入图像水平腐蚀，第二个程序将输入图像垂直腐蚀。这两个程序可以组合使用，以实现与原始程序相同的效果。
uniform sampler2D texture;
uniform vec2 texelSize;
varying vec2 vUv;

void main() {
  vec4 color = texture2D(texture, vUv);
  vec4 result = vec4(1.0);

  // 3x1 kernel for horizontal erosion
  for (int i = -1; i <= 1; i++) {
    vec4 neighbor = texture2D(texture, vUv + vec2(float(i) * texelSize.x, 0.0));
    result = min(result, neighbor);
  }

  gl_FragColor = vec4(result.rgb, color.a);
}
这个程序使用一个3x1的内核来水平搜索周围的像素。在每个像素位置，它将遍历内核中的所有像素，并将结果设置为周围像素的最小值。注意，这个程序需要一个2D纹理和一个纹理像素大小的uniform变量作为输入。

垂直腐蚀
uniform sampler2D texture;
uniform vec2 texelSize;
varying vec2 vUv;

void main() {
  vec4 color = texture2D(texture, vUv);
  vec4 result = vec4(1.0);

  // 1x3 kernel for vertical erosion
  for (int j = -1; j <= 1; j++) {
    vec4 neighbor = texture2D(texture, vUv + vec2(0.0, float(j) * texelSize.y));
    result = min(result, neighbor);
  }

  gl_FragColor = vec4(result.rgb, color.a);
}
这个程序使用一个1x3的内核来垂直搜索周围的像素。在每个像素位置，它将遍历内核中的所有像素，并将结果设置为周围像素的最小值。注意，这个程序需要一个2D纹理和一个纹理像素大小的uniform变量作为输入。
```

