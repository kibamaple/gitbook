#GNU编码规范
###规避法律及相关问题
* 不使用Unix源码及其他专有程序
* 接受代码贡献前，需要等待GNU确保免责等法律文件生效
* 在软件包和文档中，不包含任何商标声明
> 不以“win”作为Microsoft Windows的缩写
###程序设计
* 不要大量使用模板
* 提供兼容,且包含'--ansi'，'--posix'或'--compatible'选项以关闭
    > 如果定义了环境变量POSIXLY_CORRECT（即使使用空值定义），许多GNU程序会抑制与POSIX冲突的扩展。如果合适，请让程序识别这个变量。
* 最好不完全依赖于扩展，提供内嵌方式
* 构建程序时已知的配置选项，推荐使用if替换条件编译
    > [Standard C and Pre-Standard C](http://www.gnu.org/prep/standards/html_node/Standard-C.html#Standard-C)
###编写程序行为
* GNU工具主要遵循POSIX.2的规范
* 如有临时文件，需优先检查使用`TMPDIR`环境变量。----[编写健壮的程序](http://www.gnu.org/prep/standards/html_node/Semantics.html#Semantics)
* 为库选择一个名称前缀，长度超过两个字符。所有外部函数和变量名称都应以此前缀开头。
* 外部标记命名以"_"开头，后面紧跟库前缀。
* 静态函数和变量可以随意使用，不需要符合任何命名约定。
* [异常消息格式](http://www.gnu.org/prep/standards/html_node/Errors.html#Errors)
    * 交互式
    > sourcefile:lineno: message
    > sourcefile:lineno:column: message
    > sourcefile:lineno.column: message
    > sourcefile:line1.column1-line2.column2: message
    > sourcefile:line1.column1-column2: message
    > sourcefile:line1-line2: message
    * 非交互式
    > program:sourcefile:lineno:column: message
* 不根据执行程序名称或输出终端，决定功能。按照运行时选项或编译开关来选择程序功能。
* 编写图形界面程序，请使用GTK +工具包或GNUstep工具包使其与X Window系统一起工作。仍需提供一个命令行界面来控制功能，图形界面通常是调用命令行程序的单独程序。
* 参照POSIX的指导方针来定义命令行选项。可以用[gnu libc的getopt](https://www.gnu.org/software/libc/manual/html_node/Getopt.html)来解析
* 所有程序需要支持 [--version](https://www.gnu.org/prep/standards/html_node/_002d_002dversion.html#g_t_002d_002dversion) 版本查看和 [--help](https://www.gnu.org/prep/standards/html_node/_002d_002dhelp.html#g_t_002d_002dhelp) 帮助信息。CGI程序需要根据`PATH_INFO`访问路径支持。
* 插件与基础程序应紧密结合，确保是他们是同一个扩展程序的一部分。并且应该要求插件开发人员确认许可。
* 参考[可选参数选项表](https://www.gnu.org/prep/standards/html_node/Option-Table.html#Option-Table)
* 参考[OID对象标识](https://www.gnu.org/prep/standards/html_node/OID-Allocations.html#OID-Allocations)
* 如果程序仅使用几兆内存，就不用费劲心思减少内存使用。可以使用内存工具[valgrind](http://valgrind.org/)，但尽量不要使程序复杂化，避免其误报。
* `/usr`和`/etc`[文件使用](https://www.gnu.org/prep/standards/html_node/File-Usage.html#File-Usage)
### C的充分利用
* 参考[C源码格式](https://www.gnu.org/prep/standards/html_node/Formatting.html#Formatting)
* 源码需要编写详细注释，所有注释用英文书写。----[注释代码](https://www.gnu.org/prep/standards/html_node/Comments.html#Comments)
    * `main`文件顶部以注释开头，简要说明用途。如`fmt - filter for simple filling of text`
    * 所有函数需要有注释，说明函数做了什么，它得到了什么类型的参数，以及参数的可能值是什么意思和用法。如果有返回值，仍然需要注释。
    * `#endif`,`#else`,应该注释其条件和意义。 
* 明确声明所有对象的类型,保证源码可读性。----[干净的C源码](https://www.gnu.org/prep/standards/html_node/Syntactic-Conventions.html#Syntactic-Conventions)
* 全局变量和函数的名称作为一种注释，尽可能的表达其作用和意义。局部变量名可以少量缩写。可以用`_`分割词汇。
* 定义一些`int`常量时，`enum`优于`#define`
* 对于GNU来说操作系统可移植性是可取的，不是重要的。但支持基于Linux的GNU系统很重要。操作系统可移植性的最简单方法是使用[Autoconf](https://www.gnu.org/software/autoconf/autoconf.html)。
* 在编译C文件时定义“特性测试宏”_GNU_SOURCE,可以在GNU或GNU / Linux上编译时，启用GNU库扩展函数的声明。
* CPU类型的差异是需要处理的。但GNU不要求支持低于32位的机器。----[CPU可移植性](https://www.gnu.org/prep/standards/html_node/CPU-Portability.html#CPU-Portability)
* 尽量使用标准C和POSIX来编写,尽可能使用标准接口,拒绝自行声明系统函数。
* [Gnulib](https://www.gnu.org/software/gnulib/)与[GNU Autoconf](https://www.gnu.org/software/autoconf/autoconf.html)和[Automake](https://www.gnu.org/software/automake/)集成可以消除编写可移植代码的许多负担
* 国际化可以使用[GNU gettext](https://www.gnu.org/software/gettext/)。注意复数需要使用`ngettext`
* 在GNU源代码注释，文本文档和其他上下文中，坚持使用一种字符集。首选ASCII字符集。
* 坚持使用纯ASCII的引号字符,双引号`0x22 (‘"’)`或单引号`0x27 (‘'’)`。
* `mmap`内存映射文件，需要在使用的特定文件上尝试，不一定成功。
###文档编写
*  GNU手册在开始时介绍基本主题，并在稍后介绍所有细节。手册应该不仅仅告诉用户每个功能可以做什么，有哪些用法或命令行。还需要说出它最适合的工作，并展示如何将它用于这些工作。解释什么是建议的用法，以及用户应该避免什么样的用法。
* GNU手册首选文档格式是[Texinfo](http://www.gnu.org/software/texinfo/)格式化语言。例如:[Bison](https://www.gnu.org/software/bison/manual/)
