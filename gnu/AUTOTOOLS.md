# AUTOCONF
[Autoconf](https://www.gnu.org/software/autoconf/autoconf.html)是一个可扩展的M4宏包，可生成shell脚本以自动配置软件源代码包。使用Autoconf生成配置脚本需要[GNU M4](https://www.gnu.org/software/m4/m4.html)。
Autoconf的首要目的，生产出无痛苦，可移植和可预测的`configure`。
> Autoconf在C编译器中[预定义宏列表](https://www.gnu.org/software/autoconf/manual/autoconf.html#Preprocessor-Symbol-Index)
> Autoconf需要GNU M4版本1.4.6或更高版本才能生成脚本

要使用Autoconf创建配置脚本，需要编写Autoconf输入文件`configure.ac`或`configure.in`并运行autoconf。
### configure生成文件
* 一个或多个`Makefile`,通常在每个子目录中
* 可选，包含`#define`指令的C头文
* 名为`config.status`的shell脚本,运行时重新创建出以上文件
* 可选，名为config.cache的shell脚本（在使用'configure --config-cache'时创建）保存运行时的测试结果
* 一个名为config.log的文件，包含编译器生成的任何消息
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