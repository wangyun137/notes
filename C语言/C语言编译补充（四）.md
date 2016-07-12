# C语言编译补充

## 调试选项

### -g
+ 以操作系统的本地格式（stabs, COFF, XCOFF，DWARF）产生调试信息，`GDB`能够使用这些调试信息
+ 和大多数`C`编译器不同，`GNU CC`允许结合使用`-g`和`-O`选项
如果`GNU CC`支持输出多中调试信息，下面的选项非常有用：

+ -ggdb
以本地格式（如果支持）输出调试信息，尽可能包括`GDB`扩展
+ -gstabs
以`stabs`格式（如果支持）输出调试信息，不包括`GDB`扩展，这是大多数`BSD`系统上`DBX`使用的格式
+ -gstabs+
以`stabs`格式（如果支持）输出调试信息，使用只有GNU调试器（GDB）理解的GNU扩展.使用这些扩展有可能导致 其他调试器崩溃或拒绝读入程序
+ -gcoff
以`COFF`格式（如果支持）输出调试信息，这是在`System V`第四版以前的大多数`System V`系统上`SDB`使用的 格式.
+ -gxoff
以`XCOFF`格式（如果支持）输出调试信息,这是`IBM RS/6000`系统上`DBX`调试器使用的格式
+ -gxcoff+
以`XCOFF`格式（如果支持）输出调试信息,使用只有GNU调试器(`GDB`)理解的GNU扩展.使用这些扩展有可能导致 其他调试器崩溃或拒绝读入程序
+ -gdwarf
以`DWARF`格式（如果支持）输出调试信息.这是大多数`System V`第四版系统上`SDB`使用的格式.
+ -gdwarf+
以`DWARF`格式（如果支持）输出调试信息,使用只有`GNU`调试器（GDB）理解的GNU扩展.使用这些扩展有可能导致 其他调试器崩溃或拒绝读入程序

### -glevel, -ggdblevel, -gstabslevel, -gcofflevel, -gxofflevel, -gdwarflevel
+ 请求生成调试信息,同时用level指出需要多少信息.默认的level值是2

### -p
+ 产生额外代码，用于输出`profile`信息，供分析程序`prof`使用

### -pg
+ 产生额外代码，用于输出`profile`信息，供分析程序`gprof`使用

### -a
+ 产生额外代码，用于输出基本块（`basic block`）的`profile`信息，它记录各个基本块的执行次数，供诸如`tcov`此类的程序使用，注意，这个数据格式并非`tcov`期待的.最终`GNU gprof`将处理这些数据

### -ax
+ 产生额外代码，用于从`bb.in`文件读取基本块的`profile`，把`profile`的结果写到`bb.out`文件，`bb.in`包含一张函数列表，一旦进入列表中的某个函数，`profile`操作就开始，离开最外层的函数后，profile操作就结束.以`-`为前缀名的函数排除在profile操作之外.如果函数名不是唯一的,它可以写成 `/path/filename.d:functionname`来澄清. `bb.out`将列出一些有效的文件名.这四个函数名具有 特殊含义: `__bb_jumps__`导致跳转(jump)频率写进`bb.out`， `__bb_trace__`导致基本块序列通过 管道传到`gzip`,输出`bbtrace.gz`文件. `__bb_hidecall__`导致从跟踪(trace)中排除call 指令. `__bb_showret__`导致在跟踪中包括返回指令

### -dletters
+ 编译的时候，在`letters`指定的时刻做调试转储`dump`，用于调试编译器，大多数转储的文件名，通过源文件名添加字词获得（例如`foo.c.rtl`或`foo.c.jump`）

### -dM
+ 预处理结束的时候转储所有的宏定义，不输出到文件

### -dN
+ 预处理结束的时候转储所有的宏名

### -dD
+ 预处理结束的时候转储所有的宏定义，同时进行正常输出

### -dy
+ 语法分析（`parse`）的时候在标准错误转出调试信息

### -dr
+ `RTL`阶段后转储到`file.rtl`

### -dx
+ 仅对函数生成`RTL`,而不是编译，通常和`r`联用

### -dj
+ 第一次跳转优化后转储到`file.jump`

### -ds
+ `CSE`（包括有时候跟在`CSE`后面的跳转优化）后转储到`file.cse`

### -dL
+ 循环优化后转储到`file.loop`

### -dt
+ 第二次`CSE`处理(包括有时候跟在`CSE`后面的跳转优化)后转储到`file.cse2`

### -df
+ 流程分析（`flow analysis`）后转储到`file.flow`

### -dc
+ 指令组合（`instruction combination`）后转储到`file.combine`

### -dS
+ 第一次指令安排（`instruction schedule`）后转储到`file.sched`

