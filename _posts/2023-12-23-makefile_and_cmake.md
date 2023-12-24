---
layout: post
title: "makefile and cmake入门"
date: 2023-12-23
description: "makefile and cmake入门"
tag: cmake
---

### 一、 Makefile

#### （一）一个例子

![](/images/posts/cmake0/1.png)

**`main.cpp`**

主函数，分别调用printhello()和factorial()函数。

```c++
#include<iostream>
#include "functions.h"
using namespace std;

int main()
{
    printhello();
    cout<<"This is main:" <<endl;
    cout<<"The factorial of 5 is: "<<factorial(5) <<endl;
    return 0;
}
```

**`printhello.cpp`**

用途：打印字符串"Hello World!"

```c++
#include<iostream>
#include "functions.h"

using namespace std;

void printhello()
{
    int i;
    cout<<"Hello World!"<<endl;
}
```

**`factorial.cpp`**

用途：求一个正整数n的阶乘

```c++
#include "functions.h"
int factorial(int n)
{
    if (n ==1)
        return 1;
    else
        return n * factorial(n -1);
}
```

**`functions.h`**

```c++
#ifndef _FUNCTIONS_H_
#define _FUNCTIONS_H_
void printhello();
int factorial(int n);
#endif
```

有两个函数分别定义在不同的CPP文件（printhello.cpp和factorial.cpp）中，在main.cpp中进行调用，所有的函数定义放在头文件（functions.h）中。在main.cpp中通过#include "functions.h"实现对两个函数的调用。

那么，如何进行编译并运行该程序呢？

- 可以在命令行中通过如下命令进行编译

`g++ main.cpp factorial.cpp printhello.cpp -o main`

- 通过如下命令运行该程序

`./main`

- 结果如下：

![](/images/posts/cmake0/2.png)

本案例运行成功。本案例中仅有三个源文件，在实际项目中可能有很多个源文件，如果仍按照这种方式编译，则需要花费很长时间。于是就引出了Makefile

#### （二）Makefile

**`Version 1`**


```makefile
## VESRSION 1
# 生成的可执行程序叫hello，hello的生成依赖于main.cpp printhello.cpp factorial.cpp
# g++前使用tab符
# 在终端使用make命令执行该脚本，make会自动找当前目录下的Makefile文件
hello: main.cpp printhello.cpp factorial.cpp
	g++ -o hello main.cpp printhello.cpp factorial.cpp
```

![](/images/posts/cmake0/3.png)

缺点：如果源文件非常多，那么每次的编译时间会很长。

**`Version2`**

```makefile
## VESRSION 2
CXX = g++
TARGET = hello
OBJ = main.o printhello.o factorial.o

$(TARGET) : $(OBJ)  # 说明TARGET这个变量(hello)依赖于OBJ文件(main.o printhello.o factorial.o)
	$(CXX) -o $(TARGET)  $(OBJ)

main.o: main.cpp
	$(CXX) -c main.cpp

printhello.o: printhello.cpp
	$(CXX) -c printhello.cpp

factorial.o: factorial.cpp
	$(CXX) -c factorial.cpp

```

![](/images/posts/cmake0/4.png)

该中方式将只会对发生修改的CPP文件进行重新编译，随后对*.o文件进行链接，生成最终的可执行文件。

**`Vserion 3`**

```makefile
## VESRSION 3
CXX = g++
TARGET = hello
OBJ = main.o printhello.o factorial.o 

CXXFLAGS= -c -Wall # 编译选项，所有的Warning都显示出来

$(TARGET) : $(OBJ) # $@ 表示目标文件(i.e. TARGET), $^表示所有的依赖文件 (OBJ)
	$(CXX) -o $@ $^  


%.o : %.cpp # $< 表示第一个依赖文件 $@ 是指目标(.o)
	$(CXX) $(CXXFLAGS) $< -o $@

.PHONY: clean
clean: # 使用make clean命令，就会清除当前目录下所有的.o文件和TARGET
	rm -f *.o $(TARGET)
```

