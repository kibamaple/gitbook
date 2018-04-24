# AUTOCONF
[Autoconf](https://www.gnu.org/software/autoconf/autoconf.html)是一个可扩展的M4宏包，可生成shell脚本以自动配置软件源代码包。使用Autoconf生成配置脚本需要[GNU M4](https://www.gnu.org/software/m4/m4.html)。
Autoconf的首要目的，生产出无痛苦，可移植和可预测的`configure`。
> Autoconf在C编译器中[预定义宏列表](https://www.gnu.org/software/autoconf/manual/autoconf.html#Preprocessor-Symbol-Index)
> Autoconf需要GNU M4版本1.4.6或更高版本才能生成脚本

* 使用Autoconf创建配置脚本，需要编写Autoconf输入文件`configure.ac`或`configure.in`并运行autoconf。
  * `configure.ac`通过调用autoconf宏，测试程序包需要或可以使用的系统功能
* 编写补充Autoconf附带的功能测试，则还可以编写`aclocal.m4`和`acsite.m4`文件。
* 如果使用C头文件来包含`#define`指令，也可以运行`autoheader`，并且可以将`config.h.in`与程序包一起分发。

`*`:可执行文件
`[]`:可选文件
使用Autoconf：
```
 your source files --> [autoscan*] --> [configure.scan] --> configure.ac
     
     configure.ac --.
                    |   .------> autoconf* -----> configure
     [aclocal.m4] --+---+
                    |   `-----> [autoheader*] --> [config.h.in]
     [acsite.m4] ---'
     
     Makefile.in

```
使用Automake：
```
     [acinclude.m4] --.
                      |
     [local macros] --+--> aclocal* --> aclocal.m4
                      |
     configure.ac ----'
     
     configure.ac --.
                    +--> automake* --> Makefile.in
     Makefile.am ---'
```
使用`configure`：
```
                            .-------------> [config.cache]
     configure* ------------+-------------> config.log
                            |
     [config.h.in] -.       v            .-> [config.h] -.
                    +--> config.status* -+               +--> make*
     Makefile.in ---'                    `-> Makefile ---'
```
### 运行
首先读取当前目录可选文件,`acsite.m4`(包含autoconf宏文件)和`aclocal.m4`.宏的重复定义会将之前定义覆盖.
* 不带参数运行，将使用`configure.ac`生成`configure`
* 参数为指定文件，生成内容将输出到标准输出
* `--debug` `-d` 不删除临时文件
* `--force` `-f` 强制更新`configure`
* `--include=dir` `-I dir` 追加的`include`路径
* `--prepend-include=dir` `-B dir` 预添加的`include`路径
* `--output=file` `-o file`输出到指定文件
* `--warnings=category` `-W category` 警告等级
  * `all`
  * `none`
  * `error`
  * `no-category`
* `--trace=macro[:format]` `-t macro[:format]`不创建`configure`,根据格式(默认：`$f:$l:$n:$%`)罗列对宏的调用
* `--initialization` `-i`使`--trace`跟踪宏的初始化（通常是`AC_DEFUN定义）
### configure生成文件
* 一个或多个`Makefile`,通常在每个子目录中
* 可选，包含`#define`指令的C头文件
* 名为`config.status`的shell脚本,运行时重新创建出以上文件
* 可选，名为config.cache的shell脚本（在使用'configure --config-cache'时创建）保存运行时的测试结果
* 一个名为config.log的文件，包含编译器生成的任何消息
### autoscan
通过搜索包下源文件,生成`configure.scan`。在检查确认后，可重命名为`configure.ac`。通常用于创建或维护`configure.ac`。
* `--include=dir` `-I dir` 追加的`include`路径
* `--prepend-include=dir` `-B dir` 预添加的`include`路径
### ifnames
向标准输出预编译指令(`#if, #elif, #ifdef,#ifndef`等)中出现的文件列表
### autoreconf
在适当的时候,在指定目录及子目录下，运行`autoconf`,`autoheader`,`aclocal`,`automake`,`libtoolize`和`autopoint`
* `--debug` `-d` 不删除临时文件
* `--force` `-f` 强制更新
* `--install` `-i` 安装包中缺少的辅助文件
* `--include=dir` `-I dir` 追加的`include`路径
* `--prepend-include=dir` `-B dir` 预添加的`include`路径
* `--no-recursive` 不在子目录重建
* `--symlink` `-s` 与`--install`一起使用时，将符号链接到辅助文件
* `--make` `-m` 在目录被配置，更新配置文件。在`./config.status --recheck && ./config.status`后执行`make`
* `--warnings=category` `-W category` 警告等级
  * `cross`
  * `obsolete`
  * `portability`
  * `syntax`
  * `all`
  * `none`
  * `error`
  * `no-category` 
### autoheader
用于创建`config.h.in`(含`#define`指令的C头文件模板)
### 语法
* 引用参数，使用`[`和`]`,数字等可简化
* 引用参数中的空格和换行，仅在引用符号内有效
* 参数中，允许递归调用宏
* 使用宏时候，使用`[`和`]`引用
* 内容中与已定义宏相同，会引起冲突，使用转义改变与宏相同的内容
* 可选参数可以为`[]`,留空或完全省略
* 不要添加额外的空白行
* `#`开始表示注释
```
     AC_CHECK_HEADER(stdio.h,
                     [AC_DEFINE(HAVE_STDIO_H, 1,
                        [Define to 1 if you have <stdio.h>.])],
                     [AC_MSG_ERROR([sorry, can't do anything for you])])
```
转义`char b[10];`
```
     AC_COMPILE_IFELSE([AC_LANG_SOURCE([[char b[10];]])], [],
       [AC_MSG_ERROR([you lose])])
```
可选参数
```
     AC_CHECK_HEADERS([stdio.h], [], [], [])
     AC_CHECK_HEADERS([stdio.h],,,)
     AC_CHECK_HEADERS([stdio.h])
```
### configure.ac内容及顺序
```
     AC_INIT(package, version, bug-report-address)
     information on the package
     checks for programs
     checks for libraries
     checks for header files
     checks for types
     checks for structures
     checks for compiler characteristics
     checks for library functions
     checks for system services
     AC_CONFIG_FILES([file...])
     AC_OUTPUT
```
#### `AC_INIT`
>AC_INIT ([包名], [版本号], [bug报告email地址(可选)], [tar包名], [包主页url])

必须在所有输出宏之前被调用,参数只能使用静态或M4宏.
* `m4_esyscmd` 计算版本字符串
* `m4_esyscmd_s`
# AUTOMAKE
[Automake](https://www.gnu.org/software/automake/)从`Makefile.am`自动生成供Autoconf使用的`Makefile.in`文件。`Makefile.am`基本上都是`make`的变量定义。需要使用`perl`来生成`Makefile.in`。
### 标准目录标量
```
Directory variable	Default value
prefix	/usr/local
exec_prefix	${prefix}
bindir	${exec_prefix}/bin
datarootdir	${prefix}/share
datadir	${datarootdir}
mandir	${datarootdir}/man
infodir	${datarootdir}/info
docdir	${datarootdir}/doc/${PACKAGE}
…
```
# Gnulib
[Gnulib](https://www.gnu.org/software/gnulib/)常用GNU代码。打算在免费软件中共享。
# Libtool
[Libtool](https://www.gnu.org/software/libtool/)是一个通用库支持脚本。提供构建共享库的所有需求。
# GNU M4
是unix宏处理器的一个实现,除了宏扩展以外,还包含加载命名文件，执行shell命令,执行整数运算,文本处理,递归执行等内置函数.