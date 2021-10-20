---
title: 剑指offer 09. 用两个栈实现队列
date: 2020-07-24 09:58:26
tags:
---

# 题目

用两个栈实现一个队列。队列的声明如下，请实现它的两个函数 appendTail 和 deleteHead ，分别完成在队列尾部插入整数和在队列头部删除整数的功能。(若队列中没有元素，deleteHead 操作返回 -1 )

<!--more-->

示例 1：
```
输入：
["CQueue","appendTail","deleteHead","deleteHead"]
[[],[3],[],[]]
输出：[null,null,3,-1]
```

示例 2：
```
输入：
["CQueue","deleteHead","appendTail","appendTail","deleteHead","deleteHead"]
[[],[],[5],[2],[],[]]
输出：[null,-1,null,null,5,2]
```

提示：

- `1 <= values <= 10000`
- `最多会对 appendTail、deleteHead 进行 10000 次调用`

来源：力扣（LeetCode） 

链接：https://leetcode-cn.com/problems/yong-liang-ge-zhan-shi-xian-dui-lie-lcof

著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

---

难度：简单

标签：栈，设计

# 我的解答

用栈模拟队列，关键在于出队操作的实现。出队时，为了弹出队首（即栈底）的元素，肯定需要一个辅助栈来实现主栈的元素倒置操作，然后辅助栈的栈顶即为需要弹出的队首元素。

值得注意的是，实现一次主栈元素倒置到辅助栈后，辅助栈的出栈顺序就是队列的出队顺序，所以若辅助栈不为空，只需继续弹出辅助栈栈顶就可实现出队操作。只有当辅助栈为空时，才需要进行一次主栈元素到辅助栈的倒置。

```java
// Java
class CQueue {

    Deque<Integer> mainStack;
    Deque<Integer> backupStack;

    public CQueue() {
        this.mainStack = new ArrayDeque<>();
        this.backupStack = new ArrayDeque<>();
    }

    public void appendTail(int value) {
        this.mainStack.addFirst(value);
    }

    public int deleteHead() {

        // backupStack含元素，删除的元素必定就是backupStack的栈顶元素
        if (this.backupStack.size() > 0) {
            return this.backupStack.pollFirst();
        }

        // backupStack为空，则需要使用backupStack将栈倒置，然后删除栈顶
        while (this.mainStack.size() > 0 ) {
            this.backupStack.addFirst(this.mainStack.pollFirst());
        }

        if (this.backupStack.size() == 0) {
            return -1;
        }

        int res = this.backupStack.pollFirst();

        return res;
    }
}
```
