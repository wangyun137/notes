# Android mk

`NDK`构建系统预留了以下的变量，在自定义变量时注意不要跟这些系统变量冲突
+ 以`LOCAL_`开头的
+ 以`PRIVATE_, NDK_, APP`开头的
+ 小写字母的如`my-dir`

## LOCAL_PATH
每个`Android.mk`文件必须由`LOCAL_PATH`开始，用于在`development tree`中查找源文件

## include $(CLEAR_VARS)
`CLEAR_VARS`指向一个指定的`GNU Makefile`，并清理许多`LOCAL_XXX`变量，比如`LOCAL_MODULE, LOCAL_SRC_FILES`等，**它并不清理`LOCAL_PATH`**。因为所有的编译控制文件由同一个`GNU Makefile`解析和执行，其变量是全局的，所以必须清理后才能避免相互影响

## LOCAL_MODULE
每一个模块的名称必须是唯一的且不能包含任何空格。系统在构建`shared-library`时，会自动加上前缀`lib`和后缀`.so`
>如果模块名称本身已经是以`lib`开头，则系统不会再加入前缀，只是会加入后缀

## LOCAL_SRC_FILES
指明当前模块的源文件（C/C++文件）
缺省的C++源码的扩展名为`.cpp`. 也可以修改，通过`LOCAL_CPP_EXTENSION`修改

## BUILD_SAHRED_LIBRARY
该变量指向一个构建脚本并收集所有通过`LOCAL_XXX`变量指定的关于当前模块的信息，并根据你所列出的源文件构建一个动态库（.so）
使用该变量前，你应该至少定义了`LOCAL_MODULE`和`LOCAL_SRC_FILES`
用法是：
```
include $(BUILD_SAHRED_LIBRARY)
```

## BUILD_STATIC_LIBRARY
用来构建一个静态库（.a），用法是：
```
include $(BUILD_STATIC_LIBRARY)
```

## PREBUILT_SHARED_LIBRARY
用来指定一个预先已经编译好的动态库，与`BUILD_SAHRED_LIBRARY`和`BUILD_STATIC_LIBRARY`不同，该模块对应的`LOCAL_SRC_FILES`不能是源文件，而只能是一个已经编译好的的动态库的路径，如`foo/libfoo.so`，用法是：
```
include $(PREBUILT_SHARED_LIBRARY)
```

## PREBUILT_STATIC_LIBRARY
用来指定一个预先已经编译好的静态库

## TARGET_ARCH
指定目标`CPU`架构，对于大多数兼容`ARM`指令的构建，通常使用`arm`,与`CPU`架构版本无关

## TARGET_PLATFORM
指定目标平台的`Android API`版本号。例如，5.1系统对应的值是:
```
TARGET_PLATFORM := android-22
```

## TARGET_ARCH_ABI
指定`CPU`的名称和架构，你可以指定一个或多个值，可取的值有：
+ ARMv5TE         ----->   armeabi
+ ARMv7           ----->   armeabi-v7a
+ ARMv8 AArch64   ----->   arm64-v8a
+ i686            ----->   x86
+ x86-64          ----->   x86-64
+ mips32(r1)      ----->   mips
+ mips64(r6)      ----->   mips64
+ All             ----->   all

## TARGET_ABI
连接了`Android API`和`ABI`两个值，例如：
```
TARGET_ABI := android-22-arm64-v8a
```

## LOCAL_MODULE_FILENAME
该选项可以重写模块的默认名称，例如：
```
LOCAL_MODULE := foo
LOCAL_MODULE_FILENAME := libnewfoo
```
系统最终会生成`libnewfoo.so`

## LOCAL_SRC_FILES
包含了构建该模块的源文件列表。可以使用相对路径也可以使用绝对路径，不过最好不要使用绝对路径而使用相对路径，这样可以方便移植

## LOCAL_CPP_EXTENSION
指出`C++`源文件的扩展名（可选）

## LOCAL_CPP_FEATURES
该变量指明当前模块所依赖的`C++`特性（可选）
推荐使用该变量代替在`LOCAL_CPPFLAGS`选项中传入`-frtti`和`-fexception`
使用该变量允许系统对于每个模块各自使用合适的`flag`，而使用`LOCAL_CPPFLAGS`则会导致编译器给所有模块都使用传入的`flags`
```
LOCAL_CPP_FEATURES := rtti --> （use Runtime Type Information）

LOCAL_CPP_FEATURES := exceptions --> (use C++ exceptions)
```

