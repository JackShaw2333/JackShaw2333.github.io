---
title: 剑指offer 26. 树的子结构
date: 2020-08-06 10:08:49
tags:
---

# 题目

输入两棵二叉树A和B，判断B是不是A的子结构。(约定空树不是任意一个树的子结构)

B是A的子结构， 即 A中有出现和B相同的结构和节点值。

<!--more-->

例如:
给定的树 A:
```
     3
    / \
   4   5
  / \
 1   2
```

给定的树 B：
```
   4 
  /
 1
```

返回 true，因为 B 与 A 的一个子树拥有相同的结构和节点值。

示例 1：
```
输入：A = [1,2,3], B = [3,1]
输出：false
```

示例 2：
```
输入：A = [3,4,5,1,2], B = [4,1]
输出：true
```

限制：

`0 <= 节点个数 <= 10000`

来源：力扣（LeetCode）

链接：https://leetcode-cn.com/problems/shu-de-zi-jie-gou-lcof

著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

---

难度：中等

标签：树

# 我的解答

参考了[优秀题解]。

对于`判断树B是否是树A子结构`这个问题，很自然的会想到按照类似树先序遍历的顺序进行判断。

问题本身其实可以分解为2种判断问题。
- 树B可能从根上开始就和树A匹配，是树A的子结构
- 或者在树A的左右子树中匹配

第2个问题与我们需要解决的问题本身是一样的，可以递归进行，而第1个问题是一个更小的问题，并且我们要解决的问题包含了这个问题，因为最后如果树B是树A的子结构，我们一定能找到A的某个子树，树B从根开始与之匹配。

这样，我们就需要2个函数，第1个函数对应我们需要解决的问题，仅仅判断树B是否是当前树的子结构，不考虑树B是从树根开始匹配还是在左右子树中匹配；第2个函数对应第1个问题，判断是否在树根上匹配。

在第2个函数中，若树B的根与当前树的根匹配，则还需用第2个函数递归判断树B的左/右子树与树A的左/右子树是否从树根开始匹配，所以这里的逻辑关系是**与**。

在第1个函数中，我们首先用第2个函数判断树B是否从树A的树根开始匹配，若不是，则用第1个函数递归判断树A的左右子树，这里的逻辑关系是**或**，即只要包含其中一种情况即可。

对于空树的处理，在第1个函数中，若树A为空或树B为空，根据题目要求，都不符合，直接返回`false`；在第2个函数中，若树B为空，说明此递归分支之前都未找到不匹配项，则可以返回`true`。

```java
// Java
class Solution {
    // 先序遍历A的每个节点，使用search函数判断
    public boolean isSubStructure(TreeNode A, TreeNode B) {
        return A!= null && B != null && (search(A, B) || isSubStructure(A.left, B) || isSubStructure(A.right, B));
    }

    // 判断以A为根节点的树中是否包含树B且A == B
    private boolean search(TreeNode A, TreeNode B) {
        return B == null || (A != null && A.val == B.val && search(A.left, B.left) && search(A.right, B.right));
    }
}
```


[优秀题解]: https://leetcode-cn.com/problems/shu-de-zi-jie-gou-lcof/solution/mian-shi-ti-26-shu-de-zi-jie-gou-xian-xu-bian-li-p/