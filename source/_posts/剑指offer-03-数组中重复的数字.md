---
title: 剑指offer 03. 数组中重复的数字
date: 2020-07-21 23:26:56
tags:
---

# 题目

找出数组中重复的数字。


在一个长度为 n 的数组 nums 里的所有数字都在 0～n-1 的范围内。数组中某些数字是重复的，但不知道有几个数字重复了，也不知道每个数字重复了几次。请找出数组中任意一个重复的数字。

<!--more-->

示例 1：

```
输入：
[2, 3, 1, 0, 2, 5, 3]
输出：2 或 3 
```

限制：

`2 <= n <= 100000`

来源：力扣（LeetCode）

链接：https://leetcode-cn.com/problems/shu-zu-zhong-zhong-fu-de-shu-zi-lcof、

著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

---

难度：简单

标签：数组，哈希表

# 我的解答

题目要求找到第一个重复的数字，看到“**找重复**”等要求，立刻想到哈希表。利用哈希表快速查找的特点而涉及的哈希集合可满足题目的要求，所以我采用了实现了`Set`接口的[`HashSet`][HashSet]。

```java
// Java
class Solution {

    public int findRepeatNumber(int[] nums) {
        Set<Integer> set = new HashSet<>();
        int num = 0;

        // 遍历nums中的数字，如果使用添加函数.add()返回false，
        // 则说明哈希集中以存在该元素，则该元素为第一个遇到的重复值。
        // 返回该元素并且跳出循环。
        for (int n : nums) {
            if (!set.add(n)) {
                num = n;
                break;
            }
        }
        return num;
    }
}
```

[HashSet]: https://docs.oracle.com/javase/7/docs/api/java/util/HashSet.html