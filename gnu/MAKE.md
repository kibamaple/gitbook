# MAKE
一个程序，往往需要将很多源码文件及各种库，按照特定的顺序编译和链接在一起。gun make程序根据Makefile指定的规则执行指令，根据特定顺序对源码进行编译，测试，安装及清理。
* [使用make文件](#使用make文件)
* [内容](#内容)
* [Makefile规则](#Makefile规则)
* [执行make](#执行make)
* [函数](#函数)
* [gnu程序targets规范](#gnu程序targets规范)
* [通配符](#通配符)
* [文件查找规则](#文件查找规则)
* [特殊target](#特殊target)
* [静态格式匹配规则](#静态格式匹配规则)
* [gcc依赖生成命令行选项](#gcc依赖生成命令行选项)
* [读取其他makefile](#读取其他makefile)
* [转义字符](#转义字符)
* [Make隐式规则](#Make隐式规则)
* [Archive](#Archive)
* [make扩展](#make扩展)
### 使用make文件
* 默认情况下，当make查找makefile时，会按照以下顺序尝试以下名称：GNUmakefile，makefile和Makefile。
* 非标准文件名，可以使用'-f'或'--file'选项指定makefile名称。
### 内容
Makefile内容包含显式规则，隐式规则，变量定义，指令和注释。
* `显示规则`描述如何重制一个或多个文件，和调用规则的目标。它罗列出目标所有的依赖条件和执行指令
* `隐式规则`描述何时以及如何根据其名称重新创建一类文件。
* `变量定义`描述变量的文本字符串值的行，可以在稍后将其替换为文本。
  * 展开变量方式：`$变量`，`$(变量)`或`${变量}`
  * `=`为递归时扩展，即展开时去计算值
  * `:=`或`::=`为简单扩展，即定义时定义值
  * `?=`为未定义变量赋值
  * `+=`追加变量值
  * `$(var:match=replace)`为变量值替换
  * 如果变量在命令行被定义，那么makefile内普通变量无效，需要`override 变量名 = 变量值`进行覆盖。
  * target特定变量`prog : CFLAGS = -g`，只在当前target的所有`prerequisites`有效
  * `undefine 变量名`为删除变量
  * make可以直接使用系统环境变量
  * 环境变量`MAKEFILES`如果被定义,它的值作为附加makefile文件列表，在其他文件之前被读取。
  * [自动定义变量](https://www.gnu.org/software/make/manual/make.html#Automatic-Variables)
    * `#@`当前`target`的文件名
    * `#$`归档成员名
    * `#<`rule第一个`prerequisite`的名称
    * `$?`rule所有`prerequisite`的名称
  * [隐式规则变量](https://www.gnu.org/software/make/manual/make.html#Implicit-Variables)
* `指令`描述make在读取makefile时做的特殊事情。
  * 读取其他makefile：`include filenames…`
    * 文件名为空，不会包含内容，也不会报异常
    * 会自动忽略行开头多余空格，不能以tab开头
    * 如果想忽略文件不存在或不能重建的异常信息，可以使用`-include`代替`include`
  * 条件指令`ifeq`，`else`，`endif`,`ifneq`,`ifdef`,`ifndef`。
  ```
      conditional-directive-one
      text-if-one-is-true
      else conditional-directive-two
      text-if-two-is-true
      else
      text-if-one-and-two-are-false
      endif
  ```
  * 定义多行变量(即命令内容变量，可以与eval函数配合使用)
  ```
  define two-lines =
  echo foo
  echo $(bar)
  endef
  ```
* `注释`以`#`号开头的一行，为注释行。
  * 变量引用或函数调用时，`#`将不作为注释开头。
  * 执行动作中的`#`,将被传递给`SHELL`处理
  * `define`指令中，`#`内容取决于变量执行
* 双冒号规则，`target`分隔符，即`::`代替`:`
  * `target`晚于任意`prerequisites`时执行。
  * 无`prerequisites`,任何时候都将执行。
  * 同`target`规则，被视为不同的两个规则，分别执行。
### Makefile规则
```
target … : prerequisites …
        recipe
        …
        …
```
* target：通常是由程序生成的文件名,可以是执行文件或目标文件。多个`target`由空格分隔。
    * Phony Targets：不生成任何文件，仅仅是用于在明确执行指定目标的动作。
    如下，目录下存在clean文件的情况下`make`将不会执行`clean`部分
    ```
    clean:
            rm *.o temp
    ```
    如下，使用PHONY，执行`make clean`，无论是否存在clean文件，`clean`部分都会执行。
    ```
    .PHONY: clean
    clean:
            rm *.o temp
    ```
    单个`target`可以有多个`rule`，`make`执行时，它们的`prerequisites`将被合并为一个，满足条件即执行所有`recipe`
* prerequisites：作为目标的依赖目标。（非必须，可以没有 依赖文件）
分为两种依赖目标，由`|`分隔
  * 一般依赖目标,随依赖目标更新执行命令
  * order-only依赖目标，不随依赖目标更新强制执行命令。
  ```
  一般依赖目标 | order-only依赖目标
  ```
* recipe：执行的动作,可以有多个命令。（注意：需要在每一个动作行上，加上一个`tab`作为前缀,也可以设置`.RECIPEPREFIX`变量，来改变前缀符）。
  * 一个变量，定义一个上下文环境。
  * 一个条件表达式，定义一个上下文环境。
  * 以`@`开始的行，在执行时，不打印该行。
  * 执行时，将为每行调用一个新`shell`。
  * `.ONESHELL`用于将所有`target`的所有行，放在一个上下文运行。
  * make使用`SHELL`变量，执行shell。默认为`/bin/sh`。参数变量为`.SHELLFLAGS`。(附：[MS-DOS下SHELL](https://www.gnu.org/software/make/manual/make.html#Choosing-the-Shell))
  * 使用`MAKE`变量，执行make命令。变量值将被传递到子make的环境变量中。
  * make不直接解析shell,在简单的转换后，把命令传递给shell执行。
  * 任何在`recipe`执行过程中，以`tab`开头的行，make将考虑作为`recipe`执行
  * 使用变量：`$变量名`。(传递到`shell`前被转换，可使用`$$`转义为`$`)
  * `shell`执行返回非`0`退出，表示异常，将放弃当前`rule`，也可能影响并放弃所有`rule`。
  * 忽略异常使用`-`开头。`-i`或`--ignore-errors`命令行选项，将忽略所有异常。
### 执行make
* Makefile中，第一个以非`.`开头的`target`开始（即默认`target`）。如有依赖目标，即检查是否需要生成依赖目标，且当需要生成时，执行依赖目标动作。（即依赖倒推方式执行）。
* make命令行选项`-j 并行数`或`-jobs 并行数`开启并行执行(`MS-DOS`中无效)，`.NOTPARALLEL`伪`target`，罗列禁止并行项。
并行输出，使用`--output-sync`或`-0`选项同步打印。
  * `none`: 默认，输出产生即打印
  * `line`:行执行完后，输出打印
  * `target`:目标执行完后，输出打印(带同步打印选项的默认)
  * `recurse`:递归调用完成后，输出打印
* `ctrl-c`中断执行
并行输入,不允许同时从一个设备输入，如标准输入。
* make执行完成后,与shell一样，会返回退出状态
  * `0`成功完成编译
  * `1`有targets未完成
  * `2`异常
* make命令行参数
  * `-b` `-m` make的其他版本会忽略
  * `-B` `--always-make` 无条件执行所有`targets`
  * `-C dir` `--directory=dir` 改变makefile的读取目录
  * `d` 打印所有debug信息
  * `--debug[=options]` 根据选项打印debug信息
    * `a (all)`
    * `b (basic)`
    * `v (verbose)`
    * `i (implicit)`
    * `j (jobs)`
    * `m (makefile)`
    * `n (none)`
  * `-e` `--environment-overrides` 设置环境变量,优先于makefile的变量
  * `--eval=string` 用makefile语法，评估字符串
  * `-f file` `--file=file` `--makefile=file` 指定makefile名称
  * `-h` `--help`显示帮助
  * `-i` `--ignore-errors` 忽略执行中所有异常
  * `-I dir` `--include-dir=dir` 设置附加makefile包含目录
  * `-j [jobs]` `--jobs[=jobs]`同时并行执行的任务数
  * `-k`或`--keep-going`忽略错误，完成makefile编译
  * [其他](https://www.gnu.org/software/make/manual/make.html#Options-Summary)
### 函数
函数调用方式为`$(function arguments)`或`${function arguments}`
* [文本处理分析函数](https://www.gnu.org/software/make/manual/make.html#Text-Functions)
* [文件名（目录）函数](https://www.gnu.org/software/make/manual/make.html#File-Name-Functions)
* 条件函数
  * `$(if condition,then-part[,else-part])`
  * `$(or condition1[,condition2[,condition3…]])`
  * `$(and condition1[,condition2[,condition3…]])`
* 循环函数`$(foreach var,list,text)`
  * `var`为函数作用域内有效的局部变量
* 文件函数`$(file op filename[,text])`
  * `op`为文件操作，如写入`>`,追加`>>`等
* 调用函数`$(call variable,param,param,…)`
  根据变量内容中的表达式，生成新变量值，类似执行变量模板。
  * `$0`为调用变量名
  * `$1`~`$n`为参数
* 值函数`$(value variable)`
  仅获取变量值，不对变量值进一步展开
* 内容执行函数`$(eval text)`
  将text内容作为makefile的一部分执行
* 变量来源函数`$(origin variable)`
  * `undefined` 未定义
  * `default` 默认定义
  * `environment` 由make提供的环境变量
  * `environment override` 由make提供的环境变量,并由`-e`覆盖
  * `file` makefile内定义的变量
  * `command line` 由命令行定义的变量
  * `override`在makefile内被覆盖的变量
  * `automatic` 自动定义变量
* 变量类型函数`$(flavor variable)`
  * `undefined`未定义
  * `recursive` 递归扩展变量
  * `simple` 简单扩展变量
* 控制make执行函数
  * `$(error text…)`抛出异常
  * `$(warning text…)`抛出警告
  * `$(info text…)`抛出消息提醒
* shell调用函数`$(shell ...)`
* guile扩展函数`$(guile text)`
  用于在make中执行嵌入的`guile`扩展语言
### gnu程序targets规范
* `all` 编译程序，应该是默认`target`,不需要重建文档。默认makefile规则中，编译和链接需要带`-g`可调试参数。
* `install` 编译程序，并拷贝可执行文件，库文件等至安装路径。并且如有验证可用性测试，应该在此执行。
安装`unix man page`之前，应使用`-`忽略异常。
安装info文件，根据`$(INSTALL_DATA)`将文件拷贝至`$(infodir)`,然后执行`install-info`程序。
安装命令必须分为三类
  * 常规命令，拷贝安装可执行文件及库。指定target的变量：`NORMAL_INSTALL`。
  * 预安装命令，可为依赖包安装等。指定target的变量：`PRE_INSTALL`。
  * 安装后命令，一般为`install-info`等手册或文档安装。指定target的变量：`POST_INSTALL`。
* `install-*`不同格式的文档手册安装
* `uninstall` 删除所有`install`或`install-*`拷贝的文件。
与安装命令对应，卸载命令必须分为三类
  * 常规命令，`NORMAL_UNINSTALL`。
  * 预安装命令，`PRE_UNINSTALL`。
  * 安装后命令，`POST_UNINSTALL`。
* `clean`清理删除编译文件，不清理删除配置文件
* `dist`创建发布的tar文件
* `check`程序自行测试，在编译后执行，不需要安装。
* [其他gnu规范targets](https://www.gnu.org/software/make/manual/make.html#Standard-Targets)
### 通配符
与shell一样，如:`*`,`?`,`[...]`
### 文件查找规则
根据`target`对其所有`prerequisites`，先检查makefile目录，如果有没找到，则使用`VPATH`变量包含目录列表（冒号分隔）或`vpath`指令指定某类文件的查找目录(`vpath pattern directories`)
`target`中`recipe`执行目录不受影响，如需取得完整`target`名，可以使用`$@`

在`preerquisites`中，使用`-lname`做链接时，`make`将尝试链接`libname.so`或`libname.a`。检索目录如下：
* 当前执行目录
* `vpath`指令匹配目录
* `VPATH`变量目录
* ` /lib`
* `/usr/lib`
* `prefix/lib`
`.LIBPATTERNS`变量可以指定链接文件名格式，如默认为：`lib%.so lib%.a`
### 特殊target
* `.PHONY` 虚假目标，不考虑其生成文件，调用即执行的`target`列表
* [其他特殊target](https://www.gnu.org/software/make/manual/make.html#Special-Targets)
### 静态格式匹配规则
```
targets …: target-pattern: prereq-patterns …
        recipe
        …
```
从`target`中根据`target-pattern`提取匹配部分，再将该部分按照`prereq-patterns`生成`preerquisites`
### gcc依赖生成命令行选项
* `-E`预处理执行完即停止编译 
* `-M`预处理将`.c`源码中的`#include`按照make规则输出 
* `-MM`与`-M`一样，只是不包含标准库
### 读取其他makefile
* MAKE
* include
### 转义字符
转义字符：`\`
* 转义注释符`\#`
* 转义换行符
### Make隐式规则
当依赖没有规则或为规则没有`recipe`时，`make`会根据存在的源文件尝试建立隐式规则。
* `-r` `--no-builtin-rules` 取消所有预定义的隐式转换规则
### Archive
```
archive(member member1 ...)
```
以上构造仅适用于`targets`和`prerequisites`
```
make "foo.a(bar.o)"
```
仅需要`bar.c`源文件，相当于
```
cc -c bar.c -o bar.o
ar r foo.a bar.o
rm -f bar.o
```
### make扩展
https://www.gnu.org/software/make/manual/make.html#Extending-make