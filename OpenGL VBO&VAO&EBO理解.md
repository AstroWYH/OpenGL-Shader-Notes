# OpenGL关于VBO&VAO&EBO理解.md

### 顶点缓冲对象（Vertex Buffer Objects，VBO）

- 顶点缓冲对象VBO是在显卡存储空间中开辟出的一块内存缓存区，用于存储顶点的各类属性信息，如顶点坐标，顶点法向量，顶点颜色数据等。
- 在渲染时，可以直接从VBO中取出顶点的各类属性数据，由于VBO在显存而不是在内存中，不需要从CPU传输数据，处理效率更高。
- 所以可以理解为**VBO就是显存中的一个存储区域，可以保持大量的顶点属性信息。**
- 并且可以开辟很多个VBO，**每个VBO在OpenGL中有它的唯一标识ID，这个ID对应着具体的VBO的显存地址**，通过这个ID可以对特定的VBO内的数据进行存取操作。

#### VBO的创建以及配置

创建VBO的第一步需要开辟（声明/获得）显存空间并分配VBO的ID：

```glsl
// 创建vertex buffer object对象
GLuint vboId;//vertex buffer object句柄
glGenBuffers(1, &vboId);
```

创建的VBO可用来保存不同类型的顶点数据，创建之后需要通过分配的ID绑定（bind）一下制定的VBO，对于同一类型的顶点数据一次只能绑定一个VBO。

绑定操作通过glBindBuffer来实现，第一个参数指定绑定的数据类型，可以是GL_ARRAY_BUFFER, GL_ELEMENT_ARRAY_BUFFER, GL_PIXEL_PACK_BUFFER或者GL_PIXEL_UNPACK_BUFFER中的一个。

```glsl
glBindBuffer(GL_ARRAY_BUFFER, vboId);
```

接下来调用**glBufferData把用户定义的数据**传输到**当前绑定的显存缓冲区**中：

```glsl
glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);
```

顶点数据传入GPU之后，还需要通知OpenGL如何解释这些顶点数据，这个工作由函数glVertexAttribPointer完成：

```glsl
glVertexAttribPointer(0, 4, GL_FLOAT, GL_FALSE, 0, 0);
第一个参数指定顶点属性位置，与顶点着色器中layout(location=0)对应。
第二个参数指定顶点属性大小。
第三个参数指定数据类型。
第四个参数定义是否希望数据被标准化。
第五个参数是步长（Stride），指定在连续的顶点属性之间的间隔。
第六个参数表示我们的位置数据在缓冲区起始位置的偏移量。

// 注：顶点属性glVertexAttribPointer默认是关闭的，使用时要以顶点属性位置值为参数调用glEnableVertexAttribArray开启。
// 如glEnableVertexAttribArray(0);
```

### 顶点数组对象（Vertex Arrary Object，VAO）

- VBO保存了一个模型的顶点属性信息，每次绘制模型之前需要绑定顶点的所有信息，当数据量很大时，重复这样的动作变得非常麻烦。
- VAO可以把这些所有的配置都存储在一个对象中，每次绘制模型时，只需要绑定这个VAO对象就可以了。
- VAO是一个保存了所有顶点数据属性的状态结合，它存储了顶点数据的格式以及顶点数据所需的**VBO对象的引用**。

- VAO本身并没有存储顶点的相关属性数据，**这些信息是存储在VBO**中的，**VAO相当于是对很多个VBO的引用，把一些VBO组合在一起作为一个对象统一管理**。

#### VAO的创建和配置

生成一个VAO对象并绑定：

```glsl
// 创建vertex array object对象   
GLuint vaoId;//vertext array object句柄
glGenVertexArrays(1, &vaoId);
glBindVertexArray(vaoId);
```

**执行VAO绑定之后其后的所有VBO配置都是这个VAO对象的一部分**，可以说**VBO是对顶点属性信息的绑定，VAO是对很多个VBO的绑定**。

OpenGL中所有的图形都是通过分解成三角形的方式进行绘制，**glDrawArrays函数负责把模型绘制出来，它使用当前激活的着色器，当前VAO对象中的VBO顶点数据和属性配置来绘制出来基本图形。**

```glsl
glDrawArrays (GLenum mode, GLint first, GLsizei count);
第一个参数表示绘制的类型，有三种取值：
1.GL_TRIANGLES：每三个顶之间绘制三角形，之间不连接；
2.GL_TRIANGLE_FAN：以V0V1V2,V0V2V3,V0V3V4，……的形式绘制三角形；
3.GL_TRIANGLE_STRIP：顺序在每三个顶点之间均绘制三角形。这个方法可以保证从相同的方向上所有三角形均被绘制。以V0V1V2,V1V2V3,V2V3V4……的形式绘制三角形；
第二个参数定义从缓存中的哪一位开始绘制，一般定义为0；
第三个参数定义绘制的顶点数量。
```

