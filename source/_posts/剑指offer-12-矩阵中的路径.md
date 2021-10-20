---
title: 剑指offer 12. 矩阵中的路径
date: 2020-07-26 15:09:24
tags:
---

# 题目

请设计一个函数，用来判断在一个矩阵中是否存在一条包含某字符串所有字符的路径。路径可以从矩阵中的任意一格开始，每一步可以在矩阵中向左、右、上、下移动一格。如果一条路径经过了矩阵的某一格，那么该路径不能再次进入该格子。例如，在下面的3×4的矩阵中包含一条字符串“bfce”的路径（路径中的字母用加粗标出）。

[["a","**b**","c","e"],  
["s","**f**","**c**","s"],  
["a","d","**e**","e"]]

但矩阵中不包含字符串“abfb”的路径，因为字符串的第一个字符b占据了矩阵中的第一行第二个格子之后，路径不能再次进入这个格子。

<!--more-->

示例 1：
```
输入：board = [["A","B","C","E"],["S","F","C","S"],["A","D","E","E"]], word = "ABCCED"
输出：true
```

示例 2：
```
输入：board = [["a","b"],["c","d"]], word = "abcd"
输出：false
```
提示：

- `1 <= board.length <= 200`
- `1 <= board[i].length <= 200`

来源：力扣（LeetCode）

链接：https://leetcode-cn.com/problems/ju-zhen-zhong-de-lu-jing-lcof

著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

---

难度：中等

标签：动态规划

# 我的解答

这是一个非常典型的图的深度搜索问题。

- 需要对`board`中的每一个位置进行一次深度优先搜索，如果某个位置的深度优先搜索成功，那么就可以返回`true`。
- 每次深度搜索时，需要一张表`visited[][]`记录当前搜索过程是否访问过某个节点。
- 搜索`board`的某个位置时
  - 首先判断该位置的字符是否是目标字符，若不是则从当前位置返回`false`
  - 否则判断该位置是否访问过，若访问过则从当前位置返回`false`
  - 否则在`visited`表中将该位置的状态置位
  - 然后判断当前位置是否判断到目标路径的最后一个字符，如果是说明搜索成功，返回`true`
  - 否则继续搜索当前位置的上下左右4个方向的字符，若某个方向搜索成功，则可提前返回`true`
  - 若4个方向的搜索都失败了，那么从该字符回退，将`visited`表中该位置的状态复位，然后返回`false`

```java
// Java
class Solution12 {
    private static boolean[][] flags;
    private static int[][] step = {
            // row col
            {0, -1},     // left
            {0, 1},     // right
            {-1, 0},    // up
            {1, 0},     // down

    };

    public boolean exist(char[][] board, String word) {

        flags = new boolean[board.length][board[0].length];

        for (int i = 0; i < board.length; ++i) {
            for (int j = 0; j < board[0].length; ++j) {
                if (search(i, j, board, 0, word)) {
                    return true;
                }
            }
        }

        return false;

    }

    private boolean search(int i, int j, char[][] board, int iWord, String word) {

        // 不符合目标字符or已访问过，返回false
        if (board[i][j] != word.charAt(iWord) || flags[i][j]) {
            return false;
        }

        flags[i][j] = true;

        if (iWord == word.length() - 1) {
            return true;
        }


        for (int k = 0; k < 4; ++k) {
            int newI = i + step[k][0];
            int newJ = j + step[k][1];
            if (newI < 0 || newI >= board.length || newJ < 0 || newJ >= board[0].length) {
                continue;
            }

            if (search(newI, newJ, board, iWord + 1, word)) {
                return true;
            }
        }

        flags[i][j] = false;
        return false;
    }
}
```