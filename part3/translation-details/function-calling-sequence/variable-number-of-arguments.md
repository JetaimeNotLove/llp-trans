14.1.5 变长数量参数

我们使用的调用规约支持变长的参数列表。也就是说函数可以接收任意数量的参数。之所以行得通是因为参数的传入\(和函数退出时的栈清理\)是调用方\(calling\)函数的职责。

这种变参函数的声明包含一个省略号---即三个英文点来代替函数的最后一个参数。这种类型比较典型的函数是我们的老朋友了：printf。

```
void printf( char const* format, ... );
```

这个 printf 是怎么知道参数的准确数量的呢？首先它知道至少有一个参数传进来了，\(char const\* format\)。通过分析这个字符串并对字符串中的特殊占位符进行计数，printf 可以计算出参数的数量和每个参数对应的类型\(也就是说这个参数被放在哪个寄存器\)。

---

■Note 为了处理变长数量的参数，al 寄存器应该存储被参数使用到的 xmm 寄存器的数量。

---

如你所见，实际上没有任何办法知道准确的传入参数数量。函数是从参数中推断出来可能有的参数数量\(这里的场景是从 format 参数中推出的\)。如果实际上 format 中的占位符要比参数的数量多，那么 printf 是没有办法知道的，它只会天真地从寄存器和内存中去取参数的内容。

显然，对 C 程序员来说也没法对这种变长参数的功能进行编码，因为寄存器在 C 语言中没有办法直接访问。然而还是有一种标准库提供的可移植的手段，能够让我们声明变长参数的函数。每一个平台对于这种手段都有自己的实现支持。在你的程序中 include 进 stdarg.h 这个头文件，然后在函数中处理：

* `va_list`– 存储了参数信息的结构体
* `va_start`– 初始化  `va list` 的宏
* `va_end`– 结束初始化 `va list` 的宏
* `va_arg`– 在提供了 `va list` 实例和参数类型时，从参数列表中取下一个参数的宏

列表 14-7 展示了一个例子。printer 函数接收变长参数列表和任意的参数数量。

_**Listing 14-7**.vararg.c_

```
#include <stdarg.h>
#include <stdio.h>

void printer( unsigned long argcount, ... ) {
    va_list args;
    unsigned long i;
    va_start( args, argcount );
    for (i = 0; i < argcount; i++ )
        printf(" %d\n", va_arg(args, int )  );
    va_end( args );
}

int main () {
    printer(10, 1, 2, 3, 4, 5, 6, 7, 8, 9, 0 );
    return 0;
}
```

首先，`va_list` 被 ... 之前的最后一个参数所初始化。然后每次调用  \`va arg\` 都会获取到下一个参数。第二个参数是参数的具体类型。最后 va list 被 va end 结束初始化。

由于类型名被用作了参数，`va_list` 用名字来使用名字来访问，但类型有变化，这个例子看上去可能有些费解。

---

■Question 264 你可以想像一下，在哪个函数中\(非宏\)，用名字来接收一个“变量”，然后修改掉它么？这种变量应该是什么类型？

---


