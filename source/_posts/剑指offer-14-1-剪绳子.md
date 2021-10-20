---
title: 剑指offer 14-1. 剪绳子
date: 2020-07-27 09:53:56
tags:
---

# 题目

给你一根长度为`n`的绳子，请把绳子剪成整数长度的`m`段（m、n都是整数，n>1并且m>1），每段绳子的长度记为`k[0],k[1]...k[m-1]`。请问`k[0]*k[1]*...*k[m-1]`可能的最大乘积是多少？例如，当绳子的长度是8时，我们把它剪成长度分别为2、3、3的三段，此时得到的最大乘积是18。

<!--more-->

示例 1：
```
输入: 2
输出: 1
解释: 2 = 1 + 1, 1 × 1 = 1
```

示例 2:
```
输入: 10
输出: 36
解释: 10 = 3 + 3 + 4, 3 × 3 × 4 = 36
```

提示：

`2 <= n <= 58`

来源：力扣（LeetCode）

链接：https://leetcode-cn.com/problems/jian-sheng-zi-lcof

著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

---

难度：中等

标签：数学，动态规划

# 我的解答

## 动态规划

这个问题很明显的隐含了重叠子问题，而且满足最优子结构的性质。

动态规划的想法是在绳子的所有有效整数位置剪一刀，将绳子分为长度为`i`的绳子和长度为`m - i`的绳子，如此，问题得到分解。设函数`dp[i]`表示长度为`i`的绳子所能剪出的最大乘积。那么`dp[n]`的解只可能是以下4种情况的最大值：
- `dp[i] * dp[n-i]`
- `i * dp[n-i]`
- `dp[i] * (n-i)`
- `i * (n-i)`

其中`i = 1, 2, ..., n//2`。

```java
// Java
class Solution {
    public int cuttingRope(int n) {
        int[] dp = new int[n + 1];

        for (int i = 1; i <= n; ++i) {
            int max = 1;
            for (int j = 1; j <= i / 2; ++j) {
                int[] t = new int[4];
                t[0] = dp[j] * dp[i - j];
                t[1] = dp[j] * (i - j);
                t[2] = j * dp[i - j];
                t[3] = j * (i - j);
                int tMax = maxOf(t);
                max = tMax > max ? tMax : max;
            }
            dp[i] = max;
        }

        return dp[n];
    }

    private int maxOf(int[] t) {
        int max = Integer.MIN_VALUE;
        for(int n : t) {
            max = n > max ? n : max;
        }
        return max;
    }
}
```

## 数学方法分析

参考了[优秀题解]

本题的限制条件是若干个变量之和为定值，求这些变量之积的最大值。由此我们可以想到**算术几何均值不等式**，即算术均值≥几何均值，当且仅当所有变量的值相等取等号。

利用这个不等式，当每段绳子的长度相等时能取到乘积的最大值，所以当前问题在于求出每段绳子的长度应该是多少。

设每段绳子长度为`x`，那么一共可以分为`n/x`段，那么乘积为`y = x^(n/x)`，这里`x`是变量，`n`是常量，为了求最大值，我们对`x`求导。  

![adGVhQ.png](https://s1.ax1x.com/2020/08/03/adGVhQ.png)

令`dy/dx`等于0，求出解为自然对数`e`，验证后此点为极大值点。

由于切分长度`x`必须为整数，候选解为2或3，验证后发现3更优。

由此得出，要将绳子的长度尽可能以长度3分为等分。

为此，得出算法的流程：
- 若绳子长度`n`小于3，题目要求必须切一刀，那么就切成长度为1和`n-1`的即可
- 否则尽可能多地切成长度为3的等分，多余的绳子长度有以下3种情况
  - 多余长度为1，此时不如将最后一段长度为3的等分和此多余长度按2*2切分，这样乘积更大
  - 多余长度为2，此时绳子被切成若干长度为3的等分和1截长度为2的绳子

```java
// Java
class Solution {
    public int cuttingRope(int n) {
        if (n <= 3) {
            return n - 1;
        }
        int a = n / 3;
        int b = n % 3;
        int ans = 1;
        switch (b) {
            case 0:
                ans = pow(3, a);
                break;
            case 1:
                ans = pow(3, a-1) * 4;
                break;
            case 2:
                ans =  pow(3, a) * 2;
                break;
        }
        return ans;
    }

    private int pow(int base, int index) {
        int ans = 1;
        for (int i = 0; i < index; ++i) {
            ans *= base;
        }
        return ans;
    }
}
```

上述代码中我使用了自己实现的快速幂函数，其实可以使用数学包`Math`中的函数。

[优秀题解]: https://leetcode-cn.com/problems/jian-sheng-zi-lcof/solution/mian-shi-ti-14-i-jian-sheng-zi-tan-xin-si-xiang-by/

