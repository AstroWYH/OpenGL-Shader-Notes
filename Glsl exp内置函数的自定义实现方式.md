```glsl
static const char *Test_exp_256 = R"(
            in vec2 aTexCoordOut;
            uniform sampler2D aTexture;
            layout(location = 0) out vec4 fragColor0;

			// 相比于原始写法，这种写法其实是有一定精度丢失的，因为只近似取到256
            float exp_256(float x) {
                x = 1.0 + x / 256.0;
                x *= x; x *= x; x *= x; x *= x;
                x *= x; x *= x; x *= x; x *= x;
                return x;
            }

            void main() {
                vec4 color = texture(aTexture, aTexCoordOut);
                float weight = 0.0;
                for (float i = 0.0; i < 1000.0; i += 1.0) {
                   weight += exp_256(color.r);
                }
                fragColor0 = vec4(color.rgb, clamp(weight, 0.0, 1.0));
            }
    )";
```

