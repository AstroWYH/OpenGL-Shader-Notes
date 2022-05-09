# Shader类&编写、编译、管理着色器

```cpp
#ifndef SHADER_H
#define SHADER_H

#include <glad/glad.h>
#include <string>
#include <fstream>
#include <sstream>
#include <iostream>

class Shader // 自定义Shader类
{
public:
    unsigned int ID;
    // 构造函数
    // ------------------------------------------------------------------------
    Shader(const char* vertexPath, const char* fragmentPath)
    {
        // 1. 从文件路径中获取顶点/片元着色器
        std::string vertexCode;
        std::string fragmentCode;
        std::ifstream vShaderFile;
        std::ifstream fShaderFile;
        // 保证ifstream对象可以抛出异常
        vShaderFile.exceptions (std::ifstream::failbit | std::ifstream::badbit);
        fShaderFile.exceptions (std::ifstream::failbit | std::ifstream::badbit);
        try 
        {
            // 打开文件
            vShaderFile.open(vertexPath);
            fShaderFile.open(fragmentPath);
            std::stringstream vShaderStream, fShaderStream;
            // 读取文件的缓冲内容到数据流中
            vShaderStream << vShaderFile.rdbuf();
            fShaderStream << fShaderFile.rdbuf();
            // 关闭文件处理器
            vShaderFile.close();
            fShaderFile.close();
            // 转换数据流到string
            vertexCode   = vShaderStream.str();
            fragmentCode = fShaderStream.str();
        }
        catch (std::ifstream::failure& e)
        {
            std::cout << "ERROR::SHADER::FILE_NOT_SUCCESFULLY_READ: " << e.what() << std::endl;
        }
        const char* vShaderCode = vertexCode.c_str(); // 顶点着色器的.vs
        const char * fShaderCode = fragmentCode.c_str(); // 片元着色器的.fs
        // 2. 编译着色器
        unsigned int vertex, fragment;
        // 顶点着色器
        // 我们把需要创建的着色器类型以参数形式提供给glCreateShader。由于我们正在创建一个顶点着色器，传递的参数是GL_VERTEX_SHADER。
        vertex = glCreateShader(GL_VERTEX_SHADER);
        glShaderSource(vertex, 1, &vShaderCode, NULL); // 下一步我们把这个着色器源码附加到着色器对象上，然后编译它。
        glCompileShader(vertex);
        checkCompileErrors(vertex, "VERTEX"); // 检查着色器程序的 编译/链接 错误
        // 片元着色器（与顶点着色器类似）
        fragment = glCreateShader(GL_FRAGMENT_SHADER);
        glShaderSource(fragment, 1, &fShaderCode, NULL);
        glCompileShader(fragment);
        checkCompileErrors(fragment, "FRAGMENT");
        // 着色器程序
        ID = glCreateProgram(); // glCreateProgram函数创建一个程序，并返回新创建程序对象的ID引用。
        glAttachShader(ID, vertex);
        glAttachShader(ID, fragment);
        glLinkProgram(ID); // 现在我们需要把之前编译的着色器附加到程序对象上，然后用glLinkProgram链接它们。
        checkCompileErrors(ID, "PROGRAM");
        // 删除着色器，它们已经链接到我们的程序中了，已经不再需要了
        glDeleteShader(vertex);
        glDeleteShader(fragment);
    }
    // 激活着色器
    // ------------------------------------------------------------------------
    void use() 
    { 
        glUseProgram(ID); // 更新一个uniform之前你必须先使用程序（调用glUseProgram)，或称为激活着色器
    }
    // 我们用glGetUniformLocation查询uniform ourColor的位置值。我们为查询函数提供着色器程序和uniform的名字（这是我们希望获得的位置值的来源）。
    // 如果glGetUniformLocation返回-1就代表没有找到这个位置值。最后，我们可以通过glUniform1i函数设置uniform值。
    // 注意，查询uniform地址不要求你之前使用过着色器程序，但是更新一个uniform之前你必须先使用程序（调用glUseProgram)，因为它是在当前激活的着色器程序中设置uniform的。
    // ------------------------------------------------------------------------
    void setBool(const std::string &name, bool value) const
    {
        glUniform1i(glGetUniformLocation(ID, name.c_str()), (int)value); 
    }
    // ------------------------------------------------------------------------
    void setInt(const std::string &name, int value) const
    { 
        glUniform1i(glGetUniformLocation(ID, name.c_str()), value); 
    }
    // ------------------------------------------------------------------------
    void setFloat(const std::string &name, float value) const
    { 
        glUniform1f(glGetUniformLocation(ID, name.c_str()), value); 
    }

private:
    // 检查着色器程序的 编译/链接 错误
    // ------------------------------------------------------------------------
    void checkCompileErrors(unsigned int shader, std::string type)
    {
        int success;
        char infoLog[1024];
        if (type != "PROGRAM")
        {
            glGetShaderiv(shader, GL_COMPILE_STATUS, &success); // 我们用glGetShaderiv检查是否编译成功
            if (!success)
            {
                glGetShaderInfoLog(shader, 1024, NULL, infoLog); // 打印编译错误
                std::cout << "ERROR::SHADER_COMPILATION_ERROR of type: " << type << "\n" << infoLog << "\n -- --------------------------------------------------- -- " << std::endl;
            }
        }
        else
        {
            glGetProgramiv(shader, GL_LINK_STATUS, &success); // 我们用glGetShaderiv检查是否链接成功
            if (!success)
            {
                glGetProgramInfoLog(shader, 1024, NULL, infoLog); // 打印链接错误
                std::cout << "ERROR::PROGRAM_LINKING_ERROR of type: " << type << "\n" << infoLog << "\n -- --------------------------------------------------- -- " << std::endl;
            }
        }
    }
};
#endif
```