### -dl
+ 局部寄存器分配后转储到`file.lreg`

### -dg
+ 全局寄存器分配后转储到`file.greg`

### -dR
+ 第二次指令安排（`instruction schedule`）后转储到`file.sched2`

### -dJ
+ 最后以此跳转优化后转储到`file.jump2`

### -dd
+ 推迟分支调度后转储到`file.dbr`

### -dk
+ 寄存器-堆栈转换后转储到`file.stack`

### -dm
+ 运行结束后，在标准错误显示内存使用统计

### -dp
+ 在汇编输出加注指明使用了哪些模式及其替代模式

### -fpretend-float
+ 交叉编译的时候，假定目标和宿主机使用同样的浮点格式，它导致输出错误的浮点常数，但是在目标机商运行的时候，真实的指令序列有可能和`GNU CC`希望的一样

### -save-temps
+ 保存那些通常是临时的中间文件，置于当前目录下，并且根据源文件命名，因此，用`-c -save-temps`选项编译`foo.c`会变成`foo.cpp`和`foo.s`以及`foo.o`文件

### -print-file-name=[library]
+ 显示库文件`library`的全路径，连接时会使用这个库，其他什么是请也不做，根据这个选项，`GNU CC`既不编译，也不连接，仅仅显示文件名

### -print-libgcc-file-name
+ 和`-print-file-name=libgcc.a`一样

### -print-prog-name=[program]
+ 类似于`-print-file-name`,但是查找程序program如`cpp`


## 优化选项

### -O, -O1
+ 优化,对于大函数,优化编译占用稍微多的时间和相当大的内存
+ 不使用`-O`选项时，编译器的目标是减少编译的开销，使编译结果能够调试，语句是独立的:如果在两条语句之间用断点中止程序,你可以对任何变量重新赋值,或者在函数体内把程序计数器指到其他语句,以及从源程序中精确地获取你期待的结果
+ 不使用`-O`选项时,只有声明了`register`的变量才分配使用寄存器.编译结果比不用 `-O`选项的`PCC`要略逊一筹
+ 使用了`-O`选项,编译器会试图减少目标码的大小和执行时间
+ 如果指定了`-O`选项, `-fthread-jumps`和`-fdefer-pop`选项将被 打开.在有`delay slot`的机器上, `-fdelayed-branch`选项将被打开.在即使没有帧指针(`frame pointer`)也支持调试的机器上, `-fomit-frame-pointer`选项将被打开.某些机器上 还可能会打开其他选项

### -O2
+ 多优化一些.除了涉及空间和速度交换的优化选项,执行几乎所有的优化工作，例如不进行循环展开(`loop unrolling`)和函数内嵌(`inlining`)，和`-O`选项比较,这个选项既增加了编译时间,也提高了生成代码的运行效果

### -O3
+ 优化的更多.除了打开`-O2`所做的一切,它还打开了`-finline-functions`选项

### -O0
+ 不优化
+ 如果指定了多个-O选项,不管带不带数字,最后一个选项才是生效的选项

### -ffloat-store
+ 不要在寄存器中存放浮点变量，这样可以防止某些机器上不希望的过高精度
+ 对于大多数程序,过高精度只有好处.但是有些程序严格依赖于`IEEE`浮点数的定义.对这样的程序可以使用 `-ffloat-store`选项

### -fmemolize-lookups -fsave-memoized
+ 使用探索法(`heuristic`)进行更快的编译(仅对C++).默认情况下不使用探索法.由于探索法只对某些输入文件有效,其他程序的编译速度会变得更慢

### -fno-default-inline
+ 默认为不要把成员函数内嵌,因为它们定义在类的作用域内(仅C++)

### -fno-defer-pop
+ 一旦函数返回,参数就立即弹出.对于那些调用函数后必须弹出参数的机器,编译器一般情况下让几次函数调用的参数堆积在栈上,然后一次全部弹出

### -fforce-mem
+ 做数学运算前把将要使用的内存操作数送入寄存器，通过把内存访问转换成潜在的公共子表达式,它可能产生较好的目标码，如果它们不是公共子表达式,指令组合应该消除各自的寄存器载荷

### -fforce-addr
+ 做数学运算前把将要使用的内存地址常数送入寄存器.它可能和`-fforce-mem`一样产生较好的目标码

### -fomit-frame-pointer
+ 对于不需要帧指针(`frame pointer`)的函数,不要在寄存器中保存帧指针.这样能够避免保存,设置和恢复帧指针的指令;同时对许多函数提供一个额外的寄存器

### -finline-functions
+ 把所有简单的函数集成进调用者.编译器探索式地决定哪些函数足够简单,值得这种集成

