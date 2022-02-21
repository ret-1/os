# 了解x86汇编文件

~~好，下面让我们看官方手册学习汇编：~~[~~PC Assembly Language (mit.edu)~~](https://pdos.csail.mit.edu/6.828/2018/readings/pcasm-book.pdf)~~。~~

哈哈，开玩笑的！实际上大部分都是学过的比较基础的指令。

## 一个简短的汇编文件
以下面这段代码为例：

```wasm
 # test1.s 
 .section .data 
 .section .text 
 .globl _start 
 _start: 
     movl $1, %eax 
     movl $3, %ebx 
     int $0x80
```



我们先对它进行汇编操作：

```shell
 njucs> gcc -c test1.s -o test1.o
```

然后再进行链接操作：

```shell
 njucs> gcc test1.o -o test1
```

此时会输出如下的报错信息：

* `_start`函数出现了双重定义
* `_start`函数出现了未定义的引用`main`

```shell
 /usr/bin/ld: test1.o: in function `_start':
(.text+0x0): multiple definition of `_start'; /usr/lib/gcc/x86_64-linux-gnu/9/../../../x86_64-linux-gnu/Scrt1.o:(.text+0x0): first defined here
/usr/bin/ld: /usr/lib/gcc/x86_64-linux-gnu/9/../../../x86_64-linux-gnu/Scrt1.o: in function `_start':
(.text+0x24): undefined reference to `main'
collect2: error: ld returned 1 exit status
```

> #### question_red::为什么会出现重定义？
> 实际上，`_start`函数是链接器默认的**程序入口**。也就是说，通过链接器`ld`链接的ELF可执行文件所执行的第一条指令就是从\_start的第一条指令。
>  
> 而C/C++程序，都是从`main`函数开始执行的。那么，我们可以推测出：通过`gcc`默认链接的ELF可执行文件，一定有一段从`_start`跳转到`main`的代码
>  
> 所以结合报错信息(**报错的第二行有个Scrt1.o**），我们不难猜测出错误发生的原因：`gcc`在链接的时候，默认会把`Scrt1.o`同时链接到我们的程序里，而其中包含了`_start`函数，这个函数的作用正是**跳转到main函数**。

接下来改用`ld`进行链接，然后运行：

```shell
 njucs> ld test1.o -o test1 
 njucs> ./test1 
 njucs> echo $?
 3
```

在执行编译好的程序之后，我们`echo $?`来查看程序返回值，发现是3。
  
我们在`ICS`里面学到: 
* `int $0x80`是执行系统调用（`System Call`）的软中断
* `%eax`中的1是系统调用号，通过简单查阅可知，1是`_exit`系统调用的系统调用号，作用是结束当前进程，并返回
* `%ebx`里面存放了退出时返回的值。所以会显示出3。

> #### Info::Shell中的特殊变量
> * $?: 获取上一个命令的退出状态
> * $$: shell自身的PID
> * $*: 传给shell脚本的所有参数的列表
>  
> 其他特殊变量，参考[Special Parameters (Bash Reference Manual) - GNU.org](https://www.gnu.org/software/bash/manual/html_node/Special-Parameters.html)

## 汇编文件中的关键符号
以上就是我们这个简单的汇编程序执行的全过程。那么借助这个程序，我来解释几个关键的符号：

* 以`.`开头的名称：比如`.section`和`.data`，叫做汇编语言的**伪指令**，用来指导汇编器进行布局和汇编。
* `.section`：把程序划分为不同的段（`section`），比如代码段，数据段。
* `.data`：声明一个**可读可写数据段**，存放全局变量等数据。（在这个代码中，没有全局变量，所以是空的！）
* `.text`：声明一个**代码段**，存放程序的代码部分。只可以读和执行，无法写入。
* `.globl`：说明这是一个**全局函数**或者**全局变量**，决定是否可以被其他模块引用（有globl的就可以被其他模块引用）

好吧，上面这段文字就是给大家介绍一下汇编语言的文件是怎么写的。`ICS`更多偏重于学习一些汇编指令，而本阶段的`OS`则是会用到一些真正的汇编文件，当然，这些不是最全的，不过我相信经过讲解，大家遇到问题都可以合理去搜索，实践并且分析出答案。

这个是`GCC`官网，我们可以查阅相关内容（全英文有点难，可以挑战一下自己。）还可以通过`man gcc`或者`gcc --help`来学习。

下面推荐另外一套工具链：[https://asmtutor.com/#top](https://asmtutor.com/#top)
