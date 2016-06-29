# C语言编译

## include区别
包含头文件总共有两种方式：

* `#include "hello.h"`
>如果用这种方式包含头文件，编译器会首先在当前目录中寻找该头文件，如果不存在则再去系统头文件目录中搜索


* `#include <hello.h>`
>如果用这种方式包含头文件，编译器不会在当前目录中寻找，直接去系统头文件目录中搜索

## GCC编译
```
gcc -Wall -g  hello.c -o hello
```
> `-Wall` 选项是告诉编译器开启几乎所有常用的警告，这一选项非常有用，默认情况下,`GCC`不会产生任何警告，开启`-Wall` 可以有助于检测程序的错误
> `-g`表示在生成的目标文件中带调试信息，调试信息可以在程序异常终止产生core后，帮助分析错误产生的源头，包括产生错误的文件名和行号等非常有用的信息
> `-o`指明生成的文件的名称

## 简单的Makefile文件
`makefile`指定了一系列目标和依赖的编译规则，格式如下：
```
目标：依赖
      命令
```
`makefile`中命令行必须亦单个的`TAB`字符进行缩进，不能是空格
例子：
```
CC=gcc
CFLAGS=-Wall
hello: hello.o
clean:
    rm -f hello hello.o
```
其对应的含义是：
1. 使用`GCC`编译器
2. 添加变异选项`-Wall`
3. 从对象文件`hello.o`生成目标可执行文件`hello`
4. 目标`clean`没有依赖，他只是简单的移除所有编译文件

要使用该`makefile`文件，输入`make`,不加参数调用`make`时，`makefile`文件中的第一个目标被建立
一个专业的`makefile`文件通常包含用于安装(`make install`)和测试(`make check`)等额外的目标

## 链接外部库
库是预编译的的目标文件(object files)的集合，他们可被链接进程序
静态库以`.a`的特殊形式存储（意为存档文件(`archive　file`)）
标准系统库可以在目录`/usr/lib`和`/lib`中找到
**`C`标准库本身存储为`/usr/lib/libc.a`**，它包含了`ANSI/ISO C`标准指定的函数，如`printf`,对于每一个`C`程序来说,`libc.a`默认被链接
一般指定一个静态库的编译方法是：
```
gcc -Wall hello.c /usr/lib/libm.a -o hello
```
为了避免在命令行中出现长长的路径，编译器为链接函数提供了快捷的选项`-l`,如：
```
gcc -Wall hello.c -lm -o hello
```
这条命令与上面的命令是等价的，一般来说编译器会根据`-lNAME`尝试链接系统库目录中的函数库文件`libNAME.a`

##编译C++
```
gcc -Wall hello.c -lstdc++ -o hello
```
>**注意**：如果用`gcc`编译`C++`代码时必须指定`-lstdc++`,`libstdc++.a`是`C++`

除此之外，也可以直接用`GCC`专门编译`C++`的工具`G++`
```
g++ -Wall hello.cpp -o hello
```

## C编程中相关文件后缀
+ .a 静态库(archieve)
+ .c C源代码(需要编译预处理)
+ .h C源代码头文件
+ .i C源代码(不需要编译预处理)
+ .o 对象文件
+ .s 汇编文件
+ .so 动态库

## 源文件生成对象文件
选项`-c`指示`GCC`编译源代码文件并生成对象文件即`.o`文件，如果不加`-o`选项则生成同源文件同名的对象文件
```
gcc -c -Wall hello.c -o test.o
```
一条命令也可以生成多个源文件对应的对象文件
```
gcc -c -Wall hello.c test.c ubuntu.c
```
上面这条命令将生成`hello.o test.o ubuntu.o`

**NOTE:**
`g++`同样也能识别`.o`文件，也可以通过对象文件(.o)来生成可执行程序：
```
g++ hello.o test.o -o hello
```


## 多个源文件生成可执行程序
```
gcc -Wall hello.c test.c -o hello
```
上面这条命令`hello.c`和`test.c`两个源文件编译并生成了一个可执行程序`hello`

## 编译预处理
选项`-E`指示编译器只进行编译预处理，下面的命令将预处理源码文件`hello.c`并将结果在标准输出中输出：
```
gcc -E hello.c
```
选项`-o`用来将预处理的代码定向到一个文件，不需经过预处理的`C`源码文件保存在后缀为`.i`的文件中:
```
gcc -E hello.c -o hello.i
```

**NOTE:**
对于`C++`来说，一般情况下使用`g++`,而对于`C++`来说，预处理过的文件后缀为`.ii`
```
g++ -E hello.cpp -o hello.ii
```

## 生成汇编代码
选项-S指示编译器生成编译器生成汇编代码然后结束：
```
gcc -S hello.c
```