### -fcaller-saves
+ 允许在寄存器里分配数值，但是这个方案通常受到各个函数调用的冲击，因此GCC生成额外的代码,在函数调用的前后保存和复原寄存器内容，仅当生成代码看上去优于反之结果时才实现这样的分配

### -fkeep-inline-functions
+ 即使集成了某个函数的所有调用,而且该函数声明为`static`,仍然输出这个函数一个独立的,运行时可调用的版本

### -fno-function-cse
+ 不要把函数地址存入寄存器;让调用固定函数的指令显式给出函数地址
+ 这个选项产生效率较低的目标码,但是如果不用这个选项,某些不寻常的hack,改变汇编器的输出,可能因优化而带来困惑

### -fno-peephole
+ 禁止任何机器相关的`peephole`优化

### -ffast-math
+ 这个选项出于速度优化,允许`GCC`违反某些ANSI或IEEE规则/规格.例如,它允许编译器假设`sqrt`函数的参数是非负数

**下列选项控制特定的优化. `-O2`选项打开下面的大多数优化项,除了`-funroll-loops`和 `-funroll-all-loops`项
而`-O`选项通常打开`-fthread-jumps`和`-fdelayed-branch`优化项,但是特定的机器上的默认优化项有可能改变**

### -fstrength-reduce
+ 执行循环强度缩小（`loop strength reduction`）优化，并且消除重复变量

### -fthread-jumps
+ 执行优化的地点时，如果某个跳转分支的目的地存在另一个条件比较,而且该条件比较包含在前一个比较语句之内,那么执行优化.根据条件是`true`或者`false`,前面那条分支重定向到第二条分支的目的地或者紧跟在第二条分支后面

### -funroll-loops
+ 执行循环展开(`loop unrolling`)优化，仅对循环次数能够在编译时或运行时确定的循环实行

### -fcse-follow-jumps
+ 在公共子表达式消元(`common subexpression elimination`)的时候,如果没有其他路径到达某个跳转的目的地,就扫过这条`jump`指令.例如,如果`CSE`遇到带有`else`从句的`if`语句,当条件测试为`false`时, `CSE`就跟在`jump`后面

### -fcse-skip-blocks
+ 它类似于`-fcse-follow-jumps`选项，但是`CSE`跟在条件跳转后面,条件跳转跳过了语句块(`block`).如果`CSE`遇到一条简单的`if`语句,不带`else`从句, `-fcse-skip-blocks`选项将导致`CSE`跟在`if`产生的跳转后面

### -frerun-cse-after-loop
+ 执行循环优化后，重新进行公共子表达式消元

### -felide-constructors
+ 如果看上去合理就省略构造函数（仅`C++`）,根据这个选项，对于下面的代码, GNU C++直接从调用foo 初始化y,而无需通过临时变量:
```
A foo();
A y = foo();
```
+ 如果没有这个选项, `GNU C++首`先通过调用类型`A`合适的构造函数初始化`y`;然后把`foo`的结果赋给临时变量;最后,用临时变量替换`y`的初始值

### -fexpensive-optimizations
+ 执行一些相对开销较大的次要优化

### -fdelayed-branch
+ 如果对目标机支持这个功能,它试图重新排列指令,以便利用延迟分支(`delayed branch`)指令后面的指令空隙

### -fschedule-insns
+ 如果对目标机支持这个功能,它试图重新排列指令,以便消除因数据未绪造成的执行停顿.这可以帮助浮点运算或内存访问 较慢的机器调取指令,允许其他指令先执行,直到调取指令或浮点运算完成

### -fschedule-insns2
+ 类似于`-fschedule-insns`选项,但是在寄存器分配完成后,需要一个额外的指令调度过程.对于寄存器数目相对较少,而且取内存指令大于一个周期的机器,这个选项特别有用


## 代码生成选项
下面的选项和平台无关,用于控制目标码生成的接口约定

### -fnonnull-objects
+ 假设通过引用`reference`取得的对象不为`null`（仅为`C++`）
+ 一般说来, `GNU C++`对通过引用取得的对象作保守假设.例如,编译器一定会检查下似代码中的`a`不为`null`:`obj &a = g (); a.f(2)`;检查类似的引用需要额外的代码,然而对于很多程序是不必要的.如果你的程序不要求这种检查,你可以用 `-fnonnull-objects`选项忽略它

### -fpcc-struct-return
+ 函数返回`struct`和`union`值时,采用和本地编译器相同的参数约定, 对于较小的结构, 这种约定的效率偏低,而且很多机器上不能重入;它的优点是允许GCC编译的目标码和PCC编译的目标码互相调用

