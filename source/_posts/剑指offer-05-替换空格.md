---
title: 剑指offer 05. 替换空格
date: 2020-07-22 10:47:20
tags:
---

# 题目

请实现一个函数，把字符串 s 中的每个空格替换成"%20"。

<!--more-->

示例 1：

```
输入：s = "We are happy."

输出："We%20are%20happy."
```

限制：

`0 <= s 的长度 <= 10000`

来源：力扣（LeetCode）

链接：https://leetcode-cn.com/problems/ti-huan-kong-ge-lcof

著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

---

难度：简单

# 我的解答

首先创建1个结果字符串用以保存结果，然后遍历输入字符串中的每个字符，若该字符为空格，则在结果字符串中添加`%20`，否则直接将该字符添加到结果字符串。遍历结束后返回结果字符串。

在Java中为保证效率，采用`StringBuffer`作为结果字符串，在本题单线程的情况下，也可以采用`StringBuilder`，效率更高。

```java
// Java
class Solution05 {
    public String replaceSpace(String s) {
        StringBuffer sb = new StringBuffer();
        for (int i = 0; i < s.length(); ++i) {
            char c = s.charAt(i);
            if (c == ' ') {
                sb.append("%20");
            } else {
                sb.append(c);
            }
        }
        return sb.toString();
    }
}
```