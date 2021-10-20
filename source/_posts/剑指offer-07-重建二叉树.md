---
title: 剑指offer 07. 重建二叉树
date: 2020-07-23 10:19:08
tags:
---

# 题目

输入某二叉树的前序遍历和中序遍历的结果，请重建该二叉树。假设输入的前序遍历和中序遍历的结果中都不含重复的数字。

<!--more-->

例如，给出

```
前序遍历 preorder = [3,9,20,15,7]
中序遍历 inorder = [9,3,15,20,7]
```

返回如下的二叉树：

```
    3
   / \
  9  20
    /  \
   15   7
```

限制：

`0 <= 节点个数 <= 5000`

来源：力扣（LeetCode）

链接：https://leetcode-cn.com/problems/zhong-jian-er-cha-shu-lcof

著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

# 我的解答

首先回顾一下前序遍历和中序遍历：
- 前序遍历：若根节点非空，处理根节点；递归前序遍历左子树；递归前序遍历右子树
- 中序遍历：若根节点非空，递归中序遍历左子树；处理根节点；递归中序遍历右子树

简单地总结一下，即
- 前序遍历顺序：根->左->右
- 中序遍历顺序：左->根->右

那么，根据前序遍历和中序遍历的特点以及题目条件假设——输入的前序遍历和中序遍历的结果中都不含重复的数字——我们可以发现：
- 一棵树前序遍历序列的第一个值一定为根节点的值
- 找到根节点的值后，在该树中序遍历序列里找根节点值的位置，假设此位置为`i`
- 中序遍历序列中`i`之前的则为左子树的中序遍历序列，`i`之后的则为右子树的中序遍历序列。记录这两个序列的长度，假设分别为`l`和`r`
- 回到该树的前序遍历序列，根节点值之后长度为`l`的序列即为左子树的前序遍历序列，剩余的即为右子树的前序遍历序列，长度也一定为`r`
- 到目前为止，已经找出了该树左右子树的前序、中序遍历序列，只需递归处理即可

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */

class Solution07 {
    // 递归
    public TreeNode buildTree(int[] preorder, int[] inorder) {

        if (preorder.length == 0) {
            return null;
        }

        // 前序遍历序列第1个值为根节点的值
        TreeNode root = new TreeNode(preorder[0]);

        // 获得左右子树分别的中序遍历
        List<Integer> leftInorderList = new ArrayList<>();
        int i = 0;
        while (inorder[i] != root.val) {
            leftInorderList.add(inorder[i++]);
        }

        int leftLen = leftInorderList.size();   // 左子树节点个数，也就是函数中局部变量i的值
        int rightLen = inorder.length - leftLen - 1;  // 右子树节点个数

        int[] leftInorder = new int[leftLen];
        for (int j = 0; j < leftLen; ++j) {
            leftInorder[j] = leftInorderList.get(j);
        }

        int[] rightInorder = new int[rightLen];
        for (int j = 0; j < rightLen; ++j) {
            rightInorder[j] = inorder[++i];
        }

        // 获得左右子树分别的前序遍历
        int[] leftPreorder = new int[leftLen];
        int k = 1;
        for (int j = 0; j < leftLen; ++j) {
            leftPreorder[j] = preorder[k++];
        }

        int[] rightPreorder = new int[rightLen];
        for (int j = 0; j < rightLen; ++j) {
            rightPreorder[j] = preorder[k++];
        }

        root.left = buildTree(leftPreorder, leftInorder);
        root.right = buildTree(rightPreorder, rightInorder);
        
        return root;
    }
}
```

# 优秀题解

## [迭代][优秀题解-迭代]

[优秀题解-迭代]: https://leetcode-cn.com/problems/zhong-jian-er-cha-shu-lcof/solution/mian-shi-ti-07-zhong-jian-er-cha-shu-by-leetcode-s/