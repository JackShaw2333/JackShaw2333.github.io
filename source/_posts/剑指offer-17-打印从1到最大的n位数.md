---
title: 剑指offer 17. 打印从1到最大的n位数
date: 2020-08-04 10:00:12
tags:
---

# 题目

输入数字 n，按顺序打印出从 1 到最大的 n 位十进制数。比如输入 3，则打印出 1、2、3 一直到最大的 3 位数 999。

<!--more-->

示例 1:
```
输入: n = 1
输出: [1,2,3,4,5,6,7,8,9]
```

说明：

- 用返回一个整数列表来代替打印
- n 为正整数

来源：力扣（LeetCode）

链接：https://leetcode-cn.com/problems/da-yin-cong-1dao-zui-da-de-nwei-shu-lcof

著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

---

难度：简单

标签：数学

# 我的解答

最大的n位十进制数一定是`10^n-1`，所以只需要从1打印到`10^n-1`即可。

```java
// Java
class Solution {
    public int[] printNumbers(int n) {
        int upperBound = (int) Math.pow(10, n) - 1;
        int[] res = new int[upperBound];
        for(int i = 0; i < upperBound; ++i) {
            res[i] = i + 1;
        }

        return res;
    }
}
```
