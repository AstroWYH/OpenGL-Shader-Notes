| 内置函数                                                     | 解释                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| float radians (float degrees)                                | 将degrees转换为弧度并返回结果，result=PI/180*degrees         |
| vec2 radians (vec2 degrees)                                  |                                                              |
| vec3 radians (vec3 degrees)                                  |                                                              |
| vec4 radians (vec4 degrees)                                  |                                                              |
| float degrees (float radians)                                | 将radians转换为角度并返回结果，result=180/PI*radians         |
| vec2 degrees (vec2 radians)                                  |                                                              |
| vec3 degrees (vec3 radians)                                  |                                                              |
| vec4 degrees (vec4 radians)                                  |                                                              |
| float sin (float radians)                                    | 标准三角函数                                                 |
| vec2 sin (vec2 radians)                                      |                                                              |
| vec3 sin (vec3 radians)                                      |                                                              |
| vec4 sin (vec4 radians)                                      |                                                              |
| float cos (float radians)                                    |                                                              |
| vec2 cos (vec2 radians)                                      |                                                              |
| vec3 cos (vec3 radians)                                      |                                                              |
| vec4 cos (vec4 radians)                                      |                                                              |
| float tan (float radians)                                    |                                                              |
| vec2 tan (vec2 radians)                                      |                                                              |
| vec3 tan (vec3 radians)                                      |                                                              |
| vec4 tan (vec4 radians)                                      |                                                              |
| float asin (float x)                                         |                                                              |
| vec2 asin (vec2 x)                                           |                                                              |
| vec3 asin (vec3 x)                                           |                                                              |
| vec4 asin (vec4 x)                                           |                                                              |
| float acos (float x)                                         |                                                              |
| vec2 acos (vec2 x)                                           |                                                              |
| vec3 acos (vec3 x)                                           |                                                              |
| vec4 acos (vec4 x)                                           |                                                              |
| float atan (float y, float x)                                |                                                              |
| vec2 atan (vec2 y, vec2 x)                                   |                                                              |
| vec3 atan (vec3 y, vec3 x)                                   |                                                              |
| vec4 atan (vec4 y, vec4 x)                                   |                                                              |
| float atan (float y_over_x)                                  |                                                              |
| vec2 atan (vec2 y_over_x)                                    |                                                              |
| vec3 atan (vec3 y_over_x)                                    |                                                              |
| vec4 atan (vec4 y_over_x)                                    |                                                              |
| float sinh (float x)                                         |                                                              |
| vec2 sinh (vec2 x)                                           |                                                              |
| vec3 sinh (vec3 x)                                           |                                                              |
| vec4 sinh (vec4 x)                                           |                                                              |
| float cosh (float x)                                         |                                                              |
| vec2 cosh (vec2 x)                                           |                                                              |
| vec3 cosh (vec3 x)                                           |                                                              |
| vec4 cosh (vec4 x)                                           |                                                              |
| float tanh (float x)                                         |                                                              |
| vec2 tanh (vec2 x)                                           |                                                              |
| vec3 tanh (vec3 x)                                           |                                                              |
| vec4 tanh (vec4 x)                                           |                                                              |
| float asinh (float x)                                        |                                                              |
| vec2 asinh (vec2 x)                                          |                                                              |
| vec3 asinh (vec3 x)                                          |                                                              |
| vec4 asinh (vec4 x)                                          |                                                              |
| float acosh (float x)                                        |                                                              |
| vec2 acosh (vec2 x)                                          |                                                              |
| vec3 acosh (vec3 x)                                          |                                                              |
| vec4 acosh (vec4 x)                                          |                                                              |
| float atanh (float x)                                        |                                                              |
| vec2 atanh (vec2 x)                                          |                                                              |
| vec3 atanh (vec3 x)                                          |                                                              |
| vec4 atanh (vec4 x)                                          |                                                              |
| float pow (float x, float y)                                 | 返回x的y次方，x^y                                            |
| vec2 pow (vec2 x, vec2 y)                                    |                                                              |
| vec3 pow (vec3 x, vec3 y)                                    |                                                              |
| vec4 pow (vec4 x, vec4 y)                                    |                                                              |
| float exp (float x)                                          | 返回e的x次方                                                 |
| vec2 exp (vec2 x)                                            |                                                              |
| vec3 exp (vec3 x)                                            |                                                              |
| vec4 exp (vec4 x)                                            |                                                              |
| float exp2 (float x)                                         | 返回2的x次方                                                 |
| vec2 exp2 (vec2 x)                                           |                                                              |
| vec3 exp2 (vec3 x)                                           |                                                              |
| vec4 exp2 (vec4 x)                                           |                                                              |
| float log (float x)                                          | 以e为底                                                      |
| vec2 log (vec2 x)                                            |                                                              |
| vec3 log (vec3 x)                                            |                                                              |
| vec4 log (vec4 x)                                            |                                                              |
| float log2 (float x)                                         | 以2为                                                        |
| vec2 log2 (vec2 x)                                           |                                                              |
| vec3 log2 (vec3 x)                                           |                                                              |
| vec4 log2 (vec4 x)                                           |                                                              |
| float sqrt (float x)                                         | 开根号，x<0时未定义                                          |
| vec2 sqrt (vec2 x)                                           |                                                              |
| vec3 sqrt (vec3 x)                                           |                                                              |
| vec4 sqrt (vec4 x)                                           |                                                              |
| float inversesqrt (float x)                                  | X正平方根的倒数                                              |
| vec2 inversesqrt (vec2 x)                                    |                                                              |
| vec3 inversesqrt (vec3 x)                                    |                                                              |
| vec4 inversesqrt (vec4 x)                                    |                                                              |
| float abs (float x)                                          | 返回x的绝对值                                                |
| vec2 abs (vec2 x)                                            |                                                              |
| vec3 abs (vec3 x)                                            |                                                              |
| vec4 abs (vec4 x)                                            |                                                              |
| int abs (int x)                                              |                                                              |
| ivec2 abs (ivec2 x)                                          |                                                              |
| ivec3 abs (ivec3 x)                                          |                                                              |
| ivec4 abs (ivec4 x)                                          |                                                              |
| float sign (float x)                                         | Returns 1.0 if x > 0, 0.0 if x = 0,or –1.0 if x < 0.         |
| vec2 sign (vec2 x)                                           |                                                              |
| vec3 sign (vec3 x)                                           |                                                              |
| vec4 sign (vec4 x)                                           |                                                              |
| int sign (int x)                                             |                                                              |
| ivec2 sign (ivec2 x)                                         |                                                              |
| ivec3 sign (ivec3 x)                                         |                                                              |
| ivec4 sign (ivec4 x)                                         |                                                              |
| float floor (float x)                                        | 向下取整                                                     |
| vec2 floor (vec2 x)                                          |                                                              |
| vec3 floor (vec3 x)                                          |                                                              |
| vec4 floor (vec4 x)                                          |                                                              |
| float ceil (float x)                                         | 向上取整                                                     |
| vec2 ceil (vec2 x)                                           |                                                              |
| vec3 ceil (vec3 x)                                           |                                                              |
| vec4 ceil (vec4 x)                                           |                                                              |
| float trunc (float x)                                        | 返回绝对值最接近x的                                          |
| vec2 trunc (vec2 x)                                          |                                                              |
| vec3 trunc (vec3 x)                                          |                                                              |
| vec4 trunc (vec4 x)                                          |                                                              |
| float round (float x)                                        | 返回最接近x的值                                              |
| vec2 round (vec2 x)                                          |                                                              |
| vec3 round (vec3 x)                                          |                                                              |
| vec4 round (vec4 x)                                          |                                                              |
| float fract (float x)                                        | 返回x-floor(x)                                               |
| vec2 fract (vec2 x)                                          |                                                              |
| vec3 fract (vec3 x)                                          |                                                              |
| vec4 fract (vec4 x)                                          |                                                              |
| float mod (float x, float y)                                 | 取模，返回x-y*floor(x/y);                                    |
| vec2 mod (vec2 x, float y)                                   |                                                              |
| vec3 mod (vec3 x, float y)                                   |                                                              |
| vec4 mod (vec4 x, float y)                                   |                                                              |
|                                                              |                                                              |
| vec2 mod (vec2 x, vec2 y)                                    |                                                              |
| vec3 mod (vec3 x, vec3 y)                                    |                                                              |
| vec4 mod (vec4 x, vec4 y)                                    |                                                              |
| float min (float x, float y)                                 | 返回较小值                                                   |
| vec2 min (vec2 x, vec2 y)                                    |                                                              |
| vec3 min (vec3 x, vec3 y)                                    |                                                              |
| vec4 min (vec4 x, vec4 y)                                    |                                                              |
| int min (int x, int y)                                       |                                                              |
| ivec2 min (ivec2 x, ivec2 y)                                 |                                                              |
| ivec3 min (ivec3 x, ivec3 y)                                 |                                                              |
| ivec4 min (ivec4 x, ivec4 y)                                 |                                                              |
| uint min (uint x, uint y)                                    |                                                              |
| uvec2 min (uvec2 x, uvec2 y)                                 |                                                              |
| uvec3 min (uvec3 x, uvec3 y)                                 |                                                              |
| uvec4 min (uvec4 x, uvec4 y                                  |                                                              |
| ) vec2 min (vec2 x, float y)                                 |                                                              |
| vec3 min (vec3 x, float y)                                   |                                                              |
| vec4 min (vec4 x, float y)                                   |                                                              |
| ivec2 min (ivec2 x, int y)                                   |                                                              |
| ivec3 min (ivec3 x, int y)                                   |                                                              |
| ivec4 min (ivec4 x, int y)                                   |                                                              |
| uvec2 min (uvec2 x, uint y)                                  |                                                              |
| uvec3 min (uvec3 x, uint y)                                  |                                                              |
| uvec4 min (uvec4 x, uint y)                                  |                                                              |
| float max (float x, float y)                                 | 返回较大值                                                   |
| vec2 max (vec2 x, vec2 y)                                    |                                                              |
| vec3 max (vec3 x, vec3 y)                                    |                                                              |
| vec4 max (vec4 x, vec4 y)                                    |                                                              |
| int max (int x, int y)                                       |                                                              |
| ivec2 max (ivec2 x, ivec2 y)                                 |                                                              |
| ivec3 max (ivec3 x, ivec3 y)                                 |                                                              |
| ivec4 max (ivec4 x, ivec4 y)                                 |                                                              |
| uint max (uint x, uint y)                                    |                                                              |
| uvec2 max (uvec2 x, uvec2 y)                                 |                                                              |
| uvec3 max (uvec3 x, uvec3 y)                                 |                                                              |
| uvec4 max (uvec4 x, uvec4 y)                                 |                                                              |
| vec2 max (vec2 x, float y)                                   |                                                              |
| vec3 max (vec3 x, float y)                                   |                                                              |
| vec4 max (vec4 x, float y)                                   |                                                              |
| ivec2 max (ivec2 x, int y)                                   |                                                              |
| ivec3 max (ivec3 x, int y)                                   |                                                              |
| ivec4 max (ivec4 x, int y)                                   |                                                              |
| uvec2 max (uvec2 x, uint y)                                  |                                                              |
| uvec3 max (uvec3 x, uint y)                                  |                                                              |
| uvec4 max (uvec4 x, uint y)                                  |                                                              |
| float clamp (float x, float minVal,float maxVal)             |                                                              |
| vec2 clamp (vec2 x, float minVal,float maxVal)               |                                                              |
| vec3 clamp (vec3 x, float minVal,float maxVal)               |                                                              |
| vec4 clamp (vec4 x, float minVal,float maxVal)               |                                                              |
| int clamp (int x, int minVal,int maxVal)                     |                                                              |
| ivec2 clamp (ivec2 x, int minVal,int maxVal)                 |                                                              |
| ivec3 clamp (ivec3 x, int minVal,int maxVal)                 |                                                              |
| ivec4 clamp (ivec4 x, int minVal,int maxVal)                 | Returns the component-wise result of min (max (x, minVal ), maxVal).Results are undefined if minVal >maxVal. |
| uint clamp (uint x, uint minVal,uint maxVal)                 |                                                              |
| uvec2 clamp (uvec2 x, uint minVal,uint maxVal)               |                                                              |
| uvec3 clamp (uvec3 x, uint minVal,uint maxVal)               |                                                              |
| uvec4 clamp (uvec4 x, uint minVal,uint maxVal)               |                                                              |
| vec2 clamp (vec2 x, vec2 minVal,vec2 maxVal)                 |                                                              |
| vec3 clamp (vec3 x, vec3 minVal,vec3 maxVal)                 |                                                              |
| vec4 clamp (vec4 x, vec4 minVal,vec4 maxVal)                 |                                                              |
| ivec2 clamp (ivec2 x, ivec2 minVal,ivec2 maxVal)             |                                                              |
| ivec3 clamp (ivec3 x, ivec3 minVal,ivec3 maxVal)             |                                                              |
| ivec4 clamp (ivec4 x, ivec4 minVal,ivec4 maxVal)             |                                                              |
| uvec2 clamp (uvec2 x, uvec2 minVal,uvec2 maxVal)             |                                                              |
| uvec3 clamp (uvec3 x, uvec3 minVal,uvec3 maxVal)             |                                                              |
| uvec4 clamp (uvec4 x, uvec4 minVal,uvec4 maxVal)             |                                                              |
| float mix (float x, float y, float a)                        | 返回 x*(1.0 – a) + y*a                                       |
| vec2 mix (vec2 x, vec2 y, float a)                           |                                                              |
| vec3 mix (vec3 x, vec3 y, float a)                           |                                                              |
| vec4 mix (vec4 x, vec4 y, float a)                           |                                                              |
| vec2 mix (vec2 x, vec2 y, vec2 a)                            |                                                              |
| vec3 mix (vec3 x, vec3 y, vec3 a)                            |                                                              |
| vec4 mix (vec4 x, vec4 y, vec4 a)                            |                                                              |
| float step (float edge, float x)                             | Returns 0.0 if x < edge; otherwise, it returns 1.0.          |
| vec2 step (vec2 edge, vec2 x)                                |                                                              |
| vec3 step (vec3 edge, vec3 x)                                |                                                              |
| vec4 step (vec4 edge, vec4 x)                                |                                                              |
| float step (float edge, float x)                             |                                                              |
| vec2 step (float edge, vec2 x)                               |                                                              |
| vec3 step (float edge, vec3 x)                               |                                                              |
| vec4 step (float edge, vec4 x)                               |                                                              |
| float smoothstep (float edge0,float edge1, float x)          | Returns 0.0 if x <= edge0 and 1.0 if x>= edge1 and performs smooth Hermite interpolation between 0.0 and 1.0 when edge0 < x < edge1. |
| vec2 smoothstep (vec2 edge0,vec2 edge1, vec2 x)              |                                                              |
| vec3 smoothstep (vec3 edge0,vec3 edge1, vec3 x)              |                                                              |
| vec4 smoothstep (vec4 edge0,vec4 edge1, vec4 x)              |                                                              |
| vec2 smoothstep (float edge0,float edge1, vec2 x)            |                                                              |
| vec3 smoothstep (float edge0,float edge1, vec3 x)            |                                                              |
| vec4 smoothstep (float edge0,float edge1, vec4 x)            |                                                              |
| bool isnan (float x)                                         | Results are undefined if edge0 >=edge1.                      |
| bvec2 isnan (vec2 x)                                         |                                                              |
| bvec3 isnan (vec3 x)                                         |                                                              |
| bvec4 isnan (vec4 x)                                         |                                                              |
| bool isinf (float x)                                         | Returns true if x holds a NaN (not a number), false otherwise.(Implementations that do not support NaN always return false.) |
| bvec2 isinf (vec2 x)                                         |                                                              |
| bvec3 isinf (vec3 x)                                         |                                                              |
| bvec4 isinf (vec4 x)                                         |                                                              |
| 几何函数                                                     |                                                              |
| float length (float x)                                       | 返回矢量x的长度=Sqrt(x[0]*x[0]+x[1]*x[1]…)                   |
| float length (vec2 x)                                        |                                                              |
| float length (vec3 x)                                        |                                                              |
| float length (vec4 x)                                        |                                                              |
| float distance (float p0, float p1)                          | 返回p0,p1之间的距离Length(p0-p1)                             |
| float distance (vec2 p0, vec2 p1)                            |                                                              |
| float distance (vec3 p0, vec3 p1)                            |                                                              |
| float distance (vec4 p0, vec4 p1)                            |                                                              |
| float dot (float x, float y)                                 | 返回x和y的点积result = x[0] *y[0] + x[1] *y[1] + ....        |
| float dot (vec2 x, vec2 y)                                   |                                                              |
| float dot (vec3 x, vec3 y)                                   |                                                              |
| float dot (vec4 x, vec4 y)                                   |                                                              |
| vec3 cross (vec3 x, vec3 y)                                  | result[0] = x[1] *y[2] - y[1] *x[2] result[1] = x[2] *y[0] - y[2] *x[0] result[2] = x[0] *y[1] - y[0] *x[1] |
| float normalize (float x)                                    | 返回单位向量                                                 |
| vec2 normalize (vec2 x)                                      |                                                              |
| vec3 normalize (vec3 x)                                      |                                                              |
| vec4 normalize (vec4 x)                                      |                                                              |
| float faceforward (float N, float I,float Nref)              | 如果dot(Nref,I)<0.0,返回N，否者返回-N                        |
| vec2 faceforward (vec2 N, vec2 I,vec2 Nref)                  |                                                              |
| vec3 faceforward (vec3 N, vec3 I,vec3 Nref)                  |                                                              |
| vec4 faceforward (vec4 N, vec4 I,vec4 Nref)                  |                                                              |
| float reflect (float I, float N)                             | 对于关联矢量I和表面方位N，返回反射方向：result=I-2.0*dot(N,I)*N 因该规范化N |
| vec2 reflect (vec2 I, vec2 N)                                |                                                              |
| vec3 reflect (vec3 I, vec3 N)                                |                                                              |
| vec4 reflect (vec4 I, vec4 N)                                |                                                              |
| float refract (float I, float N,float eta)                   |                                                              |
| vec2 refract (vec2 I, vec2 N,float eta)                      |                                                              |
| vec3 refract (vec3 I, vec3 N,float eta)                      |                                                              |
| vec4 refract (vec4 I, vec4 N,float eta)                      |                                                              |
| 矩阵函数                                                     |                                                              |
| mat2 matrixCompMult (mat2 x, mat2 y)                         | 返回每个部分的乘积，result[i][j]=x[i][j]*y[i][j],不同于矩阵乘法 |
| mat3 matrixCompMult (mat3 x, mat3 y)                         |                                                              |
| mat4 matrixCompMult (mat4 x, mat4 y)                         |                                                              |
| mat2 transpose (mat2 m)                                      | 转置矩阵                                                     |
| mat3 transpose (mat3 m)                                      |                                                              |
| mat4 transpose (mat4 m)                                      |                                                              |
| mat2x3 transpose (mat3x2 m)                                  |                                                              |
| mat3x2 transpose (mat2x3 m)                                  |                                                              |
| mat2x4 transpose (mat4x2 m)                                  |                                                              |
| mat4x2 transpose (mat2x4 m)                                  |                                                              |
| mat3x4 transpose (mat4x3 m)                                  |                                                              |
| mat4x3 transpose (mat3x4 m)                                  |                                                              |
| mat2 inverse (mat2 m)                                        | 逆矩阵                                                       |
| mat3 inverse (mat3 m)                                        |                                                              |
| mat4 inverse (mat4 m)                                        |                                                              |
| mat2 outerProduct (vec2 c, vec2 r)                           | 矢量积                                                       |
| mat3 outerProduct (vec3 c, vec3 r)                           |                                                              |
| mat4 outerProduct (vec4 c, vec4 r)                           |                                                              |
| mat2x3 outerProduct (vec2 c, vec3 r)                         |                                                              |
| mat3x2 outerProduct (vec3 c, vec2 r)                         |                                                              |
| mat2x4 outerProduct (vec2 c, vec4 r)                         |                                                              |
| mat4x2 outerProduct (vec4 c, vec2 r)                         |                                                              |
| mat3x4 outerProduct (vec3 c, vec4 r)                         |                                                              |
| mat4x3 outerProduct (vec4 c, vec3 r)                         |                                                              |
| 矢量关系函数                                                 |                                                              |
| bvec2 lessThan(vec2 x, vec2 y)                               | 返回对各个部分执行的x<y的比较结果                            |
| bvec3 lessThan(vec3 x, vec3 y)                               |                                                              |
| bvec4 lessThan(vec4 x, vec4 y)                               |                                                              |
| bvec2 lessThan(ivec2 x, ivec2 y)                             |                                                              |
| bvec3 lessThan(ivec3 x, ivec3 y)                             |                                                              |
| bvec4 lessThan(ivec4 x, ivec4 y)                             |                                                              |
| bvec2 lessThanEqual(vec2 x, vec2 y)                          | 返回对各个部分执行的x<=y的比较结果                           |
| bvec3 lessThanEqual(vec3 x, vec3 y)                          |                                                              |
| bvec4 lessThanEqual(vec4 x, vec4 y)                          |                                                              |
| bvec2 lessThanEqual(ivec2 x, ivec2 y)                        |                                                              |
| bvec3 lessThanEqual(ivec3 x, ivec3 y)                        |                                                              |
| bvec4 lessThanEqual(ivec4 x, ivec4 y)                        |                                                              |
| bvec2 greaterThan(vec2 x, vec2 y)                            | 返回对各个部分执行的x>y的比较结果                            |
| bvec3 greaterThan(vec3 x, vec3 y)                            |                                                              |
| bvec4 greaterThan(vec4 x, vec4 y)                            |                                                              |
| bvec2 greaterThan(ivec2 x, ivec2 y)                          |                                                              |
| bvec3 greaterThan(ivec3 x, ivec3 y)                          |                                                              |
| bvec4 greaterThan(ivec4 x, ivec4 y)                          |                                                              |
| bvec2 greaterThanEqual(vec2 x, vec2 y)                       | 返回对各个部分执行的x=>y的比较结果                           |
| bvec3 greaterThanEqual(vec3 x, vec3 y)                       |                                                              |
| bvec4 greaterThanEqual(vec4 x, vec4 y)                       |                                                              |
| bvec2 greaterThanEqual(ivec2 x, ivec2 y)                     |                                                              |
| bvec3 greaterThanEqual(ivec3 x, ivec3 y)                     |                                                              |
| bvec4 greaterThanEqual(ivec4 x, ivec4 y)                     |                                                              |
| bvec2 equal(vec2 x, vec2 y)                                  | 返回对各个部分执行的x==y的比较结果                           |
| bvec3 equal(vec3 x, vec3 y)                                  |                                                              |
| bvec4 equal(vec4 x, vec4 y)                                  |                                                              |
| bvec2 equal(ivec2 x, ivec2 y)                                |                                                              |
| bvec3 equal(ivec3 x, ivec3 y)                                |                                                              |
| bvec4 equal(ivec4 x, ivec4 y)                                |                                                              |
| bvec2 equal(bvec2 x, bvec2 y)                                |                                                              |
| bvec3 equal(bvec3 x, bvec3 y)                                |                                                              |
| bvec4 equal(bvec4 x, bvec4 y)                                |                                                              |
| bvec2 notEqual(vec2 x, vec2 y)                               | 返回对各个部分执行的x!=y的比较结果                           |
| bvec3 notEqual(vec3 x, vec3 y)                               |                                                              |
| bvec4 notEqual(vec4 x, vec4 y)                               |                                                              |
| bvec2 notEqual(ivec2 x, ivec2 y)                             |                                                              |
| bvec3 notEqual(ivec3 x, ivec3 y)                             |                                                              |
| bvec4 notEqual(ivec4 x, ivec4 y)                             |                                                              |
| bvec2 notEqual(bvec2 x, bvec2 y)                             |                                                              |
| bvec3 notEqual(bvec3 x, bvec3 y)                             |                                                              |
| bvec4 notEqual(bvec4 x, bvec4 y)                             |                                                              |
| bool any(bvec2 x)                                            | 如果x的任何部分为true，返回true                              |
| bool any(bvec3 x)                                            |                                                              |
| bool any(bvec4 x)                                            |                                                              |
| bool all(bvec2 x)                                            | 只有当x的所有部分都为true才返回true                          |
| bool all(bvec3 x)                                            |                                                              |
| bool all(bvec4 x)                                            |                                                              |
| bvec2 not(bvec2 x)                                           | 对各个部分取反                                               |
| bvec3 not(bvec3 x)                                           |                                                              |
| bvec4 not(bvec4 x)                                           |                                                              |
| 纹理访问函数                                                 |                                                              |
| vec4 texture (sampler1D sampler, float coord [, float bias] ) | 自行百度或查书                                               |
| vec4 textureProj (sampler1D sampler, vec2 coord [, float bias] ) |                                                              |
| vec4 textureProj (sampler1D sampler, vec4 coord [, float bias] ) |                                                              |
| vec4 textureLod (sampler1D sampler, float coord, float lod ) |                                                              |
| vec4 textureGrad (sampler1D sampler, float coord, float dPdx, float dPdy) |                                                              |
| vec4 textureOffset (sampler1D sampler, float coord, int offset, [, float bias] ) |                                                              |
| vec4 texelFetch (sampler1D sampler, int coord, int lod )     |                                                              |
| vec4 texelFetchOffset (sampler1D sampler, int coord, int lod, int offset ) |                                                              |
| vec4 textureProjLod (sampler1D sampler, vec2 coord , float lod ) |                                                              |
| vec4 textureProjLod (sampler1D sampler, vec4 coord , float lod ) |                                                              |
| vec4 textureProjGrad (sampler1D sampler, vec2 coord, float dPdx,float dPdy) |                                                              |
| vec4 textureProjGrad (sampler1D sampler, vec4 coord, float dPdx,float dPdy) |                                                              |
| vec4 textureProjOffset (sampler1D sampler, vec2 coord, int offset[, float bias] ) |                                                              |
| vec4 textureProjOffset (sampler1D sampler, vec4 coord, int offset[, float bias] ) |                                                              |
| vec4 textureLodOffset (sampler1D sampler, float coord, float lod, int offset) |                                                              |
| vec4 textureGradOffset (sampler1D sampler, float coord, float dPdx,float dPdy, int offset) |                                                              |
| vec4 textureProjLodOffset (sampler1D sampler, vec2 coord, float lod,int offset) |                                                              |
| vec4 textureProjLodOffset (sampler1D sampler, vec4 coord, float lod,int offset) |                                                              |
| vec4 textureProjGradOffset (sampler1D sampler, vec2 coord, float dPdx,float dPdy, int offset) |                                                              |
| vec4 textureProjGradOffset (sampler1D sampler, vec4 coord, float dPdx,float dPdy, int offset) |                                                              |
| int textureSize (isampler1D sampler, int lod )               |                                                              |
| 片元处理函数                                                 |                                                              |
| float dFdx (float p)                                         | 返回输入参数p在x上的导数                                     |
| vec2 dFdx (vec2 p)                                           |                                                              |
| vec3 dFdx (vec3 p)                                           |                                                              |
| vec4 dFdx (vec4 p)                                           |                                                              |
| float dFdy (float p)                                         | 返回输入参数p在y上的导数                                     |
| vec2 dFdy (vec2 p)                                           |                                                              |
| vec3 dFdy (vec3 p)                                           |                                                              |
| vec4 dFdy (vec4 p)                                           |                                                              |
| float fwidth (float p)                                       | 返回p在x和y上的绝对导数的和return = abs (dFdx (p)) + abs (dFdy (p)); |
| vec2 fwidth (vec2 p)                                         |                                                              |
| vec3 fwidth (vec3 p)                                         |                                                              |
| vec4 fwidth (vec4 p)                                         |                                                              |
| 噪声函数                                                     |                                                              |
| float noise1 (float x)                                       | 根据输入值返回一个噪声                                       |
| float noise1 (vec2 x)                                        |                                                              |
| float noise1 (vec3 x)                                        |                                                              |
| float noise1 (vec4 x)                                        |                                                              |
| vec2 noise2 (float x)                                        |                                                              |
| vec2 noise2 (vec2 x)                                         |                                                              |
| vec2 noise2 (vec3 x)                                         |                                                              |
| vec2 noise2 (vec4 x)                                         |                                                              |
| vec3 noise3 (float x)                                        |                                                              |
| vec3 noise3 (vec2 x)                                         |                                                              |
| vec3 noise3 (vec3 x)                                         |                                                              |
| vec3 noise3 (vec4 x)                                         |                                                              |
| vec4 noise4 (float x)                                        |                                                              |
| vec4 noise4 (vec2 x)                                         |                                                              |
| vec4 noise4 (vec3 x)                                         |                                                              |
| vec4 noise4 (vec4 x)                                         |                                                              |