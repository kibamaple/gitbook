#MAKE
一个程序，往往需要将很多源码文件及各种库，按照特定的顺序编译和链接在一起。gun make程序根据Makefile指定的规则执行指令，根据特定顺序对源码进行编译，测试，安装及清理。
###使用make文件
* 默认情况下，当make查找makefile时，会按照以下顺序尝试以下名称：GNUmakefile，makefile和Makefile。
* 非标准文件名，可以使用'-f'或'--file'选项指定makefile名称。
###内容
Makefile内容包含显式规则，隐式规则，变量定义，指令和注释。
* `显示规则`描述如何重制一个或多个文件，和调用规则的目标。它罗列出目标所有的依赖条件和执行指令
* `隐式规则`描述何时以及如何根据其名称重新创建一类文件。
* `变量定义`描述变量的文本字符串值的行，可以在稍后将其替换为文本。
  * 展开变量方式：`$(变量)`或`${变量}`
  * 环境变量`MAKEFILES`如果被定义,它的值作为附加makefile文件列表，在其他文件之前被读取。
* `指令`描述make在读取makefile时做的特殊事情。
  * 读取其他makefile：`include filenames…`
    * 文件名为空，不会包含内容，也不会报异常
    * 会自动忽略行开头多余空格，不能以tab开头
    * 如果想忽略文件不存在或不能重建的异常信息，可以使用`-include`代替`include`
  * 条件指令`ifeq`，`else`，`endif`。
  ```
        libs_for_gcc = -lgnu
        normal_libs =

        foo: $(objects)
        ifeq ($(CC),gcc)
                $(CC) -o foo $(objects) $(libs_for_gcc)
        else
                $(CC) -o foo $(objects) $(normal_libs)
        endi
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
###Makefile规则
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
* prerequisites：作为目标的依赖目标。（非必须，可以没有依赖文件）
分为两种依赖目标，由`|`分隔
  * 一般依赖目标,随依赖目标更新执行命令
  * order-only依赖目标，不随依赖目标更新强制执行命令。
  ```
  一般依赖目标 | order-only依赖目标
  ```
* recipe：执行的动作,可以有多个命令。（注意：需要在每一个动作行上，加上一个tab作为前缀,也可以设置`.RECIPEPREFIX`变量，来改变前缀符）。
  * 一个变量，定义一个上下文环境
  * 一个条件表达式，定义一个上下文环境
  * 以`@`开始的行，在传递给`shell`之前会抛弃掉，用于打印提示。
  * 执行时，将为每行调用一个新`shell`
  * `.ONESHELL`用于将所有`target`的所有行，放在一个上下文运行
###执行make
Makefile中，第一个以非`.`开头的`target`开始（即默认`target`）。如有依赖目标，即检查是否需要生成依赖目标，且当需要生成时，执行依赖目标动作。（即依赖倒推方式执行）。
makefile有读入和展开两个阶段。读入阶段会替换变量或转义字符,展开阶段会继续替换转义后的变量。
###gnu程序targets规范
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
###通配符
与shell一样，如:`*`,`?`,`[...]`
###文件查找规则
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
###特殊target
* `.PHONY` 虚假目标，不考虑其生成文件，调用即执行的`target`列表
* [其他特殊target](https://www.gnu.org/software/make/manual/make.html#Special-Targets)
###静态格式匹配规则
```
targets …: target-pattern: prereq-patterns …
        recipe
        …
```
从`target`中根据`target-pattern`提取匹配部分，再将该部分按照`prereq-patterns`生成`preerquisites`
###gcc依赖生成命令行选项
* `-E`预处理执行完即停止编译 
* `-M`预处理将`.c`源码中的`#include`按照make规则输出 
* `-MM`与`-M`一样，只是不包含标准库
###读取其他makefile
* MAKE
* include
###转义字符
转义字符：`\`
* 转义注释符`\#`
* 转义换行符

###Makefile变量
makefilek可以定义一个名为objects,OBJECTS,objs,OBJS,obj,或OBJ的变量，来记录所有编译对象的列表。
```
objects = main.o kbd.o command.o display.o \
          insert.o search.o files.o utils.o

edit : $(objects)
        cc -o edit $(objects)
main.o : main.c defs.h
        cc -c main.c
kbd.o : kbd.c defs.h command.h
        cc -c kbd.c
command.o : command.c defs.h command.h
        cc -c command.c
display.o : display.c defs.h buffer.h
        cc -c display.c
insert.o : insert.c defs.h buffer.h
        cc -c insert.c
search.o : search.c defs.h buffer.h
        cc -c search.c
files.o : files.c defs.h buffer.h command.h
        cc -c files.c
utils.o : utils.c defs.h
        cc -c utils.c
clean :
        rm edit $(objects)
```
###Make隐式规则
不必指明编译的单个C源码文件名。
```
objects = main.o kbd.o command.o display.o \
          insert.o search.o files.o utils.o

edit : $(objects)
        cc -o edit $(objects)

main.o : defs.h
kbd.o : defs.h command.h
command.o : defs.h command.h
display.o : defs.h buffer.h
insert.o : defs.h buffer.h
search.o : defs.h buffer.h
files.o : defs.h buffer.h command.h
utils.o : defs.h

.PHONY : clean
clean :
        rm edit $(objects)
```