## 创建静态库
静态库是编译器生成的普通的`.o`文件的集合
链接一个程序时用库中的对象文件还是目录中的对象文件都是一样的
静态库的另一个名字叫归档文件(`archive`)，管理这种归档文件的工具叫`ar`
**要创建一个库，首先要编译出库中需要的对象模块**
```
gcc -c -Wall hello.c test.c
```
此时会生成`hello.o`和`test.o`，**之后用程序`ar`配合参数`-r`可以创建一个新库并将对象文件插入**，如果库不存在的话，参数`-r`将创建一个新的，并将对象模块添加到归档文件中：
```
ar -r libhello.a hello.o test.o
```
这之后便可使用这一静态库
```
gcc -Wall world.c -lhello -o world
```
也可以通过`-L`指定寻找库的具体路径
```
gcc -Wall -L ./lib/ world.c -lhello -o world
```
上面这条命令是告诉编译器去`./lib/`下寻找`libhello.a`

##创建共享库(动态库)
共享库是编译器以一种特殊的方式生成的对象文件的集合
**对象文件模块中所有地址（变量引用或函数调用）都是相对而不是绝对的，这使得共享模块可以在程序的运行过程中被动态地调用和执行**
```
gcc -c -Wall -fpic hello.c world.c
```
选项`-c`告诉编译器只生成`.o`的对象文件
**选项`-fpic`使生成的对象模块采用浮动的（可重定位的）地址**（`pic`表示位置无关---position independent code）
下面的`gcc`命令将对象文件构建成一个名为`hello.so`的共享库
```
gcc -Wall -shared hello.o world.o -o hello.so
```
通常情况下，**链接器定位并使用`main()`函数作为程序的入口**，但是对于上述的模块来说并没有这个函数，所以为了抑制错误采用`-shared`选项
**NOTE:**
编译器能将后缀为 `.c` 的文件识别为 `C` 语言源代码文件，并知道如何将其编译成为对象文件。基于这一点，先前的两条命令我们可以合并为一条;下面的命令直接将模块编译并存储为共享库：
```
gcc -Wall -fpic -shared hello.c world.c - o hello.so
```

要使用共享库的话，使用如下命令：
```
gcc -Wall test.c hello.so -o test
```
程序`test`已经完成，但要运行它必须让其能定位到共享库`hello.so`，因为库中的函数要在程序运行时被加载。
需要注意的是，当前工作目录可能不在共享库的查找路径中，因此需要使用如下的命令行设定环境变量`LD_LIBRARY_PATH：`
`export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:./`

## 超越命名惯例
通常，在没有`-x`选项的情况下，任何具有未知后缀名的源码文件名都被认为是链接器可以识别的选项，并在不做任何更改的情况下传递给链接器
选项`-x`对其後的所有未知后缀的文件都起作用。例如，下面的命令使`gcc`将`align.zzz`和`types.xxx`都作为`C`源码文件来处理
```
gcc -c -xc align.zzz types.xxx
```

## C与C++混合编程
### C++调用C函数
```
//cpp2c.cpp
#include <iostream>
extern "C" void csayhello(char *str);
int main(int argc,char *argv[])
{
    csayhello("Hello from cpp to c");
}
```
`C`函数不需要任何特殊处理，代码如下：
```
/* csayhello.c */
#include <stdio.h>
void csayhello(char *str)
{
    printf("%s\n",str);
}
```
下面的三条命令编译以上两个文件并链接成为一个可执行文件:
```
g++ -c cpp2c.cpp -o cpp2c.o
gcc -c csayhello.c -o csayhello.o
gcc cpp2c.o csayhello.o -lstdc++ cpp2c
```
最普遍的做法是，将函数声明放到头文件中，然后将所有内容包含在 extern "C" 声明块内。文件内容像下面所示：
```
extern "C" {
    int mlimitav(int lowend, int highend);
    void updatedesc(char *newdesc);
    double getpct(char *name);
};
```
### C调用C++函数
下面的代码是在`c++`源文件中定义相关`C`函数：
```
/* cppsayhello.cpp */
#include <iostream>
extern "C" void cppsayhello(char *str);
void cppsayhello(char *str)
{
    std::cout << str << "\n";
}
```
尽管函数`cppsayhello()`通过`extern "C"`声明为`C`函数，事实上它是` C++ `源代码的一部分，这意味着函数体内是真正的` C++ `代码。**在函数内你可以自由地创建和析构对象**
如果你要在 `cppsayhello()`内调用`C`函数的话，将其声明为`extern "C"`是必须的。否则，编译器会将其作为一个`C++`函数并相应地更改函数名
下面是调用`C++`函数`cppsayhello()`的`C`程序：
```
/* c2cpp.c */
int main(int argc,char *argv[])
{
    cppsayhello("Hello from C to C++");
    return(0);
}
```
下面的命令编译并链接生成`c2cpp`：
```
g++ -c cppsayhello.cpp -o cppsayhello.o
gcc -c c2cpp.c -o c2cpp.o
gcc cppsayhello.o c2cpp.o -lstdc++ -o c2cpp
```
