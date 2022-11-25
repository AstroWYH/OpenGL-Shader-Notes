```glsl
static const char *TEST_FSH = R"(
            in vec2 aTexCoordOut;
            uniform sampler2D aTexture;
            layout(location = 0) out vec4 fragColor0;

            void main() {
                const int index = 0;
                // float数组在glsl中的正确写法
                float[18] arr = float[18](1.0, 1.0, 1.0, 1.0,
                                          1.0, 1.0, 1.0, 1.0,
                                          1.0, 1.0, 1.0, 1.0,
                                          1.0, 1.0, 1.0, 1.0,
                                          1.0, 1.0);

                float sum = 0.0;
                for (int i = 0; i < 18; i++) {
                    sum = sum + arr[index + i];
                }
                float avg = sum / 18.0;

                fragColor0 = texture(aTexture, aTexCoordOut) * avg;
            }
    )";
```

