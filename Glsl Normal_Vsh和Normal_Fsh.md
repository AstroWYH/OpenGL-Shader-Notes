normal_vsh、normal_fsh是基础中的基础

```glsl
// [-1,1]的范围
static const float SquareVertices[] = {
    -1.0f, -1.0f, // bottom left
    1.0f, -1.0f, // bottom right
    -1.0f,  1.0f, // top left
    1.0f,  1.0f, // top right
};

// [0,1]的范围，以左下角为原点
static const float TextureVertices[] = {
    0.0f, 0.0f, // bottom left
    1.0f, 0.0f, // bottom right
    0.0f, 1.0f, // top left
    1.0f, 1.0f, // top right
};

const char *normal_vsh = R"(#version 300 es
    layout(location = 0) in vec2 aPosition;
    layout(location = 1) in vec2 aTexCoordIn;
    out vec2 aTexCoordOut;

    void main() {
        gl_Position = vec4(aPosition, 1.0, 1.0);
	    aTexCoordOut = aTexCoordIn;
	}
)";

const char *normal_fsh = R"(#version 300 es
	#ifdef GL_FRAGMENT_PRECISION_HIGH
	precision highp float;
    #else
    precision mediump float;
    #endif
    uniform sampler2D aTexture;
    in vec2 aTexCoordOut;
    layout(location = 0) out vec4 fragColor0;

    void main() {
        fragColor0 = texture(aTexture, aTexCoordOut);
    }
)";
```

