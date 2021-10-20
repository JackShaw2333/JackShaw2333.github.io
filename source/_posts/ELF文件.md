---
title: ELF文件
date: 2020-07-26 22:00:34
tags:
---

本文参考自[《Linux C编程一站式学习》][reference-book]

<!--more-->

# 什么是ELF文件？

ELF文件格式是一个开放标准，各种UNIX系统的可执行文件都采用ELF格式，有3种不同的类型：
- 可重定位的目标文件（Relocatable）
- 可执行文件（Executable）
- 共享库（Shared Object）

## 目标文件

ELF文件格式提供了2种视角：
- 在汇编器和链接器看来，ELF文件是由Section Header Table描述的一系列Section的集合
- 在加载器看来，ELF文件是由Program Header Table描述的一系列Segment的集合

```
+----------------------+
| ELF header           |
+----------------------+
| program header table |
+----------------------+
| ...                  |
+----------------------+
| ...                  |
+----------------------+
| section header table |
+----------------------+
```

需要注意的是，Section Header Table和Program Header Table的相对位置并非严格按照上图所示。

开头的ELF Header描述了体系结构和操作系统等基本信息，指出了Section Header Table和Program Header Table在文件中的位置。

从汇编器和链接器的角度看：  
- Program Header Table在汇编和链接的过程中用不到，可有可无
- Section Header Table保存了所有Section的描述信息

从加载器的角度看：
- Program Header Table保存了所有Segment的描述信息
- Section Header Table在加载过程中没用到，可有可无

汇编程序中用`.section`声明的Section会成为目标文件中的Section，汇编器还会自动添加一些Section。

Segment是**在程序运行时加载到内存的具有相同属性的区域**，由若干个Section组成。

- 目标文件需要链接器进一步处理，所以一定要有Section Header Table
- 可执行文件需要加载器运行，所以一定要有Program Header Table
- 共享库既要加载运行，又要加载时做动态链接，所以两者都要

可以使用`readelf`命令读出目标文件的ELF Header和Section Header Table。

```shell
readelf -a xxx.o
```

Section Header Table的信息格式如下所示。
```
Section Headers:
  [Nr] Name              Type             Address           Offset
       Size              EntSize          Flags  Link  Info  Align
  [ 0]                   NULL             0000000000000000  00000000
       0000000000000000  0000000000000000           0     0     0
  [ 1] .text             PROGBITS         0000000000000000  00000040
       0000000000000099  0000000000000000  AX       0     0     1
  [ 2] .rela.text        RELA             0000000000000000  000003a0
       0000000000000108  0000000000000018   I      10     1     8
  [ 3] .data             PROGBITS         0000000000000000  000000d9
       0000000000000000  0000000000000000  WA       0     0     1
  [ 4] .bss              NOBITS           0000000000000000  000000d9
       0000000000000000  0000000000000000  WA       0     0     1
  [ 5] .rodata           PROGBITS         0000000000000000  000000e0
       000000000000004c  0000000000000000   A       0     0     8
  [ 6] .comment          PROGBITS         0000000000000000  0000012c
       000000000000002e  0000000000000001  MS       0     0     1
  [ 7] .note.GNU-stack   PROGBITS         0000000000000000  0000015a
       0000000000000000  0000000000000000           0     0     1
  [ 8] .eh_frame         PROGBITS         0000000000000000  00000160
       0000000000000058  0000000000000000   A       0     0     8
  [ 9] .rela.eh_frame    RELA             0000000000000000  000004a8
       0000000000000030  0000000000000018   I      10     8     8
  [10] .symtab           SYMTAB           0000000000000000  000001b8
       0000000000000198  0000000000000018          11     9     8
  [11] .strtab           STRTAB           0000000000000000  00000350
       000000000000004a  0000000000000000           0     0     1
  [12] .shstrtab         STRTAB           0000000000000000  000004d8
       0000000000000061  0000000000000000           0     0     1
```

从Section Header Table可读出各Section的描述信息。
- 列`Address`指出了这些段加载到内存中的地址，加载地址在链接过程填写，现在空缺，所以全是0
- 列`Offset`和`Size`指出了各Section的文件地址

`.shstrtab`保存各Section的名字，`.strtab`保存程序中用到的符号的名字，每个名字都是以`\0`结尾的字符串。

在C语言中，未初始化的全局变量如果在代码中没有初始化，就会在程序加载时用`0`初始化，这种数据属于`.bss`段，在加载时和`.data`段一样都是可读可写的数据，但在ELF文件中`.data`段占用一部分空间保存初始值，而`.bss`段不需要。  
程序加载时`.bss`段所占内存空间在Section Header中描述。

`.rel.text`告诉链接器指令中的哪些地方需要重定位。

`symtab`是符号表，如下所示。
```
Symbol table '.symtab' contains 17 entries:
   Num:    Value          Size Type    Bind   Vis      Ndx Name
     0: 0000000000000000     0 NOTYPE  LOCAL  DEFAULT  UND
     1: 0000000000000000     0 FILE    LOCAL  DEFAULT  ABS inet_aton.c
     2: 0000000000000000     0 SECTION LOCAL  DEFAULT    1
     3: 0000000000000000     0 SECTION LOCAL  DEFAULT    3
     4: 0000000000000000     0 SECTION LOCAL  DEFAULT    4
     5: 0000000000000000     0 SECTION LOCAL  DEFAULT    5
     6: 0000000000000000     0 SECTION LOCAL  DEFAULT    7
     7: 0000000000000000     0 SECTION LOCAL  DEFAULT    8
     8: 0000000000000000     0 SECTION LOCAL  DEFAULT    6
     9: 0000000000000000    89 FUNC    GLOBAL DEFAULT    1 main
    10: 0000000000000000     0 NOTYPE  GLOBAL DEFAULT  UND inet_aton
    11: 0000000000000059    64 FUNC    GLOBAL DEFAULT    1 error_handling
    12: 0000000000000000     0 NOTYPE  GLOBAL DEFAULT  UND printf
    13: 0000000000000000     0 NOTYPE  GLOBAL DEFAULT  UND stderr
    14: 0000000000000000     0 NOTYPE  GLOBAL DEFAULT  UND fputs
    15: 0000000000000000     0 NOTYPE  GLOBAL DEFAULT  UND fputc
    16: 0000000000000000     0 NOTYPE  GLOBAL DEFAULT  UND exit
```

`Ndx`列是每个符号所在的Section的编号，各Section的编号在Section Header Table中指出。

`Value`列是每个符号所代表的地址，在目标文件中，符号地址是相对于该符号所在的Section的相对地址。

## 可执行文件

[reference-book]: http://staff.ustc.edu.cn/~guoyan/os12/LinuxC.pdf