## LOCAL_C_INCLUDES
指明编译时的`include`路径列表，相对于`NDK ROOT`目录　（可选）

## LOCAL_CFLAGS
指明在编译`C/C++`时要添加的`flags`（可选）
>**注意：不要在这改变优化选项和调试等级,这些选项应该在`Application.mk`中自动指定**

也可以指定`include`目录：
```
LOCAL_CFLAGS += -I<path>
```
这个方法比`LOCAL_C_INCLUDES`要好一些，因为可以被`ndk-gdb`所使用

## LOCAL_CXXFLAGS
`LOCAL_CPPFLAGS`的别名

## LOCAL_CPPFLAGS
指明编译`C++`代码时的`flags`（可选），这些选项在编译器的命令行中会跟在`LOCAL_CFLAGS`后面

## LOCAL_STATIC_LIBRARIES
指明编译模块时需要的静态库列表
如果当前模块是动态库或可执行文件，该变量会告诉编译器将该变量列出的静态库连接到最终的二进制文件上
如果当前模块一个静态库，该变量只是简单的指明其他依赖当前模块的库同时也依赖该变量声明的这些库

## LOCAL_SHARED_LIBRARIES
列出当前模块在`runtime`时依赖的动态库

## LOCAL_WHOLE_STATIC_LIBRARIES
静态库全链接，不同于LOCAL_STATIC_LIBRARIES，类似于使用`--whole-archive`

## LOCAL_LDLIBS
该变量包含了在编译动态库或可执行文件时的附加连接选项列表。它允许你使用`-l`前缀来传递指定的系统库，例如：
```
LOCAL_LDLIBS := -lz
```
上面的代码告诉编译器在编译模块时还要连接`/system/lib/libz.so`

## LOCAL_ALLOW_UNDEFIEND_SYMBOLS
默认情况下，系统在编译动态库时遇到`undefined reference`错误时，会抛出`undefined symbol error`
如果将该变量设为`true`，则会关闭`undefined reference`检查
>**如果你在编译静态库时指定该变量，系统会忽略该变量**

## LOCAL_ARM_MODE
默认情况下，系统产生的二进制文件是`thumb`模式，每个指令是16位，并且连接这`/thumb`下`STL libraries`。通过将该变量设为`arm`,可以让产生的指令变为32位，例如：
```
LOCAL_ARM_MODE := arm
```
也可以将某一个源文件以`.arm`结尾以此来指定该文件要被编译成`arm`模式，例如：
```
LOCAL_SRC_FILES := foo.c bar.c.arm
```
>注意：也可以在`Application.mk`中指定`APP_OPTIM := debug`来达到同样的目的

## LOCAL_ARM_NEON
该变量只有在将`ABI`指定为`armeabi-v7a`时才有效。设置为true时，会讲浮点编译成neon指令，这会极大地加快浮点运算(前提是硬件支持)
同时，也可以将某一个源文件以`.neon`结尾来指定该源文件支持`neon`,例如：
```
LOCAL_SRC_FILES := foo.c.neon bar.c zoo.c.arm.neon
```
上面的代码指明了`zoo.c`设为`arm`模式同时支持`neon`

## LOCAL_DISABLE_NO_EXECUTE
`Android NDK r4`添加了`NX bit`安全特性，该选项默认是开启的，如果要关闭，可以将`LOCAL_DISABLE_NO_EXECUTE`这个变量设为`true`
该变量并不改变`ABI`，同时只在`ARMv6+ CPU`的设备开启

## LOCAL_DISABLE_RELRO
默认情况下， `NDK`在编译代码时会加入`read-only relocations`和`GOT protection`
该变量指明`runtime linker`在`relocation`之后确保内存为`read-only`，同时确保`security exploit`更为困难。
>这些保护只在`Android API 16`及以上才起作用

将这个变量`LOCAL_DISABLE_RELRO`设为`true`,可以将上述机制关闭

## LOCAL_DISABLE_FORMAT_STRING_CHECKS
默认情况下， 系统在编译时会进行`string`格式的检查。这个检查默认是开启的，不过可以将`LOCAL_DISABLE_FORMAT_STRING_CHECKS`这个变量设为`true`来取消这个检查

