---
title: 剑指offer 06. 从尾到头打印链表
date: 2020-07-22 11:07:38
tags:
---

# 题目

输入一个链表的头节点，从尾到头反过来返回每个节点的值（用数组返回）。

<!--more--> 

示例 1：

```
输入：head = [1,3,2]
输出：[2,3,1]
```

限制：

`0 <= 链表长度 <= 10000`

来源：力扣（LeetCode）

链接：https://leetcode-cn.com/problems/cong-wei-dao-tou-da-yin-lian-biao-lcof

著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

---

难度：简单

标签：链表

# 我的解答

为了实现链表的倒置，我设置了3个指针`i`、`j`、`k`，分别指向当前已倒向链表的第一个节点、未倒向链表的第一个节点和未倒向链表的第二个节点。链表倒置后，再遍历链表，将访问到的元素值存入返回数组后返回。

倒置操作需要首先在循环中遍历链表。在循环的每一轮中，让`j.next`指向`i`，然后`i`指向`j`，`j`指向`k`，`k`指向`k.next`，然后开始下一轮循环，这样就实现了链表的倒置。需要注意的是，题目给出的链表**没有头节点**，第一个节点就含有有效数据。

在倒置链表的同时，统计链表中元素的个数`cnt`，然后就可以开一个最小的数组，节省空间。

代码先后遍历了2遍链表，为返回数组开辟了大小为n的空间。  
时间复杂度为`O(n)`；  
空间复杂度为`O(n)`。

```java
// Java
class Solution {
    public int[] reversePrint(ListNode head) {

        // 如果是空链表，返回空数组
        if (head == null) {
            return new int[0];
        }

        int cnt = 1;

        ListNode i = head;

        // 如果只有1个节点的链表，返回长度为1的数组
        if (i.next == null) {
            int[] res = new int[1];
            res[0] = i.val;
            return res;
        }

        ListNode j = i.next;
        ListNode k = j.next;

        i.next = null;

        // 循环遍历链表并倒置
        while (j != null) {
            j.next = i;
            ++cnt;
            i = j;
            j = k;
            if (k != null) {
                k = k.next;
            }
        }

        // 再遍历一遍链表，储存逆序的元素顺序
        int[] res = new int[cnt];
        int p = 0;
        while (i != null) {
            res[p++] = i.val;
            i = i.next;
        }

        return res;
    }
}
```

# 优秀题解

## [栈][优秀题解-栈]


使用**栈**实现链表的倒置。

创建1个栈，在遍历链表的同时将节点值压入栈中。遍历结束后，将栈中元素依次出栈。

链表中的元素被遍历了两次，一次是遍历链表压栈操作，另一次是元素出栈放入数组。空间上，在压栈时需要与链表等长的栈，在出栈时栈的长度与返回数组的长度之和仍为链表的长度。由此时间复杂度为`O(n)`，空间复杂度为`O(n)`。

相比我的方法，这个方法的优点：

- 简单易懂，实现不复杂
- 特殊情况只需考虑空链表，不需考虑单节点链表，代码逻辑简洁
- 时间复杂度的常数因此更小，因为循环时的基本操作更少

同时，该方法也有个小小的缺点：在第一次遍历压栈开始到结束的过程中，使用的空间长度从`0`逐步增长到链表长度`n`，然后直到函数返回前，使用的空间长度都为`n`；而我的方法使用的空间长度只在第二次遍历倒置链表的过程中从`0`增长到`n`，之前只有3个指针使用少量空间。

不过这个缺点几乎不值一提，综合来说，该方法优于我的方法。

```java
// Java
class Solution {
    public int[] reversePrint(ListNode head) {
        if (head == null) {
            return new int[0];
        }

        Stack<Integer> stack = new Stack<>();

        ListNode p = head;
        while (p != null) {
            stack.push(p.val);
            p = p.next;
        }

        int size = stack.size();
        int[] res = new int[size];
        for (int i = 0; i < size; ++i) {
            res[i] = stack.pop();
        }

        return res;
    }
}
```

## [递归][优秀题解-递归]

递归法从头部开始进行递归，一直递归到最后一个节点，然后递归返回时将当前节点的值放入提前定义好的可变长数组`list`中，这样就完成了元素值的倒置处理。  
递归结束后，将变长数组中的值处理到返回数组中再返回即可。

该方法的时间复杂度与空间复杂度与前2个方法相同，然而该方法的缺点也显而易见：
- `O(n)`长度的递归，容易造成栈溢出。递归本身也是利用了调用栈，但相比直接利用栈的方法，该方法耗费空间更大

```java
class Solution{

    ArrayList<Integer> list = new ArrayList<>();
    public int[] reversePrint(ListNode head) {
        recur(head);
        int size = list.size();
        int[] res = new int[size];
        for (int i = 0; i < size; ++i) {
            res[i] = list.get(i);
        }

        return res;
    }

    public void recur(ListNode head) {
        if (head == null) {
            return;
        }

        recur(head.next);
        list.add(head.val);
    }
}
```

[优秀题解-栈]: https://leetcode-cn.com/problems/cong-wei-dao-tou-da-yin-lian-biao-lcof/solution/mian-shi-ti-06-cong-wei-dao-tou-da-yin-lian-biao-b/

[优秀题解-递归]: https://leetcode-cn.com/problems/cong-wei-dao-tou-da-yin-lian-biao-lcof/solution/mian-shi-ti-06-cong-wei-dao-tou-da-yin-lian-biao-d/