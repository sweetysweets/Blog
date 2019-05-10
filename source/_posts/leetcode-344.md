---
title: leetcode-344 Reverse String 反转字符串
date: 2019-03-02 09:24:27
tags:
- leetcode
---

## 题目

Write a function that reverses a string. The input string is given as an array of characters `char[]`.

Do not allocate extra space for another array, you must do this by **modifying the input array in-place** with O(1) extra memory.

You may assume all the characters consist of [printable ascii characters](https://en.wikipedia.org/wiki/ASCII#Printable_characters).

<!--more-->

**示例 1：**

```
输入：["h","e","l","l","o"]
输出：["o","l","l","e","h"]
```

**示例 2：**

```
输入：["H","a","n","n","a","h"]
输出：["h","a","n","n","a","H"]
```

## 解题 -- 双指针法

这个题目实在是没啥难度，我用了双指针法一遍过，现在已经能记得写代码之前先写测试用例了。

```python
"""
[]
["s"]
["s","a"]
["s","a","b"]
["s","a","b","c"]

"""
class Solution:
    def reverseString(self, s: List[str]) -> None:
        """
        Do not return anything, modify s in-place instead.
        """
        n = len(s)
        if n <= 1:
            return 
        i = 0
        j = n -1
        while i<j:
            tmp = s[i]
            s[i] = s[j]
            s[j] = tmp
            i += 1
            j -= 1   
```

## 复杂度分析

时间复杂度：O(n)，循环n/2次

空间复杂度：O(1),原地操作

## 思考

我看评论中很多人都是用了库里的reverse函数，这里复习一下

#### python

有reverse & reversed 函数，这两个函数都是 对list中元素 **反向排序**，区别在于：

| API            | 改变原list | 返回值 |
| -------------- | ---------- | ------ |
| list.reverse() | **是**     | **无** |
| reversed(list) | **否**     | **有** |

- `reversed()` 的返回值类型 **并不是list**，而是迭代器，因此如果需要，要再套上一个`list()` 。

在这里再记录下sort和sorted：

>简单的说以上四个内置函数都是排序。
>
>对于sort和reverse都是list列表的内置函数，一般不传参数，没有返回值，会改变原列表的值。
>
>而sorted和reversed是python内置函数，需要传参数，参数可以是字符串，列表，字典，元组，不管传的参数是什么sorted返回的都是列表，reversed返回的都是迭代器，原参数的值不会发生改变.

### Java

````java
public static String reverse(String str){
	return new StringBuffer(str).reverse().toString();
}
````

JDK中只有StringBuilder 和 StringBuffer 有reverse函数，而String类却没有，为什么JDK不提供？(String是final所以不能操作，仅从逻辑的情况下，JDK的开发人员就是想让你在字符串可变的情况下，不去使用String，所以就没必要重写了。。而且，从性能上，已经实现了一个性能较好的方法，也没必要再去搞性能差的了)

## 参考阅读

[Java String类为什么不提供reverse函数？](https://www.zhihu.com/question/267985363)