## LOCAL_EXPORT_CFLAGS
在该变量中声明的所有`flags`都会添加到在那些以`LOCAL_STATIC_LIBRARIES`或者`LOCAL_SHARED_LIBRARIES`声明依赖当前静态库或动态库的模块的`LOCAL_CFLAGS`中，例如：
```
include $(CLEAR_VARS)
LOCAL_MODULE := foo
LOCAL_SRC_FILES := foo/foo.c
LOCAL_EXPORT_CFLAGS := -DFOO=1
include $(BUILD_STATIC_LIBRARY)

include $(CLEAR_VARS)
LOCAL_MODULE := bar
LOCAL_SRC_FILES := bar.c
LOCAL_CFLGAS := -DBAR=2
LOCAL_STATIC_LIBRARIES := foo
include $(BUILD_SAHRED_LIBRARY)
```
**这里，系统会将`-DFOO=1`和`-DBAR=2`都传入给`bar.c`**
**另外，所有依赖关系都是传递的，如果`zoo`依赖`bar`,而`bar`又依赖`foo`，则`zoo`同样继承了`foo`模块的所有`exported flags`**
**系统并不会传递`exported flags`给当前模块，举例来说，`-DFOO=1`并不会传递给`foo`**

## LOCAL_EXPORT_CPPFLAGS
作用同`LOCAL_EXPORT_CFLAGS`，只是针对`C++`

## LOCAL_EXPORT_C_INCLUDES
导出`include paths`，例如，该变量会告诉`bar.c`应该包含`foo`模块的头文件

## LOCAL_EXPORT_LDFLAGS
该变量作用同`LOCAL_EXPORT_CFLAGS`,只不过是针对`linker flags`

## LOCAL_EXPORT_LDLIBS
该变量告诉系统传递指定的的系统库给编译器，系统会添加导入的的连接属性给`LOCAL_LDLIBS`，例如：
```
include $(CLEAR_VARS)
LOCAL_MODULE := foo
LOCAL_SRC_FILES := foo/foo.c
LOCAL_EXPORT_LDLIBS := -llog
include $(BUILD_STATIC_LIBRARY)

include $(CLEAR_VARS)
LOCAL_MODULE := bar
LOCAL_SRC_FILES := bar.c
LOCAL_STATIC_LIBRARIES := foo
include $(BUILD_SHARED_LIBRARY)
```
系统会在编译`libbar.so`时将`-llog`放在连接命令行末端

## LOCAL_SHORT_COMMANDS
当你的模块中有大量源文件或依赖库时，可以将此变量设为`true`，之后系统会使用`@`语法来直接包含对象文件或二进制文件，这个特性在`Windows`会有用，也可以在`Application.mk`中定义`APP_SHORT_COMMANDS`来为每个模块开启这一属性

## LOCAL_THIN_ARCHIVE
构建静态库时，可以将此变量设为`true`，系统会产生一个'瘦身了的`archive`文件'，在该库文件中并不包含对象文件，而只是包含每个对象文件对应的路径

## my-dir
这个宏返回最后一个`makefile`的路径，一般都是当前`Android.mk`的路径，这个宏一般用来在`Android.mk`开头定义`LOCAL_PATH`变量，例如：
```
LOCAL_PATH := $(call my-dir)
```

## all-subdir-makefiles
返回当前`my-dir`路径下所有子目录的所有`Android.mk`

## this-makefile
返回当前`makefile`的路径

## parent-makefile
返回包含当前`makefile`的父`makefile`的路径

## grand-parent-makefile
返回包含当前`makefile`的祖父`makefile`的路径

## import-module
可以用来包含一个指定模块的`Android.mk`，用法是：
```
$(call import-module,<name>)
```

## 使用`Prebuilt Libraries`

你必须声明每个`prebuilt library`作为一个独立的模块，步骤为以下几步:
1. 定义一个模块名称，该名称不必同`prebuilt library`本身的名字一致
2. 在`Android.mk`中，将`LOCAL_SRC_FILES`置为`prebuilt library`的路径
3. `include PREBUILT_SHARED_LIBRARY or PREBUILT_STATIC_LIBRARY`
例子：
```
LOCAL_PATH := $(call my-dir)
include $(CLEAR_VARS)

LOCAL_MODULE := foo-prebuilt
LOCAL_SRC_FILES := libfoo.so
include $(PREBUILD_SHARED_LIBRARY)
```
