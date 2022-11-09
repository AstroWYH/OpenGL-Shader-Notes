## Name

glTexImage2D — specify a two-dimensional texture image

## C Specification

| `void **glTexImage2D**(` | GLenum target,        |
| ------------------------ | --------------------- |
|                          | GLint level,          |
|                          | GLint internalformat, |
|                          | GLsizei width,        |
|                          | GLsizei height,       |
|                          | GLint border,         |
|                          | GLenum format,        |
|                          | GLenum type,          |
|                          | const void * data`)`; |

 

## Parameters

- **`target`**

  Specifies the target texture. Must be `GL_TEXTURE_2D`, `GL_PROXY_TEXTURE_2D`, `GL_TEXTURE_1D_ARRAY`, `GL_PROXY_TEXTURE_1D_ARRAY`, `GL_TEXTURE_RECTANGLE`, `GL_PROXY_TEXTURE_RECTANGLE`, `GL_TEXTURE_CUBE_MAP_POSITIVE_X`, `GL_TEXTURE_CUBE_MAP_NEGATIVE_X`, `GL_TEXTURE_CUBE_MAP_POSITIVE_Y`, `GL_TEXTURE_CUBE_MAP_NEGATIVE_Y`, `GL_TEXTURE_CUBE_MAP_POSITIVE_Z`, `GL_TEXTURE_CUBE_MAP_NEGATIVE_Z`, or `GL_PROXY_TEXTURE_CUBE_MAP`.

- **`level`**

  Specifies the level-of-detail number. Level 0 is the base image level. Level *n* is the *n*th mipmap reduction image. If *`target`* is `GL_TEXTURE_RECTANGLE` or `GL_PROXY_TEXTURE_RECTANGLE`, *`level`* must be 0.

- **`internalformat`**

  Specifies the number of color components in the texture. Must be one of base internal formats given in Table 1, one of the sized internal formats given in Table 2, or one of the compressed internal formats given in Table 3, below.

- **`width`**

  Specifies the width of the texture image. All implementations support texture images that are at least 1024 texels wide.

- **`height`**

  Specifies the height of the texture image, or the number of layers in a texture array, in the case of the `GL_TEXTURE_1D_ARRAY` and `GL_PROXY_TEXTURE_1D_ARRAY` targets. All implementations support 2D texture images that are at least 1024 texels high, and texture arrays that are at least 256 layers deep.

- **`border`**

  This value must be 0.

- **`format`**

  Specifies the format of the pixel data. The following symbolic values are accepted: `GL_RED`, `GL_RG`, `GL_RGB`, `GL_BGR`, `GL_RGBA`, `GL_BGRA`, `GL_RED_INTEGER`, `GL_RG_INTEGER`, `GL_RGB_INTEGER`, `GL_BGR_INTEGER`, `GL_RGBA_INTEGER`, `GL_BGRA_INTEGER`, `GL_STENCIL_INDEX`, `GL_DEPTH_COMPONENT`, `GL_DEPTH_STENCIL`.

- **`type`**

  Specifies the data type of the pixel data. The following symbolic values are accepted: `GL_UNSIGNED_BYTE`, `GL_BYTE`, `GL_UNSIGNED_SHORT`, `GL_SHORT`, `GL_UNSIGNED_INT`, `GL_INT`, `GL_HALF_FLOAT`, `GL_FLOAT`, `GL_UNSIGNED_BYTE_3_3_2`, `GL_UNSIGNED_BYTE_2_3_3_REV`, `GL_UNSIGNED_SHORT_5_6_5`, `GL_UNSIGNED_SHORT_5_6_5_REV`, `GL_UNSIGNED_SHORT_4_4_4_4`, `GL_UNSIGNED_SHORT_4_4_4_4_REV`, `GL_UNSIGNED_SHORT_5_5_5_1`, `GL_UNSIGNED_SHORT_1_5_5_5_REV`, `GL_UNSIGNED_INT_8_8_8_8`, `GL_UNSIGNED_INT_8_8_8_8_REV`, `GL_UNSIGNED_INT_10_10_10_2`, and `GL_UNSIGNED_INT_2_10_10_10_REV`.

- **`data`**

  Specifies a pointer to the image data in memory.

### 对于type

