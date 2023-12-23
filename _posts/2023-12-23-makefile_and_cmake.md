---
layout: post
title: "makefile and cmake入门"
date: 2023-12-23
description: "makefile and cmake入门"
tag: cp
---

### 一、 Makefile

#### （一）一个例子

![](images/posts/cmake0/1.png)

main.cpp

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

printhello.cpp

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

factorial.cpp

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

有两个函数分别定义在不同的CPP文件（printhello.cpp和factorial.cpp）中，在main.cpp中进行调用，所有的函数定义放在头文件（functions.h）中。在main.cpp中通过#include "functions.h"实现对两个函数的调用。

那么，如何进行编译并运行该程序呢？

- 可以在命令行中通过如下命令进行编译

`g++ main.cpp factorial.cpp printhello.cpp -o main`

- 通过如下命令运行该程序

`./main`

- 结果如下：

![](images/posts/cmake0/2.png)

本案例运行成功。本案例中仅有三个源文件，在实际项目中可能有很多个源文件，如果仍按照这种方式编译，则需要花费很长时间。于是就引出了Makefile

（二）Makefile

**`Version 1`**


```makefile
## VESRSION 1
# 生成的可执行程序叫hello，hello的生成依赖于main.cpp printhello.cpp factorial.cpp
# g++前使用tab符
# 在终端使用make命令执行该脚本，make会自动找当前目录下的Makefile文件
hello: main.cpp printhello.cpp factorial.cpp
	g++ -o hello main.cpp printhello.cpp factorial.cpp
```

![](images/posts/cmake0/3.png)

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

![](images/posts/cmake0/4.png)

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

![](images/posts/cmake0/5-1.png)

![](images/posts/cmake0/5-2.png)

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

