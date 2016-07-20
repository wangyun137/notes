# Activity LaunchMode
Activity的启动模式总共有四种，分别是：

+ `standard` 标准模式
+ `singleTop` 栈顶复用模式
+ `singleTask` 栈内复用模式
+ `singleInstance` 单例模式

## standard 标准模式
如果没有在Manifest文件中明确指定`Activity`的`android:launchMode`，则系统默认该`Activity`就是标准模式，标准模式下，每次启动一个`Activity`，系统都会为这个`Activity`创建一个实例，无论是否已经有相应的实例存在。
> NOTE:这种模式下，被启动的`Activity`会属于启动它的那个`Activity`的任务栈，即假设A启动了B,则B属于A的任务栈
> NOTE:如果用`ApplicationContext`去启动`Activity`会报错，因为`ApplicationContext`没有相应的任务栈，解决办法是给要启动的`Activity`加上`FLAG_ACTIVITY_NEW_TASK`

## singleTop 栈顶复用模式
如果新的`Activity`已经位于任务栈的栈顶，则不会创建新的实例，而是调用它的`onNewIntent`方法；但如果该`Activity`实例已经存在但并不是在栈顶，则系统仍然会重新创建新实例

## singleTask 栈内复用模式
只要`Activity`在栈内存在，则回调其`onNewIntent`方法，并不会创建实例，同时自动`clearTop`，即将位于该`Activity`上面的`Activity`都出栈。如果一个`Activity`带有`singleTask`属性，则它在启动时，系统会首先查询其需要的任务栈是否存在，如果不存在，则重新创建一个任务栈，并将该`Activity`压入栈中，如果存在，则`clearTop`并调用`onNewIntent`方法。
> 关于`singleTask`，有一个属性与它紧密相关，就是`taskAffinity`，该参数标识了一个`Activity`所需要的任务栈的名称，默认情况下，所有`Activity`所需的任务栈都是与程序包名相同，可以单独指定，指定的名字不能和包名相同，否则相当于没指定
> `taskAffinity`属性一般还和`allowTaskReparenting`属性结合使用，举例来说，假设应用A启动了应用B中的一个`Activity` C，此时C运行在应用A的任务栈中，但如果C的`allowTaskReparenting = true`,则在这种情况下，启动C以后，点击Home回到桌面，再次启动应用B,此时显示的是`Activity` C， 这是因为，当B启动后，B会创建属于自己的任务栈，此时，系统发现C所需要的任务栈已经创建，就把C从应用A的任务栈中转移到了应用B的任务栈

## singleInstance 单例模式
这是一种加强的`singleTask`模式，它除了具有`singleTask`的一切特点外，还有一个特点就是该模式的`Activity`只能单独的存在于一个任务栈中，即此任务栈只能有这一个`Activity`

## 优先级
启动模式可以通过在Manifest中指定也可以通过代码指定，相比较而言，用代码指定的启动模式优先级更高
