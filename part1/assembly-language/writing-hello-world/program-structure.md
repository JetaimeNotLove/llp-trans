2.2.2 程序结构

我们应该还记得冯诺伊曼架构的描述，代码和数据共享同一块内存；是没有办法区分开的。但程序员希望能够对两者进行分离。汇编程序一般会被划分为多个**段**。每一个段都有自己的用处：例如 .text 段放指令，.data 段放**全局变量**\(程序执行期间每一时刻都可访问的变量\)。程序内可以在段之间进行反复地切换；不过所有的段和数据最终还能聚集在同一个地方。

为了避免用麻烦的数值表示地址，程序员使用 **label**。label 的本质是一些名字和对应的内存地址。label 可以放在任意的命令之前，且用冒号与后续的代码隔开。这个程序在第五行有一个 label： \_start。

高级语言中一般都有变量的概念。在汇编语言中，实际上，变量和过程的概念就很微妙了。说 label 或者地址会更方便一些。

一个汇编程序可以被分成多个文件。其中一个文件包含 \_start label。这个 label 是整个**程序的入口**；标记了整个程序开始执行的第一条指令。

\_start 标签应该被声明为 global 标签\(看第一行\)。global 的原因会在稍后给出。

汇编中的**注释**以分号开头，一直持续到下一行之前。

汇编语言是由命令组成的，这些命令会直接被 map 到机器码。然而并不是语言的内容都可以被翻译成命令。还有一些控制翻译过程的叫作伪操作\(directives\)的东西。在 Hello world 示例中有三个伪操作：global，section，db。

---

■Note 通常情况下，汇编语言是大小写不敏感的，但标签名是大小写敏感的。

mov，mOV，Mov 是没什么区别的，但 `global start`_ 和 _`global _START`是不一样的。段的名字也是大小写敏感的，比如：.DATA 和 .data 就是不一样的。

---

db 伪指令用来创建字节数据。想定义数据一般用下面这些伪指令之一，具体用哪个需要根据数据的格式确定：

* db---字节；
* dw---words，每个 word 等于两个字节；
* dd---double words，每个double words 等于四个字节；
* dq---quad words，每个 quad words 等于八个字节；

来看一个例子，列表 2-2。

_**Listing 2-2**.data\_decl.asm_

```
section .data
   example1: db 5, 16, 8, 4, 2, 1
   example2: times 999 db 42
   example3: dw 999
```

times n 是一个伪指令，表示相同的操作重复 n 次。和你自己手动复制粘贴 n 次的效果是一样的。这个伪指令也可以和 CPU 指令配合着一起用。

你可以在任何段内创建数据。就像之前讲的一样，对于 CPU 来说数据和指令没什么区别，CPU 在需要时甚至也会尝试去把数据翻译成指令。

这些伪指令允许你一个一个地创建数据对象，像列表 2-3 一样，一个字符串序列，然后紧跟着一个字节，值为 10。

_**Listing 2-3**.hello.asm_

```
message: db 'hello, world!', 10
```

字母，数字，和其它字符都会以 ASCII 编码进行编码。程序员们都会以 ASCII 的码表达成共识。我们从标签 message 对应的地址开始。将 Hello, world! 对应的 ASCII 码都存储下来，然后再附上一个值为 10 的字符。为什么是 10？按照约定，编码为 10 的字符是换行符。

---

**■Terminological chaos** 术语混乱：通常人们认为 integer 的长度即是计算机的字长。因为我们写代码的平台是 64 位计算机，地址是 64 位，通用寄存器也是 64 位，因此认为机器字长是 64 位或者  8 字节是比较方便的。

在 Intel 架构的语境下汇编编程中的词 word 则是表示 16 位的数据条目，因为在老的机器上 word 就是那时候的机器字长。不幸的是，考虑到历史遗产问题，现在依然沿用了 word 为 16 位的做法，这也就是为什么 32 位的数据被称为 double words 而 64 位的数据被称为 quad words。

---



