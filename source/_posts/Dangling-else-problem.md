---
title: Dangling else problem
date: 2021-10-28 22:13:43
tags:
---

## Intro

在解决ambiguous context-free grammar (CFG)转为unambiguous的相关问题中遇到了一个经典的案例——Dangling else problem.

<!--more-->

## Definition & Example

以下来自[维基百科 Dangling else][wiki dangling else]

> The dangling else is a problem in computer programming in which an optional else clause in an if–then(–else) statement results in nested conditionals being ambiguous.

考虑这样一个[例子][dangling else example]：

一个典型的Ambiguous的语法是这样的：

```text
<STMT> -> if <EXPR> then <STMT> | if <EXPR> then <STMT> else <STMT> | <EXPR>
<EXPR> -> some terminal strings
```

```text
if A then
    if B then
        other
else
    other
```

这一段看似unambiguous的（只为了可读性而缩进）代码其实有2种解读的方式，如果使用缩进来区分：

```text
if A then
    if B then
        other
else
    other
```

```text
if A then
    if B then
        other
    else
        other
```

<!--
## Approaches

为了解决这种ambiguity，常见的方法有2种：keeping grammar & changing grammar

### Keeping grammar

常用的语言如C, Java, Pascal使用的是这种方式。他们都遵循一个惯例，即把dangling `else`匹配到前面最近的`if`上，为此这些语言明确的使用了分块的符号，如`{}`。

### Changing grammar

-->

## Solution

为了消除ambiguity，我们可以规定**每个`else`只和最近的`if`匹配**。这样，一组`if else`之间的任何statement不能存在没有`else`匹配的`if then`，否则就会出现`dangling else`。为此，原grammar可以改为：

```text
<STMT>          -> <OPEN STMT> | <CLOSED STMT>
<CLOSED STMT>   -> if <EXPR> then <CLOSED STMT> else <CLOSED STMT> | <EXPR>
<OPEN STMT>     -> if <EXPR> then <STMT> | if <EXPR> then <CLOSED STMT> else <OPEN STMT>
<EXPR>          -> some terminal strings
```

这样修改后的grammar就已经解决了`dangling else`问题，而且它所generate的语言与原grammar相同。

## References

[Dangling else - 维基百科][wiki dangling else]

[How to remove ambiguity - Medium blog by Gianluca][dangling else example]

[The dangling else ambiguity][emory.edu]

[Eliminating Ambiguity][nuim.ie]

[cs5641 - Lecture 6][umn.edu]

[wiki dangling else]: https://en.wikipedia.org/wiki/Dangling_else
[dangling else example]: https://echo-bravo-fox.medium.com/make-a-grammar-unambiguous-4b12554bc3a6#942b
[emory.edu]: http://www.mathcs.emory.edu/~cheung/Courses/561/Syllabus/2-C/dangling-else.html
[nuim.ie]: http://www.cs.nuim.ie/~jpower/Courses/Previous/parsing/node28.html
[umn.edu]: https://www.d.umn.edu/~rmaclin/cs5641/Notes/Lecture6.pdf