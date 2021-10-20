---
title: 剑指offer 27. 二叉树的镜像
date: 2020-08-06 10:40:15
tags:
---

# 题目

请完成一个函数，输入一个二叉树，该函数输出它的镜像。

<!--more-->

例如输入：
```
     4
   /   \
  2     7
 / \   / \
1   3 6   9
```

镜像输出：
```
     4
   /   \
  7     2
 / \   / \
9   6 3   1
```
 

示例 1：
```
输入：root = [4,2,7,1,3,6,9]
输出：[4,7,2,9,6,3,1]
```

限制：

`0 <= 节点个数 <= 1000`

来源：力扣（LeetCode）

链接：https://leetcode-cn.com/problems/er-cha-shu-de-jing-xiang-lcof

著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

---

难度：简单

标签：树

# 我的解答

这个镜像翻转问题非常简单。

首先从树根开始翻转左右子树的位置，然后递归翻转左右子树即可，因为翻转左右子树的过程完全不会影响在这之前已发生过的翻转。

```java
// Java
class Solution {
    public TreeNode mirrorTree(TreeNode root) {
        if (root == null) {
            return null;
        }
        TreeNode t = root.left;
        root.left = root.right;
        root.right = t;
        mirrorTree(root.left);
        mirrorTree(root.right);
        return root;
    }
}
```