```glsl
// 注：该fsh的gles版本、精度，都可作为header提前提取出来。
// 对于多输入多输出纹理，在纹理创建时的精度（如8UC1/16FC1/32FC1）在shader里，是一视同仁的。而shader内部的mediump、highp是有区别的。
// 对于多输入多输出纹理的通道数，无论是8UC1/8UC2/8UC3/8UC4，都可直接作为4个通道传入，下面会展示各自的处理情况。

static const char *Test_Common_Header = "#version 300 es\n";
static const char *Test_Precision_Header = R"(
#ifdef HIGH_PRECISION
    precision highp float;
#elif defined(MEDIUM_PRECISION)
    precision mediump float;
#elif defined(LOW_PRECISION)
    precision lowp float;
#else
    #ifdef GL_FRAGMENT_PRECISION_HIGH
    precision highp float;
    #else
    precision mediump float;
    #endif
#endif
    )";

// Group A // 多输入多输出纹理，输入4*C1输出4*C1
static const char *Test_multi_in_4_c1_out_4_c1 = R"(
            in vec2 aTexCoordOut;
            uniform sampler2D aTexture;
            uniform sampler2D aTexture1;
            uniform sampler2D aTexture2;
            uniform sampler2D aTexture3;
            layout(location = 0) out vec4 fragColor0;
            layout(location = 1) out vec4 fragColor1;
            layout(location = 2) out vec4 fragColor2;
            layout(location = 3) out vec4 fragColor3;

            void main() {
                fragColor0 = texture(aTexture, aTexCoordOut);
                fragColor1 = texture(aTexture1, aTexCoordOut);
                fragColor2 = texture(aTexture2, aTexCoordOut);
                fragColor3 = texture(aTexture3, aTexCoordOut);
            }
    )";

// Group B // 多输入多输出纹理，输入1*C4输出4*C1
static const char *Test_multi_in_1_c4_out_4_c1 = R"(
            in vec2 aTexCoordOut;
            uniform sampler2D aTexture;
            layout(location = 0) out vec4 fragColor0;
            layout(location = 1) out vec4 fragColor1;
            layout(location = 2) out vec4 fragColor2;
            layout(location = 3) out vec4 fragColor3;

            void main() {
                float r = texture(aTexture, aTexCoordOut).r;
                float g = texture(aTexture, aTexCoordOut).g;
                float b = texture(aTexture, aTexCoordOut).b;
                float a = texture(aTexture, aTexCoordOut).a;
                fragColor0 = vec4(r, r, r, 1);
                fragColor1 = vec4(g, g, g, 1);
                fragColor2 = vec4(b, b, b, 1);
                fragColor3 = vec4(a, a, a, 1);
            }
    )";

// Group C // 多输入多输出纹理，输入4*C1输出1*C4
static const char *Test_multi_in_4_c1_out_1_c4 = R"(
            in vec2 aTexCoordOut;
            uniform sampler2D aTexture;
            uniform sampler2D aTexture1;
            uniform sampler2D aTexture2;
            uniform sampler2D aTexture3;
            layout(location = 0) out vec4 fragColor0;

            // 经测试，对于4个单通道输入（前提是通过GL_R8这样的形式传入，相当于GBA被裁剪了），读入shader后只保留了r，gb为0，a为1。
			// 如果是通过GL_ALPHA这种形式传入，则shader内读取应该只读.a，rgb是无值的。
            void main() {
                float in1 = texture(aTexture, aTexCoordOut).r;
                float in2 = texture(aTexture1, aTexCoordOut).r;
                float in3 = texture(aTexture2, aTexCoordOut).r;
                float in4 = texture(aTexture3, aTexCoordOut).r;
                fragColor0 = vec4(in1, in2, in3, in4);
            }
    )";

// Group D // 多输入多输出纹理，输入1*C4输出1*C4
static const char *Test_multi_in_1_c4_out_1_c4 = R"(
            in vec2 aTexCoordOut;
            uniform sampler2D aTexture;
            layout(location = 0) out vec4 fragColor0;

            void main() {
                fragColor0 = texture(aTexture, aTexCoordOut);
            }
    )";

// Group E // 多输入多输出纹理，输入2*C2输出4*C1
static const char *Test_multi_in_2_c2_out_4_c1 = R"(
            in vec2 aTexCoordOut;
            uniform sampler2D aTexture;
            uniform sampler2D aTexture1;
            layout(location = 0) out vec4 fragColor0;
            layout(location = 1) out vec4 fragColor1;
            layout(location = 2) out vec4 fragColor2;
            layout(location = 3) out vec4 fragColor3;

            void main() {
                vec2 rg1 = texture(aTexture, aTexCoordOut).rg;
                vec2 rg2 = texture(aTexture1, aTexCoordOut).rg;
                fragColor0 = vec4(rg1.r, rg1.r, rg1.r, 1);
                fragColor1 = vec4(rg1.g, rg1.g, rg1.g, 1);
                fragColor2 = vec4(rg2.r, rg2.r, rg2.r, 1);
                fragColor3 = vec4(rg2.g, rg2.g, rg2.g, 1);
            }
    )";

// Group F // 多输入多输出纹理，输入2*C2输出1*C4
static const char *Test_multi_in_2_c2_out_1_c4 = R"(
            in vec2 aTexCoordOut;
            uniform sampler2D aTexture;
            uniform sampler2D aTexture1;
            layout(location = 0) out vec4 fragColor0;

            void main() {
                vec2 rg1 = texture(aTexture, aTexCoordOut).rg;
                vec2 rg2 = texture(aTexture1, aTexCoordOut).rg;
                fragColor0 = vec4(rg1, rg2);
            }
    )";

// Group G // 多输入多输出纹理，输入8*C1输出2*C4
static const char *Test_multi_in_8_c1_out_2_c4 = R"(
            in vec2 aTexCoordOut;
            uniform sampler2D aTexture;
            uniform sampler2D aTexture1;
            uniform sampler2D aTexture2;
            uniform sampler2D aTexture3;
            uniform sampler2D aTexture4;
            uniform sampler2D aTexture5;
            uniform sampler2D aTexture6;
            uniform sampler2D aTexture7;
            layout(location = 0) out vec4 fragColor0;
            layout(location = 1) out vec4 fragColor1;

            void main() {
                float r0 = texture(aTexture, aTexCoordOut).r;
                float r1 = texture(aTexture1, aTexCoordOut).r;
                float r2 = texture(aTexture2, aTexCoordOut).r;
                float r3 = texture(aTexture3, aTexCoordOut).r;
                float r4 = texture(aTexture4, aTexCoordOut).r;
                float r5 = texture(aTexture5, aTexCoordOut).r;
                float r6 = texture(aTexture6, aTexCoordOut).r;
                float r7 = texture(aTexture7, aTexCoordOut).r;
                fragColor0 = vec4(r0, r1, r2, r3);
                fragColor1 = vec4(r4, r5, r6, r7);
            }
    )";
```

