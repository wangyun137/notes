# C语言编译补充

两个比较常用的选项
1. `-w`
`-w`选项告诉编译器关闭所有警告信息
2. `-v`
`-v`选项告诉编译器列出所有警告信息


## 选项
选项必须分别给出：`-dr`完全不同于`-d`和`-r`
下面是选项的类别：

- 总体选项
- 语言选项
- 警告选项
- 调试选项
- 优化选项
- 预处理选项
- 汇编器选项
- 连接器选项
- 目录选项
- 目录机选项
- 配置相关选项
- 代码生成选项

## 总体选项
总体选项包含`-x, -c, -S, -E, -o, -v, -pipe`，`-pipe`是指在编译过程的不同阶段使用管道而非临时文件进行通信，这个选项在某些系统上无法工作
其他选项在**C语言编译.md**中都有说明

## 语言选项
下列选项控制编译器能够接受的`C`'方言':
### -ans`:
+ 支持符合`ANSI`标准的`C`程序
+ 这样就会关闭`GNU C`中某些不兼容`ANSI C`的特性，例如`asm`,`inline`,`typeof`关键字，以及诸如`unix`和`vax`这些表明当前系统类型的预定义宏，同时开启不受欢迎和极少使用的`ANSI trigraph`特性，以及禁止`$`成为标识符的一部分
+ 尽管使用`-ansi`选项，以下关键字`__asm__, __extension__, __inline__, __typeof__`仍然有效
+ 使用`-ansi`选项不会让编译器拒绝编译非`ANSI`程序，除非增加`-pedantic`选项
+ 使用`-ansi`时，预处理器会预定义一个`__STRICT_ANSI__`宏，有些头文件会关注此宏，以避免声明某些函数

### -fno-asm
+ 不把`asm, inline,typeof`当做关键字，这些词可以用作标识符,`__asm__，　__extension__, __typeof__`可以替代它们，`-ansi`隐含声明了`-fno-asm`

### -fno-builtin
+ 不接受　**非两个下划线开头的内建函数(built-in function)**,　目前受影响的函数有`_exit, abort, abs, alloca, cos, exit, fabs, labs,memcpm, memcpy, sin, sqrt, strcmp, strcpy, strlen`
+ `-ansi`能够组织`alloca,_exit`成为内建函数

### -fhosted
+ 按宿主环境编译，隐含声明了`-fbuiltin`选项，而且警告不正确的`main`涵书声明

### -ffreestanding
+ 按独立环境编译，隐含声明了`-fno-builtin`,而且对`main`函数没有特别要求

### -fno-strict-prototype
+ 对于没有参数的函数声明如`int foo();`，按照`C`风格处理（即不说明参数个数或类型），正常情况下，这样的函数在`C++`中意味着参数为空

### -trigraphs
+ 支持`ANSI C trigraphs`, `-ansi`选项隐含声明了`-trigraphs`

### -traditional
+ 试图支持传统`C`编译器的某些方面
+ 对于`C++`程序，`-traditional`选项带来一个附加效应，允许对`this`赋值，他和`-fthis-is-variable`选项的效果一样

### -traditional-cpp
+ 试图支持传统`C`预处理器的某些方面，但是不包括`-traditional`选项的其他效应

### -fdollars-in-identifiers
+ 允许在标识符中使用`$`字符（仅针对`C++`）,可以制定`-fno-dollars-in-idetifiers`选项明显禁止使用`$`符

### -fenum-int-equiv
+ 允许`int`型到枚举类型`enum`的隐式转换（仅针对`C++`），正常情况下，`C++`允许`enum`到`int`的隐式转换，反之不行

### -fexternal-templates
+ 为模板声明(template declaration)产生较小的代码（仅针对`C++`）,对于每个模板函数，只在定义他们的地方生成一个副本，想要成功使用这个选项，必须在所有使用模板的文件中，标记`#pragma implemention'`(定义)或这`#pragma interface`（声明）
+ 当程序使用这个选项时，模板实例（template instantiation）全部是外部实例，**你必须让需要的实例在实现文件中出现**,可以通过`typedef`实现这一点。相对应的，如果编译时使用`-fno-external-templates`，所有模板实例明确设为内置

### -fall-virtual
+ 所有可能的成员函数默认为虚函数，所有的成员函数（除了构造子函数和`new,delete`成员操作符）视为所在类的虚函数
+ 这不表明每次调用成员函数都将通过内部虚函数表.有些情况下,编译器能够判断出可以直接调用某个虚函数;这时就 直接调用

### -fcond-mismatch
+ 允许条件表达式的第二个和第三个参数的类型不匹配，这种表达式的值是`void`

### -fthis-is-variable
+ 允许对this赋值(仅对C++).合并用户自定义的自由存储管理机制到C++后,使可赋值的 `this`显得不合时宜.因此,默认情况下,类成员函数内部对this赋值是无效操作.然而为了 向后兼容,你可以通过`-fthis-is-variable`选项使这种操作有效

### -funsigned-char
+ 把`char`定义为无符号型，如同`unsigned char`
+ 各种机器都有自己的默认`char`类型，可能是`signed char`，也可能是`unsigned char`
+ 理想情况下,当依赖于数据的符号性时,一个可移植程序总是应该使用`signed char`或`unsigned char`,但是许多程序已经写成只用简单的`char`,并且期待这是有符号数(或者无符号数,具体情况取决于 编写程序的目标机器),这个选项,和它的反义选项,使那样的程序工作在对应的默认值上
+ `char`的类型始终应该明确定义为`signed char`或`unsigned char`,即使它表现的和其中之一完全一样

### -fsigned-char
+ 把`char`定义为有符号类型,如同`signed char`.
+ 这个选项等同于`-fno-unsigned-char`
+ 同样, `-fno-signed-char`等价于 `-funsigned-char`.

### -fsigned-bitfields, -funsigned-bitfields, -fno-signed-bitfields, -fno-unsigned-bitfields
+ 如果没有明确声明`signed`或`unsigned`修饰符,这些选项用来定义有符号位域 (`bitfield`)或无符号位域.缺省情况下,位域是有符号的,因为他们继承的基本整数类型,如`int`,是有符号数
+ 然而,如果指定了`-traditional`选项,位域永远是无符号数.

### -fwritable-strings
+ 把字符串常量存储到可写数据段,而且不做特别对待.这是为了兼容一些老程序,他们假设字符串常量是可写的. `-traditional`选项也有相同效果
