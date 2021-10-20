---
title: Java队列
date: 2020-07-14 12:32:57
tags:
- Java
---

# Queue
队列的接口（[docs](https://docs.oracle.com/javase/7/docs/api/java/util/Queue.html)），常用的实现类有

- LinkList类（[docs](https://docs.oracle.com/javase/7/docs/api/java/util/LinkedList.html)）
- PriorityQueue类

Queue还有1个子接口Deque（[docs](https://docs.oracle.com/javase/7/docs/api/java/util/Deque.html)），代表1个双端队列，同时可以作为栈使用。

## LinkList（[docs](https://docs.oracle.com/javase/7/docs/api/java/util/LinkedList.html)）

LinkList既实现了List接口，也实现了Deque接口，故其既可作为队列也可作为栈使用。

LinkList作为队列使用时，应使用[`offer()`](https://docs.oracle.com/javase/7/docs/api/java/util/LinkedList.html#offer(E))添加元素；使用[`poll()`](https://docs.oracle.com/javase/7/docs/api/java/util/LinkedList.html#poll())删除元素。

## PriorityQueue

优先队列，出队顺序按元素大小排序。