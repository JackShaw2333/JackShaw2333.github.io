---
title: 剑指offer 04. 二维数组中的查找
date: 2020-07-22 09:03:11
tags:
---

# 题目

在一个 n * m 的二维数组中，每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序。请完成一个函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。

<!--more-->

示例:

现有矩阵 matrix 如下：

```
[
  [1,   4,  7, 11, 15],
  [2,   5,  8, 12, 19],
  [3,   6,  9, 16, 22],
  [10, 13, 14, 17, 24],
  [18, 21, 23, 26, 30]
]
```

给定 target = `5`，返回 `true`。

给定 target = `20`，返回 `false`。

 

限制：

`0 <= n <= 1000`

`0 <= m <= 1000`

来源：力扣（LeetCode）

链接：https://leetcode-cn.com/problems/er-wei-shu-zu-zhong-de-cha-zhao-lcof

著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

---

难度：简单

标签：数组，双指针

# 我的解答

参考了[官方题解]

## 暴力法

如果采用暴力法，时间复杂度是`O(mn)`，空间复杂度是`O(1)`。暴力法完全没有利用数组每行从左到右递增，每列从上到下递增的信息，所以肯定不是好的解法。

## 如何利用二维数组有序的信息？

如果仔细观察并加以联想可以发现：若将二维数组右上角的数`x`看作树的根节点，那么`x`左边的数小于`x`而`x`下面的数大于`x`，这种结构很像二叉搜索树。因此，我们可以利用在二叉搜索树中搜索节点值的方法对这个二维数组进行搜索。

从最右上的数`x`开始搜索，如果`x`等于目标值则返回`true`；  
如果`x`大于目标值，则移动到左一列开始搜索；  
如果`x`小于目标值，则移动到下一行开始搜索。

可以证明这种方法不会错过目标值。  
若当前元素大于目标值，则根据列的既定顺序，当前元素所在列中下面的元素全部大于目标值，故应该向左查找；  
若当前元素小于目标值，则根骨行的既定顺序，当前元素所在行中左边的元素全部小于目标值，故应该向下查找。

代码逻辑如下：

- 若数组`matrix`为空，返回`false`
- 初始化行下标为`0`，列下标为`matrix[0].length-1`
- 重复以下步骤，直到行or列下标越界
  - 获得当前元素`num`
  - 如果`num`和目标值`target`相等，返回`true`
  - 如果`num`小于`target`，则向下搜索，行下标加1
  - 如果`num`大于`target`，则向左搜索，列下标减1
- 循环结束后若还未返回，则说明未找到与`target`值相等的元素，返回`false`

```java
// Java
class Solution {
    public boolean findNumberIn2DArray(int[][] matrix, int target) {
        int nRows = matrix.length;

        // 如果matrix数组为空，直接返回false
        if (nRows < 1) {
            return false;
        }
        int nCols = matrix[0].length;

        int i = 0;
        int j = nCols - 1;

        while (i < nRows && j >= 0 && target != matrix[i][j] ) {
            if (target < matrix[i][j]) {
                --j;
            } else if (target > matrix[i][j]) {
                ++i;
            }
        }

        if (i >= nRows || j < 0) {
            return false;
        } else {
            return true;
        }
    }
}
```

[官方题解]: https://leetcode-cn.com/problems/er-wei-shu-zu-zhong-de-cha-zhao-lcof/solution/mian-shi-ti-04-er-wei-shu-zu-zhong-de-cha-zhao-b-3/