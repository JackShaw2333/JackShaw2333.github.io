---
title: log-sum-exp函数
date: 2021-09-25 10:35:39
tags:
---

## Introduction

这个问题来自[动手学深度学习](https://github.com/d2l-ai/d2l-zh/releases/download/v2.0.0-alpha2/d2l-zh.2.0.0-alpha2-pytorch.pdf)124页，来自Github的[d2l-ai/d2l-zh](https://github.com/d2l-ai/d2l-zh)仓库。

<!--more-->

## softmax

在深度学习的分类任务中经常使用softmax函数：

$$
\hat{y_j} = softmax(o_j) = \frac{e^{o_j}}{\sum_k e^{o_k}}
$$

然而在实际计算实现中，如果某个$o_k$非常大，可能导致$e^{o_k}$上溢。

一个自然的想法是在softmax函数运算前从所有的$o_k$中减去$max(o_k)$，然而这样很有可能导致下溢，由于分母下溢，输出的函数值可能无穷大。

因此，一个通用的方法是使用对数函数对softmax函数进行处理：

$$
log(\hat{y_j}) = log(\frac{e^{o_j}}{\sum_k e^{o_k}}) \\
= o_j - log(\sum_k e^{o_k})
$$

此时，上溢的风险仍然存在，但$log(\sum_k e^{o_k})$恰好是log-sum-exp函数，它具有非常好的性质，能够解决上溢问题。

## log-sum-exp

这一部分参考了[What Is the Log-Sum-Exp Function?](https://nhigham.com/2021/01/05/what-is-the-log-sum-exp-function/)的相关内容。

log-sum-exp函数接收一个向量输入并输出一个标量值，它的形式如下所示：

$$
LSE(x) = log \sum_{i=1}^{n} e^{x_{i}}
$$

LSE函数是对向量中某一元素最大值的一个很好的估计，因为

$$
e^{max(x)} \le \sum_{i=1}^{n} e^{x_{i}} \le ne^{x_{i}}, \\

max(x) \le log \sum_{i=1}^{n} e^{x_{i}} \le max(x) + log(n)
$$

更重要的是它另外一个形式

$$
LSE(x) = log \sum_{i=1}^{n} e^{x_{i}} = log(e^a \sum_{i=1}^{n} e^{x_{i} - a}) = a + log \sum_{i=1}^{n} e^{x_{i} - a}
$$

如果$a = max(x)$，LSE的结果就不会导致上溢，softmax计算过程中的上溢问题就可以得到解决。