![](/images/posts/cmake0/5-1.png)

![](/images/posts/cmake0/5-2.png)

**`Version 4`**

该版本是对第3个版本的进一步改进，该版本不需要再显示写出文件名

```makefile
# Version 4
CXX = g++
TARGET = hello
#查找当前路径下所有后缀为.cpp的文件,保存到SRC变量中
SRC = $(wildcard *.cpp)
# 把SRC中的cpp后缀替换成.o
OBJ = $(patsubst %.cpp, %.o, $(SRC))  

$(info SRC is $(SRC))
$(info OBJ is $(OBJ))

CXXFLAGS= -c -Wall # 编译选项，所有的Warning都显示出来

$(TARGET) : $(OBJ) # $@ 表示目标文件, $^ 表示所有的依赖文件
	$(CXX) -o $@ $^  


%.o : %.cpp # $< 表示第一个依赖文件 $@ 是指目标(.o)
	$(CXX) $(CXXFLAGS) $< -o $@

.PHONY: clean
clean:
	rm -f *.o $(TARGET)
```



### 二、Cmake

前面讲的Makefile可以高效管理源文件进行编译，但其仍旧存在一个问题。Makefile的配置是与操作系统强相关的，比如在Linux或MacOS下配置的Makefile的文件放到Windows下就会有各种问题，这是路径、编译器等不同造成的。那么如果要开发跨平台的软件，是否要给不同的环境写不同的Makefile？这个事情就会非常繁琐，令人头大。于是，CMake应运而生！！！

![](/images/posts/cmake0/6.png)

**`CMakeLists.txt`**

CMake的最基本语法如下所示

```cmake
# 最低的cmake版本
cmake_minimum_required(VERSION 3.10)

project(hello) 

# 生成可执行文件hello，其依赖于后面的几个cpp文件
add_executable(hello main.cpp factorial.cpp printhello.cpp)
```

- `cmake .`

上述命令会在当前目录寻找CMakeLists.txt，读取其配置并执行，结果如下图所示。

![](/images/posts/cmake0/6-1.png)

可以看到生成了很多文件，最瞩目的是**`Makefile`**。这就是我们使用CMake的目的，让CMake帮我们生成当前操作系统环境下的Makefile。

- make

随后，使用make命令，使用Makefile文件，进行编译。

![](/images/posts/cmake0/6-2.png)



PS：上述方式会导致目录下文件比较乱，我们可以采用如下图的方式进行。

![](/images/posts/cmake0/6-3.png)



一些碎碎念：昨天刷在知乎刷到一个话题“为什么大学C语言课不顺便教一下Makefile、CMake……”。下面的高赞评论是“因为老师不会啊！”。看到后特别想笑，因为深有体会。记得当时C语言课，采用由谭浩强编写的"void main"风格的教材，教大家使用古老的Visual C++6.0 IDE编写代码。至于C++老师呢，每次上课前都要吹吹她女儿五年级的小学生都开始学C++了，然而她的课讲的稀烂，完全不知所云。好在当时的郑莉老师编写的教材还不错，并带有网课，跟着学了一学期哈哈哈，最后理论课和实验课都满绩了。好在自己还算有探索精神，进行过各种尝试：使用visual studio高级一点的IDE来编程，也尝试了在windows下安装mingw，使用visul studio code编程，安装双系统ubuntu（当时一点不怕折腾，没少重装系统哈哈哈哈），学习使用git，在阿里云租服务器搭建个人主页，使用github pages搭建个人主页，研究过遗传算法相关的调度问题，尝试过医学图像分割（当时还是使用的Tensorflow1.x，难用的要死），写过一些爬虫脚本（当然现在可能也不怎么会写了阿哈哈）…… 现在回想起来觉得还蛮有趣，希望自己能保持求知和探索的精神，纸上得来终觉浅， 绝知此事要躬行。