### 索引缓冲对象（Element Buffer Object，EBO）

- 索引缓冲对象EBO相当于OpenGL中的顶点数组的概念，是为了**解决同一个顶点多次重复调用的问题，可以减少内存空间浪费，提高执行效率**。
- 当**需要使用重复的顶点时，通过顶点的位置索引来调用顶点**，而不是对重复的顶点信息重复记录，重复调用。
- EBO中存储的内容就是顶点位置的索引indices，**EBO跟VBO类似，也是在显存中的一块内存缓冲器，只不过EBO保存的是顶点的索引**。

创建EBO并绑定，用glBufferData（以GL_ELEMENT_ARRAY_BUFFER为参数）把索引存储到EBO中：

```glsl
GLuint EBO;
glGenBuffers(1, &EBO);
glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, EBO);
glBufferData(GL_ELEMENT_ARRAY_BUFFER, sizeof(indices), indices, GL_STATIC_DRAW);
```

**当用EBO绑定顶点索引的方式绘制模型时，需要使用glDrawElements而不是glDrawArrays**：

```glsl
glDrawElements(GL_TRIANGLES, 6, GL_UNSIGNED_INT, 0);
第一个参数指定了要绘制的模式；
第二个参数指定要绘制的顶点个数；
第三个参数是索引的数据类型；
第四个参数是可选的EBO中偏移量设定。
```

下面用两种方式绘制同样的矩形图像。

### 方法一：使用VBO，VAO绘制一个矩形图形

```glsl
//使用VAO VBO绘制矩形
#include <GL/glew.h>  
#include <GL/freeglut.h>  
 
void userInit();  //自定义初始化
void reshape(int w, int h);   //重绘
void display(void);
void keyboardAction(unsigned char key, int x, int y);   //键盘退出事件
 
GLuint vboId;//vertex buffer object句柄    
GLuint vaoId;//vertext array object句柄    
GLuint programId;//shader program 句柄    
 
int main(int argc, char **argv)
{
	glutInit(&argc, argv);
	glutInitDisplayMode(GLUT_RGBA | GLUT_DOUBLE);
	glutInitWindowPosition(100, 100);
	glutInitWindowSize(512, 512);
	glutCreateWindow("Rectangle demo");
 
	//使用glew，需要执行glewInit，不然运行过程会报错
	//glewInit要放在glut完成了基本的初始化之后执行
	glewInit();
 
	//自定义初始化，生成VAO，VBO对象
	userInit();
 
	//重绘函数
	glutReshapeFunc(reshape);
 
	glutDisplayFunc(display);
 
	//注册键盘按键退出事件
	glutKeyboardFunc(keyboardAction);
 
	glutMainLoop();
	return 0;
}
 
//自定义初始化函数    
void userInit()
{
	glClearColor(0.0, 0.0, 0.0, 0.0);
	//创建顶点数据    
	const GLfloat vertices[] = {
		-0.5f,-0.5f,0.0f,1.0f,
		0.5f,-0.5f,0.0f,1.0f,
		0.5f,0.5f,0.0f,1.0f,
		-0.5f,0.5f,0.0f,1.0f,
	};
 
	//创建VAO对象
	glGenVertexArrays(1, &vaoId);
	glBindVertexArray(vaoId);
 
	//创建VBO对象	
	glGenBuffers(1, &vboId);
	glBindBuffer(GL_ARRAY_BUFFER, vboId);
	//传入VBO数据
	glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);
	//解除VBO绑定
	glBindBuffer(GL_ARRAY_BUFFER, 0);
}
 
//调整窗口大小回调函数    
void reshape(int w, int h)
{
	glViewport(0, 0, (GLsizei)w, (GLsizei)h);
}
 
//绘制回调函数    
void display(void)
{
	glClear(GL_COLOR_BUFFER_BIT);
 
	//绑定VBO
	glBindBuffer(GL_ARRAY_BUFFER, vboId);
	glEnableVertexAttribArray(0);
 
	//解释顶点数据方式
	glVertexAttribPointer(0, 4, GL_FLOAT, GL_FALSE, 0, 0);
 
	//绘制模型
	glDrawArrays(GL_TRIANGLE_FAN, 0, 4);
 
	glBindBuffer(GL_ARRAY_BUFFER, 0);
	glDisableVertexAttribArray(0);
 
	glutSwapBuffers();
}
 
//键盘按键回调函数    
void keyboardAction(unsigned char key, int x, int y)
{
	switch (key)
	{
	case 033:  // Escape key    
		exit(EXIT_SUCCESS);
		break;
	}
}
```

