# OpenGL常用函数整理

### glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), (void*)0);

- 第一个参数指定我们要配置的顶点属性。还记得我们在顶点着色器中使用`layout(location = 0)`定义了position顶点属性的位置值(Location)吗？它可以把顶点属性的位置值设置为`0`。因为我们希望把数据传递到这一个顶点属性中，所以这里我们传入`0`。
- 第二个参数指定顶点属性的大小。顶点属性是一个`vec3`，它由3个值组成，所以大小是3。
- 第三个参数指定数据的类型，这里是GL_FLOAT(GLSL中`vec*`都是由浮点数值组成的)。
- 下个参数定义我们是否希望数据被标准化(Normalize)。如果我们设置为GL_TRUE，所有数据都会被映射到0（对于有符号型signed数据是-1）到1之间。我们把它设置为GL_FALSE。
- 第五个参数叫做步长(Stride)，它告诉我们在连续的顶点属性组之间的间隔。由于下个组位置数据在3个`float`之后，我们把步长设置为`3 * sizeof(float)`。要注意的是由于我们知道这个数组是紧密排列的（在两个顶点属性之间没有空隙）我们也可以设置为0来让OpenGL决定具体步长是多少（只有当数值是紧密排列时才可用）。一旦我们有更多的顶点属性，我们就必须更小心地定义每个顶点属性之间的间隔，我们在后面会看到更多的例子（译注: 这个参数的意思简单说就是从这个属性第二次出现的地方到整个数组0位置之间有多少字节）。
- 最后一个参数的类型是void*，数据指针， 这个值受到VBO的影响；1）**在不使用VBO的情况下，就是一个指针，指向的是需要上传到顶点数据指针**，项目中通常在不使用VBO的情况下，绘制之前，执行glBindBuffer(GL_ARRAY_BUFFER, 0)，否则会导致数组顶点无效，界面无法显示；2）**使用VBO的情况下**，先要执行glBindBuffer(GL_ARRAY_BUFFER, 1)，如果一个名称非零的缓冲对象被绑定至GL_ARRAY_BUFFER目标（见glBindBuffer）且此时一个定点属性数组被指定了，**那么pointer被当做该缓冲对象数据存储区的字节偏移量**。并且，缓冲对象绑定（GL_ARRAY_BUFFER_BINDING）会被存为索引为index的顶点属性数组客户端状态，此时指针指向的就不是具体的数据了。**因为数据已经缓存在缓冲区了，这里的指针表示位置数据在缓冲中起始位置的偏移量(Offset)**。由于位置数据在数组的开头，所以这里是0。

[(4条消息) OpenGL glVertexAttribPointer（）函数解析_soft_logic的博客-CSDN博客](https://blog.csdn.net/weixin_37459951/article/details/96433508?ops_request_misc=%7B%22request%5Fid%22%3A%22165510409416782390549174%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=165510409416782390549174&biz_id=0&spm=1018.2226.3001.4187)

### glUniform

因为OpenGL在其核心是一个C库，所以它不支持类型重载，在函数参数不同的时候就要为其定义新的函数；glUniform是一个典型例子。这个函数有一个特定的后缀，标识设定的uniform的类型。可能的后缀有：

- 后缀	含义

- f	  函数需要一个float作为它的值
- i	  函数需要一个int作为它的值
- ui	函数需要一个unsigned int作为它的值
- 3f	函数需要3个float作为它的值
- fv	函数需要一个float向量/数组作为它的值

每当你打算配置一个OpenGL的选项时就可以简单地根据这些规则选择适合你的数据类型的重载函数。在我们的例子里，我们希望分别设定uniform的4个float值，所以我们通过glUniform4f传递我们的数据(注意，我们也可以使用fv版本)。

### void glEnableVertexAttribArray(GLuint index);

### void glDisableVertexAttribArray(GLuint index);

- 写代码画三角形的时候发现总是无法输出顶点的数据，查阅文档之后发现是因为不小心注释掉了glEnableVertexAttribArray(0);
- 当我们特别谈论到顶点着色器的时候，每个输入变量也叫顶点属性(Vertex Attribute)。我们能声明的顶点属性是有上限的，它一般由硬件来决定。OpenGL确保至少有16个包含4分量的顶点属性可用，但是有些硬件或许允许更多的顶点属性，你可以查询GL_MAX_VERTEX_ATTRIBS来获取具体的上限。通常情况下它至少会返回16个，大部分情况下是够用了。
- 默认情况下，**出于性能考虑，所有顶点着色器的属性（Attribute）变量都是关闭的**，意味着数据在着色器端是不可见的，哪怕数据已经上传到GPU，由glEnableVertexAttribArray启用指定属性，才可在顶点着色器中访问逐顶点的属性数据。**glVertexAttribPointer或VBO只是建立CPU和GPU之间的逻辑连接，从而实现了CPU数据上传至GPU。但是，数据在GPU端是否可见，即，着色器能否读取到数据，由是否启用了对应的属性决定，这就是glEnableVertexAttribArray的功能，允许顶点着色器读取GPU（服务器端）数据。**

- 那么，glEnableVertexAttribArray应该在glVertexAttribPointer之前还是之后调用？答案是**都可以**，只要在绘图调用（glDraw*系列函数）前调用即可。

- 不用glEnableVertexAttribArray对应属性时绘制内容为清除缓冲区颜色。在取消glEnableVertexAttribArray(0);注释后就可以得到正常的绘制结果。
  
