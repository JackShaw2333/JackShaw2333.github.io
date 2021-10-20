---
title: 剑指offer 14-2. 剪绳子2
date: 2020-08-03 22:08:42
tags:
---

# 题目

给你一根长度为`n`的绳子，请把绳子剪成整数长度的`m`段（m、n都是整数，n>1并且m>1），每段绳子的长度记为`k[0],k[1]...k[m - 1]`。请问`k[0]*k[1]*...*k[m - 1]`可能的最大乘积是多少？例如，当绳子的长度是8时，我们把它剪成长度分别为2、3、3的三段，此时得到的最大乘积是18。

答案需要取模 1e9+7（1000000007），如计算初始结果为：1000000008，请返回 1。

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

`2 <= n <= 1000`

来源：力扣（LeetCode）

链接：https://leetcode-cn.com/problems/jian-sheng-zi-ii-lcof

著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

---

难度：中等

标签：数学，动态规划

# 我的解答

本题与`剑指offer14-1 剪绳子`类似，仅仅提出了取模要求，为此，根据取模运算的规律，`(a * b) % p = (a % p * b % p) % p`，我对代码进行了修改。

```java
class Solution {

    private static final int INF = (int) 1e9 + 7;

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
                ans = pow(3, a-1) * 4 % INF;
                break;
            case 2:
                ans = pow(3, a) * 2 % INF;
                break;
        }
        return ans;
    }

    private int pow(int base, int index) {
        int ans = 1;
        while (index != 0) {
            if ((index & 1) == 1) {
                ans = ans * base % INF;
            }
            base = base * base % INF;
            index >>= 1;
        }
        return ans;
    }
}
```