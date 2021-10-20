---
title: 剑指offer 10-1. 斐波那契数列
date: 2020-07-24 10:12:38
tags:
---

# 题目

写一个函数，输入 n ，求斐波那契（Fibonacci）数列的第 n 项。斐波那契数列的定义如下：

<!--more-->

```
F(0) = 0,   F(1) = 1
F(N) = F(N - 1) + F(N - 2), 其中 N > 1.
```

斐波那契数列由 0 和 1 开始，之后的斐波那契数就是由之前的两数相加而得出。

答案需要取模 1e9+7（1000000007），如计算初始结果为：1000000008，请返回 1。 

示例 1：
```
输入：n = 2
输出：1
```

示例 2：
```
输入：n = 5
输出：5
```

提示：

`0 <= n <= 100`

来源：力扣（LeetCode）

链接：https://leetcode-cn.com/problems/fei-bo-na-qi-shu-lie-lcof

著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

---

难度：简单

标签：递归

# 我的解答

斐波那契数列的递推公式非常符合动态规划问题的状态转移方程，所以我下意识地采用了长度为2的数组，然后用动态规划的格式写了代码。

```java
// Java
class Solution {

    private int[] tmp = new int[]{0, 1};
    private static final int INF = (int) 1e9+7;

    public int fib(int n) {
        for (int i = 2; i <= n; ++i) {
            tmp[i%2] = (tmp[(i-1)%2] + tmp[(i-2)%2]) % INF;
        }
        return tmp[n%2];
    }
}
```

很容易看到，由于计算某一项的值只依赖前两项，故可以不用数组，直接使用3个变量来保存数据（参考了一下优秀题解）。

```java
// Java
class Solution {

    private static final int INF = (int) 1e9+7;

    public int fib(int n) {

        int a = 0;
        int b = 1;
        int sum = 0;

        for (int i = 0; i < n; ++i) {
            sum = (a + b) % INF;
            a = b;
            b = sum;
        }

        return a;
    }
}
```

正如标签所指出的，这个问题还有一个非常常见的解法就是递归，但是递归的缺点非常明显，如果不开辟内存空间记录递归过程中计算的值，将导致非常严重的重复计算，而且`O(n)`规模的递归非常容易用尽栈空间导致溢出，所以不推荐递归的方法。