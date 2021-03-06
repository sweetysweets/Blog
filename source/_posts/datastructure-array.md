---
title: 数据结构之数组
date: 2019-02-28 17:14:16
tags:
- Data Structure
---

## 数组

数组是线性数据结构中的连续存储结构，它的特点是：

- 元素类型是固定的
- 长度是固定的
- 通过角标查询、查询快
- 增删慢

<!--more-->

## Java 数组原理

首先我们要知道jvm的内存划分包括：

寄存器	给CPU使用，和我们开发无关。
本地方法栈	JVM在使用操作系统功能的时候使用，和我们开发无关。
方法区	存储可以运行的class文件。
堆内存	存储对象或者数组，new来创建的，都存储在堆内存。
方法栈	方法运行时使用的内存，比如main方法运行，进入方法栈中执行。

其中堆栈属于重点要用的

new出的对象都存在堆上，而方法中的变量arr保存的是数组的地址。 **输出arr[0]，就会输出arr保存的内存地址中数组中0索引上的元素.**

## Python 数组原理

##### python数组实际上相当于java中的arraylist，是支持动态扩容的数组。

ArrayList 通过数组实现，一旦我们实例化 ArrayList 无参数构造函数默认为数组初始化长度为 10,如果增加的元素个数超过了 10 个，那么 ArrayList 底层会新生成一个数组，长度为原数组的 1.5 倍+1，然后将原数组的内容复制到新数组当中，并且后续增加的内容都会放到新数组当中。当新数组无法容纳增加的元素时，重复该过程。

## 数组中的算法题

数组中的算法题无外乎排序，选择这方面的，一般使用简单的排序、双指针法等等可以解决大部分的问题。

数组也经常用在算法题中，回溯法中存储访问标记，动态规划中存储矩阵等等。。

[相关练习代码地址](https://github.com/sweetysweets/Algorithm-Python/tree/master/datastructre/array)