编译并执行：

![image-20220616111834433](https://hanbabang-1311741789.cos.ap-chengdu.myqcloud.com/Pics/image-20220616111834433.png)

### 方法二：使用EBO绘制两个三角形，组成同样的矩形图形

```glsl
//使用EBO绘制矩形(两个三角形)
#include <GL/glew.h>  
#include <GL/freeglut.h>  
 
void userInit();  //自定义初始化
void reshape(int w, int h);   //重绘
void display(void);
void keyboardAction(unsigned char key, int x, int y);   //键盘退出事件
 
GLuint eboId;//element buffer object句柄    
GLuint vboId;//vertext buffer object句柄    
GLuint vaoId;//vertext array object句柄    
 
int main(int argc, char **argv)
{
	glutInit(&argc, argv);
	glutInitDisplayMode(GLUT_RGBA | GLUT_DOUBLE);
	glutInitWindowPosition(100, 100);
	glutInitWindowSize(512, 512);
	glutCreateWindow("Rectangle demo");
 
	//使用glew，需要执行glewInit，不然运行过程会报错
	//glewInit要放在glut完成了基本的初始化之后执行
	glewInit();
 
	//自定义初始化，生成VAO，VBO,EBO
	userInit();
 
	//重绘函数
	glutReshapeFunc(reshape);
	glutDisplayFunc(display);
	//注册键盘按键退出事件
	glutKeyboardFunc(keyboardAction);
	glutMainLoop();
	return 0;
}
 
//自定义初始化函数    
void userInit()
{
	glClearColor(0.0, 0.0, 0.0, 0.0);
 
	//创建顶点数据    
	const GLfloat vertices[] = {
		-0.5f,-0.5f,0.0f,1.0f,
		0.5f,-0.5f,0.0f,1.0f,
		0.5f,0.5f,0.0f,1.0f,
		-0.5f,0.5f,0.0f,1.0f,
	};
	// 索引数据
	GLshort indices[] = {
		0, 1, 3,  // 第一个三角形
		1, 2, 3   // 第二个三角形
	};
 
	//创建VAO对象
	glGenVertexArrays(1, &vaoId);
	glBindVertexArray(vaoId);
 
	//创建VBO对象，把顶点数组复制到一个顶点缓冲中，供OpenGL使用
	glGenBuffers(1, &vboId);
	glBindBuffer(GL_ARRAY_BUFFER, vboId);
	glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);
 
	//创建EBO对象	
	glGenBuffers(1, &eboId);
	glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, eboId);
	//传入EBO数据
	glBufferData(GL_ELEMENT_ARRAY_BUFFER, sizeof(indices), indices, GL_STATIC_DRAW);
 
	//解释顶点数据方式
	glVertexAttribPointer(0, 4, GL_FLOAT, GL_FALSE, 0, 0);
	glEnableVertexAttribArray(0);
 
	//解绑VAO
	glBindVertexArray(0);
	//解绑EBO
	glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, 0);
	//解绑VBO
	glBindBuffer(GL_ARRAY_BUFFER, 0);
}
 
//调整窗口大小回调函数    
void reshape(int w, int h)
{
	glViewport(0, 0, (GLsizei)w, (GLsizei)h);
}
 
//绘制回调函数    
void display(void)
{
	glClear(GL_COLOR_BUFFER_BIT);
	//绑定VAO
	glBindVertexArray(vaoId);
	//绘制模型
	glDrawElements(GL_TRIANGLES, 6, GL_UNSIGNED_SHORT, NULL);
	glutSwapBuffers();
}
 
//键盘按键回调函数    
void keyboardAction(unsigned char key, int x, int y)
{
	switch (key)
	{
	case 033:  // Escape key    
		exit(EXIT_SUCCESS);
		break;
	}
}
```

效果一样：

![image-20220616111910014](https://hanbabang-1311741789.cos.ap-chengdu.myqcloud.com/Pics/image-20220616111910014.png)

### 参考链接

[(5条消息) OpenGL图形渲染管线、VBO、VAO、EBO概念及用例_-牧野-的博客-CSDN博客_vao vbo](https://blog.csdn.net/dcrmg/article/details/53556664)
