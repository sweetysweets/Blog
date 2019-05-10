---
title: 数据结构之链表
date: 2019-02-28 14:04:04
tags:
- Data Structure
---

## 链表

和数组相比，链表是一种在物理内存上不连续的数据结构。链表的特点是

- 插入删除快
- 查找慢

链表有单链表，双链表，循环单链表，循环双链表，可以方便的解决一些算法中的问题。

<!--more-->

## Java的LinkedList

```java
LinkedList<E>
    extends AbstractSequentialList<E>
    implements List<E>, Deque<E>, Cloneable, java.io.Serializable
```

- LinkedList是有序的双向链表
- LinkedList在内存中开辟的内存不连续【ArrayList开辟的内存是连续的】
- LinkedList插入和删除元素很快，但是查询很慢【相对于ArrayList】
- 它也可以被当作堆栈、队列或双端队列进行操作。

## Python的链表实现

python中很多数据结构都需要自己去实现，本身没有专门的链表数据类型。



[相关练习代码地址](https://github.com/sweetysweets/Algorithm-Python/tree/master/datastructre/linkedlist)

