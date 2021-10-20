---
title: "$bitmap有标记已使用的未用簇"
date: 2021-09-23 21:37:17
tags:
---

## Introduction

由于C盘空间剩余容量警告，使用diskgenius从D盘扩容C盘，但常常在磁盘检查步骤报错：

> $bitmap有标记已使用的未用簇

<!--more-->

## Solution

使用windows自带的`chkdsk`指令，选项为`/f`和`/x`，参数为需要修复的盘符。使用`chkdsk`修复C盘的指令如下所示。

```cmd
chkdsk /f /x c:
```
