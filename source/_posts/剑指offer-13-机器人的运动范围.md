---
title: 剑指offer 13. 机器人的运动范围
date: 2020-07-27 09:36:55
tags:
---

# 题目

地上有一个m行n列的方格，从坐标 [0,0] 到坐标 [m-1,n-1] 。一个机器人从坐标 [0, 0] 的格子开始移动，它每次可以向左、右、上、下移动一格（不能移动到方格外），也不能进入行坐标和列坐标的数位之和大于k的格子。例如，当k为18时，机器人能够进入方格 [35, 37] ，因为3+5+3+7=18。但它不能进入方格 [35, 38]，因为3+5+3+8=19。请问该机器人能够到达多少个格子？

<!--more--> 

示例 1：
```
输入：m = 2, n = 3, k = 1
输出：3
```

示例 2：
```
输入：m = 3, n = 1, k = 0
输出：1
```

提示：

- `1 <= n,m <= 100`
- `0 <= k <= 20`

来源：力扣（LeetCode）

链接：https://leetcode-cn.com/problems/ji-qi-ren-de-yun-dong-fan-wei-lcof

著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

---

难度：中等

# 我的解答

这是一个非常典型的图搜索的问题，用DFS和BFS都可以。

## DFS

```java
// Java
// DFS
class Solution {

    private static boolean visited[][];

    public int movingCount(int m, int n, int k) {

        visited = new boolean[m][n];
        return dfs(0, 0, m, n, k);

    }

    private int dfs(int i, int j, int m, int n, int k) {
        if (i < 0 || i >=m || j < 0 || j >=n || visited[i][j] || !isValid(i, j, k)) {
            return 0;
        }

        visited[i][j] = true;

        return 1 + dfs(i, j - 1, m, n, k) + dfs(i, j + 1, m, n, k) + dfs(i - 1, j, m, n, k) + dfs(i + 1, j, m, n, k);

    }

    private boolean isValid(int i, int j, int k) {
        int n = 0;
        while (i > 0) {
            n += i % 10;
            i /= 10;
        }

        while (j > 0) {
            n += j % 10;
            j /= 10;
        }

        return n <= k;
    }
}
```

## BFS

采用BFS搜索时，只需考虑向右和向下两个方向

```java
// Java
// BFS
class Solution {

    private static boolean visited[][];
    private static int step[][] = {{1, 0},
                                    {0, 1}};

    public int movingCount(int m, int n, int k) {
        visited = new boolean[m][n];
        return bfs(0, 0, m, n, k);
    }

    private int bfs(int i, int j, int m, int n, int k) {
        Queue<Integer> q = new LinkedList<>();
        int num = 1;

        visited[i][j] = true;
        q.offer(i);
        q.offer(j);

        while (q.size() > 0) {
            int tI = q.poll();
            int tJ = q.poll();
            for (int v = 0; v < 2; ++v) {
                  int ttI = tI + step[v][0];
                  int ttJ = tJ + step[v][1];
                if (ttI >= m || ttJ >= n || visited[ttI][ttJ] || !isValid(ttI, ttJ, k)) {
                    continue;
                }

                q.offer(ttI);
                q.offer(ttJ);
                visited[ttI][ttJ] = true;
                ++num;
            }
        }

        return num;
    }

    private boolean isValid(int i, int j, int k) {
        int n = 0;
        while (i > 0) {
            n += i % 10;
            i /= 10;
        }

        while (j > 0) {
            n += j % 10;
            j /= 10;
        }

        return n <= k;
    }
}
```

## 递推

通过观察，这个图搜索问题可以进一步简化。

- 搜索方向只需考虑向右和向下，这一点BFS时已经指出
- 只要当前格子的行列坐标数位之和小于k，且机器人可以从该格子的左边或上面到达，那么当前格子就可标记为可到达

为此，只需要递推即可

```java
class Solution {

    public int movingCount(int m, int n, int k) {
        boolean visited[][] = new boolean[m][n];

        visited[0][0] = true;

        int ans = 1;

        for (int i = 0; i < m; ++i) {
            for (int j = 0; j < n; ++j) {
                if (isValid(i, j, k) && (i != 0 || j != 0)) {
                    if (i > 0) {
                        visited[i][j] |= visited[i - 1][j];
                    }
                    if (j > 0) {
                        visited[i][j] |= visited[i][j - 1];
                    }
                    if (visited[i][j]) {
                        ++ans;
                    }
                }
            }
        }

        return ans;

    }

    private boolean isValid(int i, int j, int k) {
        int n = 0;
        while (i > 0) {
            n += i % 10;
            i /= 10;
        }

        while (j > 0) {
            n += j % 10;
            j /= 10;
        }

        return n <= k;
    }
}
```