### -freg-struct-return
+ 一有可能就通过寄存器返回`struct`和`union`函数值.对于较小的结构,它比`-fpcc-struct-return`更有效率
+ 如果既没有指定`-fpcc-struct-return` ,也没有指定`-freg-struct-return`, GNU CC默认使用目标机的标准约定.如果没有标准约定, GNU CC默认采用`-fpcc-struct-return`

### -fshort-enums
+ 给`enum`类型只分配它声明的值域范围的字节数，就是说,`enum`类型等于大小足够的最小整数类型

### -fshort-double
+ 使`double`类型的大小和`float`一样

### -fshared-data
+ 要求编译结果的数据和非`const`变量是共享数据,而不是私有数据.这种差别仅在某些操作系统上面有意义, 那里的共享数据在同一个程序的若干进程间共享,而私有数据在每个进程内都有副件

### -fno-common
+ 即使未初始化的全局变量也分配在目标文件的`bss`段,而不是把它们当做普通块(`common block`)建立.这样的 结果是,如果在两个不同的编译结果中声明了同一个变量(没使用`extern`),连接它们时会产生错误. 这个选项可能有用的唯一情况是,你希望确认程序能在其他系统上运行,而其他系统总是这么做

### -fno-ident
+ 忽略`#ident`指令

### -fno-gnu-linker
+ 不要把全局初始化部件(如C++的构造子和解构子)输出为`GNU`连接器使用的格式(在`GNU`连接器是标准方法的系统上).当你打算使用非GNU连接器的时候可以用这个选项,非GNU连接器也需要`collect2`程序确保系统连接器 放入构造函数(`constructor`)和解构子(`destructor`). (GNU CC的发布包中包含有`collect2`程序.)对于必须使用`collect2`的系统,编译器驱动程序`gcc`自动配置为这么做

### -finhibit-size-directive
+ 不要输出.size汇编指令,或其他类似指令,当某个函数一分为二,两部分在内存中距离很远时会引起问题. 当编译`crtstuff.c`时需要这个选项;其他情况下都不应该使用

### -fverbose-asm
+ 输出汇编代码时放些额外的注释信息.这个选项仅用于确实需要阅读汇编输出的时候(可能调试编译器自己的时候)

### -fvolatile
+ 使编译器认为所有通过指针访问的内存是易变内存(`volatile`)

### -fvolatile-global
+ 使编译器认为所有的外部和全局变量是易变内存

### -fpic
+ 如果支持这种目标机,编译器就生成位置无关目标码，适用于共享库(`shared library`)

### -fPIC
+ 如果支持这种目标机,编译器就输出位置无关目标码.适用于动态连接(`dynamic linking`),即使分支需要大范围转移

### -ffixed-reg
+ 把名为`reg`的寄存器按固定寄存器看待(`fixed register`);生成的目标码不应该引用它(除了或许用作栈指针,帧指针,或其他固定的角色)

### -fcall-used-reg
+ 把名为`reg`的寄存器按可分配寄存器看待,不能在函数调用间使用.可以临时使用或当做变量使用,生存期不超过一个函数.这样编译的函数无需保存和恢复`reg`寄存器

### -fcall-saved-reg
+ 把名为`reg`的寄存器按函数保护的可分配寄存器看待.可以临时使用或当做变量使用,它甚至能在函数间 生存.这样编译的函数会保存和恢复使用中的`reg`寄存器



## PRAGMAS
GNU C++支持两条`#pragma`指令使同一个头文件有两个用途:对象类的接口定义, 对象类完整的内容定义

### #pragam interface
+ (仅对`C++`)在定义对象类的头文件中,使用这个指令可以节省大部分采用该类的目标文件的大小.一般说来,某些信息（内嵌成员函数的备份副件,调试信息,实现虚函数的内部表格等）的本地副件必须保存在包含类定义的各个目标文件中.使用这个 `pragma`指令能够避免这样的复制.当编译中引用包含`#pragma interface`指令的头文件时,就 不会产生这些辅助信息(除非输入的主文件使用了`#pragma implementation`指令).作为替代,目标文件 将包含可被连接时解析的引用(`reference`)

### #pragma implementation  #pragma implementation "objects.h"
+ (仅对C++)如果要求从头文件产生完整的输出(并且全局可见),你应该在主输入文件中使用这条pragma.头文件 中应该依次使用`#pragma interface`指令，在`implementation`文件中将产生全部内嵌成员函数的备份,调试信息,实现虚函数的内部表格等
+ 如果`#pragma implementation`不带参数,它指的是和源文件有相同基本名的包含文件;例如, `allclass.cc`中, `#pragma implementation`等于`#pragma implementation allclass.h`.如果某个`implementation`文件需要从多个头文件引入代码,就应该 使用这个字符串参数
+ 不可能把一个头文件里面的内容分割到多个`implementation`文件