![image-20221103140101757](https://hanbabang-1311741789.cos.ap-chengdu.myqcloud.com/Pics/image-20221103140101757.png)

### 对于internalformat

Table 1. Base Internal Formats

| **Base Internal Format** | **RGBA, Depth and Stencil Values** | **Internal Components** |
| :----------------------- | :--------------------------------- | :---------------------- |
| `GL_DEPTH_COMPONENT`     | Depth                              | D                       |
| `GL_DEPTH_STENCIL`       | Depth, Stencil                     | D, S                    |
| `GL_RED`                 | Red                                | R                       |
| `GL_RG`                  | Red, Green                         | R, G                    |
| `GL_RGB`                 | Red, Green, Blue                   | R, G, B                 |
| `GL_RGBA`                | Red, Green, Blue, Alpha            | R, G, B, A              |

Table 2. Sized Internal Formats

| **Sized Internal Format** | **Base Internal Format** | **Red Bits** | **Green Bits** | **Blue Bits** | **Alpha Bits** | **Shared Bits** |
| :------------------------ | :----------------------- | :----------- | :------------- | :------------ | :------------- | :-------------- |
| `GL_R8`                   | `GL_RED`                 | 8            |                |               |                |                 |
| `GL_R8_SNORM`             | `GL_RED`                 | s8           |                |               |                |                 |
| `GL_R16`                  | `GL_RED`                 | 16           |                |               |                |                 |
| `GL_R16_SNORM`            | `GL_RED`                 | s16          |                |               |                |                 |
| `GL_RG8`                  | `GL_RG`                  | 8            | 8              |               |                |                 |
| `GL_RG8_SNORM`            | `GL_RG`                  | s8           | s8             |               |                |                 |
| `GL_RG16`                 | `GL_RG`                  | 16           | 16             |               |                |                 |
| `GL_RG16_SNORM`           | `GL_RG`                  | s16          | s16            |               |                |                 |
| `GL_R3_G3_B2`             | `GL_RGB`                 | 3            | 3              | 2             |                |                 |
| `GL_RGB4`                 | `GL_RGB`                 | 4            | 4              | 4             |                |                 |
| `GL_RGB5`                 | `GL_RGB`                 | 5            | 5              | 5             |                |                 |
| `GL_RGB8`                 | `GL_RGB`                 | 8            | 8              | 8             |                |                 |
| `GL_RGB8_SNORM`           | `GL_RGB`                 | s8           | s8             | s8            |                |                 |
| `GL_RGB10`                | `GL_RGB`                 | 10           | 10             | 10            |                |                 |
| `GL_RGB12`                | `GL_RGB`                 | 12           | 12             | 12            |                |                 |
| `GL_RGB16_SNORM`          | `GL_RGB`                 | 16           | 16             | 16            |                |                 |
| `GL_RGBA2`                | `GL_RGB`                 | 2            | 2              | 2             | 2              |                 |
| `GL_RGBA4`                | `GL_RGB`                 | 4            | 4              | 4             | 4              |                 |
| `GL_RGB5_A1`              | `GL_RGBA`                | 5            | 5              | 5             | 1              |                 |
| `GL_RGBA8`                | `GL_RGBA`                | 8            | 8              | 8             | 8              |                 |
| `GL_RGBA8_SNORM`          | `GL_RGBA`                | s8           | s8             | s8            | s8             |                 |
| `GL_RGB10_A2`             | `GL_RGBA`                | 10           | 10             | 10            | 2              |                 |
| `GL_RGB10_A2UI`           | `GL_RGBA`                | ui10         | ui10           | ui10          | ui2            |                 |
| `GL_RGBA12`               | `GL_RGBA`                | 12           | 12             | 12            | 12             |                 |
| `GL_RGBA16`               | `GL_RGBA`                | 16           | 16             | 16            | 16             |                 |
| `GL_SRGB8`                | `GL_RGB`                 | 8            | 8              | 8             |                |                 |
| `GL_SRGB8_ALPHA8`         | `GL_RGBA`                | 8            | 8              | 8             | 8              |                 |
| `GL_R16F`                 | `GL_RED`                 | f16          |                |               |                |                 |
| `GL_RG16F`                | `GL_RG`                  | f16          | f16            |               |                |                 |
| `GL_RGB16F`               | `GL_RGB`                 | f16          | f16            | f16           |                |                 |
| `GL_RGBA16F`              | `GL_RGBA`                | f16          | f16            | f16           | f16            |                 |
| `GL_R32F`                 | `GL_RED`                 | f32          |                |               |                |                 |
| `GL_RG32F`                | `GL_RG`                  | f32          | f32            |               |                |                 |
| `GL_RGB32F`               | `GL_RGB`                 | f32          | f32            | f32           |                |                 |
| `GL_RGBA32F`              | `GL_RGBA`                | f32          | f32            | f32           | f32            |                 |
| `GL_R11F_G11F_B10F`       | `GL_RGB`                 | f11          | f11            | f10           |                |                 |
| `GL_RGB9_E5`              | `GL_RGB`                 | 9            | 9              | 9             |                | 5               |
| `GL_R8I`                  | `GL_RED`                 | i8           |                |               |                |                 |
| `GL_R8UI`                 | `GL_RED`                 | ui8          |                |               |                |                 |
| `GL_R16I`                 | `GL_RED`                 | i16          |                |               |                |                 |
| `GL_R16UI`                | `GL_RED`                 | ui16         |                |               |                |                 |
| `GL_R32I`                 | `GL_RED`                 | i32          |                |               |                |                 |
| `GL_R32UI`                | `GL_RED`                 | ui32         |                |               |                |                 |
| `GL_RG8I`                 | `GL_RG`                  | i8           | i8             |               |                |                 |
| `GL_RG8UI`                | `GL_RG`                  | ui8          | ui8            |               |                |                 |
| `GL_RG16I`                | `GL_RG`                  | i16          | i16            |               |                |                 |
| `GL_RG16UI`               | `GL_RG`                  | ui16         | ui16           |               |                |                 |
| `GL_RG32I`                | `GL_RG`                  | i32          | i32            |               |                |                 |
| `GL_RG32UI`               | `GL_RG`                  | ui32         | ui32           |               |                |                 |
| `GL_RGB8I`                | `GL_RGB`                 | i8           | i8             | i8            |                |                 |
| `GL_RGB8UI`               | `GL_RGB`                 | ui8          | ui8            | ui8           |                |                 |
| `GL_RGB16I`               | `GL_RGB`                 | i16          | i16            | i16           |                |                 |
| `GL_RGB16UI`              | `GL_RGB`                 | ui16         | ui16           | ui16          |                |                 |
| `GL_RGB32I`               | `GL_RGB`                 | i32          | i32            | i32           |                |                 |
| `GL_RGB32UI`              | `GL_RGB`                 | ui32         | ui32           | ui32          |                |                 |
| `GL_RGBA8I`               | `GL_RGBA`                | i8           | i8             | i8            | i8             |                 |
| `GL_RGBA8UI`              | `GL_RGBA`                | ui8          | ui8            | ui8           | ui8            |                 |
| `GL_RGBA16I`              | `GL_RGBA`                | i16          | i16            | i16           | i16            |                 |
| `GL_RGBA16UI`             | `GL_RGBA`                | ui16         | ui16           | ui16          | ui16           |                 |
| `GL_RGBA32I`              | `GL_RGBA`                | i32          | i32            | i32           | i32            |                 |
| `GL_RGBA32UI`             | `GL_RGBA`                | ui32         | ui32           | ui32          | ui32           |                 |

Finally, *`internalformat`* may also be one of the generic or compressed texture formats shown in Table 3 below

Table 3. Compressed Internal Formats

| **Compressed Internal Format**          | **Base Internal Format** | **Type** |
| :-------------------------------------- | :----------------------- | :------- |
| `GL_COMPRESSED_RED`                     | `GL_RED`                 | Generic  |
| `GL_COMPRESSED_RG`                      | `GL_RG`                  | Generic  |
| `GL_COMPRESSED_RGB`                     | `GL_RGB`                 | Generic  |
| `GL_COMPRESSED_RGBA`                    | `GL_RGBA`                | Generic  |
| `GL_COMPRESSED_SRGB`                    | `GL_RGB`                 | Generic  |
| `GL_COMPRESSED_SRGB_ALPHA`              | `GL_RGBA`                | Generic  |
| `GL_COMPRESSED_RED_RGTC1`               | `GL_RED`                 | Specific |
| `GL_COMPRESSED_SIGNED_RED_RGTC1`        | `GL_RED`                 | Specific |
| `GL_COMPRESSED_RG_RGTC2`                | `GL_RG`                  | Specific |
| `GL_COMPRESSED_SIGNED_RG_RGTC2`         | `GL_RG`                  | Specific |
| `GL_COMPRESSED_RGBA_BPTC_UNORM`         | `GL_RGBA`                | Specific |
| `GL_COMPRESSED_SRGB_ALPHA_BPTC_UNORM`   | `GL_RGBA`                | Specific |
| `GL_COMPRESSED_RGB_BPTC_SIGNED_FLOAT`   | `GL_RGB`                 | Specific |
| `GL_COMPRESSED_RGB_BPTC_UNSIGNED_FLOAT` | `GL_RGB`                 | Specific |

参考链接：[glTexImage2D - OpenGL 4 Reference Pages (khronos.org)](https://registry.khronos.org/OpenGL-Refpages/gl4/html/glTexImage2D.xhtml)