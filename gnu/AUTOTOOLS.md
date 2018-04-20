# AUTOCONF
Autoconf是一个可扩展的M4宏包，可生成shell脚本以自动配置软件源代码包。使用Autoconf生成配置脚本需要[GNU M4](https://www.gnu.org/software/m4/m4.html)。
# AUTOMAKE
从`Makefile.am`自动生成符合GNU编码标准的`Makefile.in`文件。`Makefile.am`基本上都是`make`的变量定义。需要使用`perl`来生`Makefile.in`。
> 需要使用[Autoconf](https://www.gnu.org/software/autoconf/autoconf.html)
 
### 标准目录标量
```
Directory variable	Default value
prefix	/usr/local
exec_prefix	${prefix}
bindir	${exec_prefix}/bin
libdir	${exec_prefix}/lib
…
includedir	${prefix}/include
datarootdir	${prefix}/share
datadir	${datarootdir}
mandir	${datarootdir}/man
infodir	${datarootdir}/info
docdir	${datarootdir}/doc/${PACKAGE}
…
```