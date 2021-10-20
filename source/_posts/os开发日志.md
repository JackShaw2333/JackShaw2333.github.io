---
title: os开发日志
date: 2021-01-01 17:28:14
tags: os
---



## 名词解释

<!--more-->

- [Multiboot Specification](https://www.gnu.org/software/grub/manual/multiboot/multiboot.html#Motivation): 传统上，每种操作系统都有一套自己的bootloader，如果一台计算机上同时存在着多个这样的操作系统，引导问题将会是一个噩梦。为此，Multiboot Specification诞生了，主要用于开源操作系统。
- [Global Constructors](https://wiki.osdev.org/Calling_Global_Constructors): 用于在kernel的main函数执行前执行其他指令，类似初始化全局变量等无法在runtime时期完成的任务。
- [System V ABI](https://wiki.osdev.org/System_V_ABI): **System V Application Binary Interface**是一套规范，为符合X/Open Common Application Environment Specification和System V Interface Definition的系统规定了calling conventions, object file formats, executable file formats, dynamic linking semantics等等规则。它是现在主流Unix操作系统家族（如Linux，BSD等等）的标准ABI。例如ELF就是System V ABI的一部分。

## 知识点

### Global Constructors

Global Constructors用于在kernel的main函数执行前执行其他指令，类似初始化全局变量等无法在runtime时期完成的任务。

System V ABI规定了5个不同的object文件用于处理程序初始化，它们分别是`crt0.o`, `crti.o`, `crtbegin.o`, `crtend.o`, `crtn.o`。这些object文件共同实现了2个特殊的函数：`_init`用于执行global constructors和其他初始化任务；`_fini`用于执行global destructors和其他终止化(termination)任务。

cross compiler会提供`crtbegin.o`和`crtend.o`。这2个文件包含了编译器希望向你隐藏又希望你使用的internals。为了使用这些信息，你需要提供`crti.o`和`crtn.o`的实现。`crt0.o`包含程序的entry point（通常是`_start`）调用`_init`函数执行由`crti.o`, `crtbegin.o`, `crtend.o`和`crtn.o`共同组成的程序初始化任务，而`exit`函数会调用由上述文件提供的`_fini`函数。

通常，`_init`函数会被存储在`.init` section，`_fini`函数存储在`.fini` section。上述5个文件每个文件都为这2个section贡献自己的代码，然后链接器再将它们链接起来。因此，链接的顺序非常重要。例如考虑将`foo.o`和`bar.o`链接为一个程序`program`，通常我们使用这个命令：

```shell
i686-elf-gcc foo.o bar.o -o program
```

编译器重写上面这行命令，并将下述命令传给链接器：

```shell
i686-elf-ld crt0.o crti.0 crtbegin.o foo.o bar.o crtend.o crtn.o
```

#### 在C语言中使用global constructors

GCC允许C语言将函数作为global constructor运行，例如：

```c
__attribute__((constructor)) void foo(void) {
    printf("foo is running and printf is available at this point\n");
}

int main(int argc, char* argv[]) {
	printf("%s: main is running with argc=%i\n", argv[0], argc);
}
```

#### 在内核中使用`crti.o`, `crtbegin.o`, `crtend.o`, `crtn.o`

在内核中没有用户空间的C库，你可能使用一个特别的C库或者根本没有。编译器总是会提供`crtbegin.o`和`crtend.o`，通常`crti.o`和`crtn.o`由C库提供，但在内核中，很可能需要内核提供`crti.o`和`crtn.o`（即使它们可能和用户空间的C库实现一模一样）。内核在链接时通常使用`-nostdlib`选项（与`-nodefaultlibs`和`-nostartfiles`选项一样），这将使得`crt*.o`文件不会被自动送给链接器，我们必须在命令行手动添加`crti.o`, `crtbegin.o`, `crtend.o`, `crtn.o`。

然而，`crtbegin.o`, `crtend.o`一般都在一个编译器相关的文件夹，为了寻找它们的路径，可以使用下述shell命令，假设`i686-elf-gcc`是cross compiler，`$CFLAGS`是通常提供给编译器的选项：

```shell
i686-elf-gcc $CFLAGS -print-file-name=crtbegin.o
```

来寻找`crtbegin.o`文件。

在编写Makefile时需要注意这4个文件的顺序。

由此，内核会拥有`_init`和`_fini`函数，它们可以被内核的entry point object文件调用，而这一切都发生在内核主要代码运行之前（后）。请注意此时内核可能根本还未初始化，你只能用这些global constructors做一些琐碎的任务。而且`_fini`可能根本不会被调用，因为除了处理器reset之外可能没有什么有价值的工作。

相比之下，设置一个用于初始化堆、日志和其他内核特征的函数可能更有用。可以让`boot.o`调用这个函数，然后调用`_init`，最后把控制转移到内核中。

其实，这与在用户空间中使用这4个文件的过程类似。

---

## 6.828

### lab1

### lab2

计算机的CMOS RAM硬件中储存着诸如PC中物理内存大小之类的信息，在进行内存管理时需要读取其中的内容

## GCC编译选项

- `-ggdb`: 使 GCC 为 GDB 生成专用的更为丰富的调试信息，但是，此时就不能用其他的调试器来进行调试
- `-gstabs`: 以stabs格式声称调试信息，但是不包括gdb调试信息
- `-fno-PIC`: 等于`-fno-pic`，禁止位置相关的代码
- `-fno-builtin`: 在定义函数的时候和C语言的内建函数重名了，不使用C语言的内建函数，而使用自己定义的
- `-Wall`: 显示所有警告
- `-Werror`: 警告提升为错误
- `-march=`: 生成为指定架构优化的代码
- `-m32`: 生成32位的汇编代码
- `-nostdinc`: 不要搜索标准系统头文件目录，仅搜索`-I`指定的路径
- `-fno-stack-protector`: 禁止栈保护
