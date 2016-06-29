# C语言宏的用法

## 标示符别名
`#define BUFFER_SIZE 1024`
预处理阶段，`foo = (char *)malloc(BUFFER_SIZE);`会被替换成`foo = (char *)malloc(1024);`

宏体换行需要在行末加反斜杠`\`
``````
#define Numbers 1, \
                2, \
                2
``````
预处理阶段`int x[] = {NUMBERS};`会被扩展成`int x[] = {1, 2, 3};`

## 宏函数
宏名称之后带括号的宏被认为是宏函数。在预处理阶段，宏函数会被展开。
优点是没有普通函数保存寄存器和参数传递的开销，展开后的代码有利于CPU cache的利用和指令预测，速度快。缺点是科执行代码体积大
`#define min(x, y) ((x) < (y) ? (x) : (y))`
`y = min(1, 2);`会被扩展成`y = ((1) < (2) ? (1) : (2));`

## 宏的特殊用法

###字符串化
在宏体中，如果宏参数前加个`#`,那么在宏体展开的时候，宏参数会被扩展成字符串的形式，如：
``````
#define WARN_IF(EXP) \
    dp { if (EXP) \
            fprintf(stderr, "Warning: "#EXP" \n");} \
    while(0)
``````
`WARN_IF (x == 0);`会被扩展成:
``````
do {
    if (x == 0)
        fprintf(stderr, "Warning: "x == 0" \n");}
while(0)
``````
这种用法可以用在`assert`中，如果断言失败，可以将失败的语句输出到反馈信息中

### 连接
在宏体中，如果宏体所在标示符有`##`，那么在宏体扩展的时候，宏参数会被直接替换到标示符中。
``````
#define COMMAND(NAME) {#NAME, NAME ## _command}

struct command
{
    char *name;
    void (*function)(void);
};
``````
在宏展开的时候
``````
struct command commands[] =
{
    COMMAND (quit),
    COMMAND (help),
    ...
}
``````
会被扩展成：
``````
struct command commands[] =
{
    {"quit", quit_command},
    {"help", help_command},
    ...
}
``````

## 需要主意的地方
1. **语法问题**
由于是纯文本替换，预处理器不对宏体做任何语法检查，比如少括号，少分号这些问题与处理器是不管的
2. **算符优先级**
不仅宏体是纯文本替换，宏参数也是纯文本替换。
``````
#define MULTIPLY(x, y) x * y
``````
假设现在`MULTIPLY(1+2, 3)`则会被展开为`1 + 2 * 3`则算法优先级会出错，应当在宏体中，给引用的参数加括号
``````
#define MULTIPLY(x, y) (x) * (y)
``````
此时就会被正常展开成为`(1+2) * 3`
3. **分号吞噬问题**
``````
#define SKIP_SPACES(p, limit) \
    { char *lim = (limit);    \
      while (p < lim) {       \
        if (*p++ != ' ') {    \
          p--; break;}}}      

if (*p != 0)
  SKIP_SPACES(p, lim);
else
  ...
``````
编译这段代码，GCC报`error: 'else' without a previous 'if'`。原因是这个看似是一个函数的宏被展开后是一段大括号括起来的代码块
加上分号之后这个`if`逻辑块就结束了，所以编译器发现这个`else`没有对应的`if`,这个问题一般用`do...while(0)`的形式解决：
``````
#define SKIP_SPACES(p, limit) \
    do {                      \
      char *lim = (limit);    \
      while (p < lim) {       \
        if (*p++ != ' ') {    \
          p--;break;          \
        }}}while(0)           
``````
展开后就变成了
``````
if (*p != 0)
  do ... while (0);
else ...
``````
这样就消除了分好吞噬问题
4. **宏参数重复调用**
`#define min(X, Y) ((X))`
当有如下调用：`next = min(x+y, foo(z));`宏体被展开成`next=((x+y) < (foo(z)) ? (x + y) : (foo(z)));`
可以看到，`foo(z)`被重复调用了两次，做了重复计算。更为严重的是，如果`foo`是不可重入的即`foo`内部修改了全局或静态变量，程序会产生逻辑错误
所以尽量不要在宏参数中传入函数调用
5. **对自身的递归引用**
`#define foo (4 + foo)`
按前面的理解， `(4 + foo)`会展开成`(4 + (4 + foo))`,然后一直展开下去，直至内存耗尽。但是，预处理器采取的策略是**只展开一次**
也就是,`foo`只会展开成`(4 + foo)`，而展开之后`foo`的含义就要根据上下文来确定
对于以下的**交叉引用**，宏体也只会展开一次
``````
#define x (4 + y)
#define y (x * x)
``````
`x`展开成`(4 + y) -> (4 + (2 * x))`, `y`展开成`(2 * x) -> (2 * (4 + y))`
**这是极不推荐的写法**
