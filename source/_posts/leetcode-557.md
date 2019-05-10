---
title: leetcode-557 Reverse Words in a String III 
date: 2019-03-03 11:35:21
tags:
- leetcode
---

## 题目

Given a string, you need to reverse the order of characters in each word within a sentence while still preserving whitespace and initial word order.

<!--more-->

**Example 1:**

```
Input: "Let's take LeetCode contest"
Output: "s'teL ekat edoCteeL tsetnoc"
```

**Note:** In the string, each word is separated by single space and there will not be any extra space in the string.

## 解题

这道题和反转字符串有点类似，不过要多加一层遍历即可，参考[前几天做的题目](https://sweets.ml/2019/03/02/leetcode-344/),其实可以用python一行搞定：

```python
"""
""
"tack"
"tack tack"
"""
class Solution:
    def reverseWords(self, s: str) -> str:
        return " ".join(x[::-1] for x in s.split())
```

强行偷懒=。= 老老实实实现了一遍：

```python
class Solution:
    def reverseWord(self, s: str):
        n = len(s)
        i,j = 0,n-1
        while i<j:
            tmp = s[i]
            s[i] = s[j]
            s[j] = tmp
            i += 1
            j -= 1       
    def reverseWords(self, s: str) -> str:
        words = s.split()
        for word in words:
            self.reverseWord(word)
        return " ".join(words)
```

然后就遇到一个报错。。。

```python
TypeError: ‘str’ object does not support item assignment 
```

原因：在python中，**字符串是不可变对象**，不能通过下标的方式直接赋值修改。同样的不可变对象还有：数字、字符串和元组。想这样修改，只能传入一个列表.

修改代码：

```python
class Solution:
    def reverseWord(self, s: str) -> str:
        n = len(s)
        i,j = 0,n-1
        result = ""
        for i in range(n-1,-1,-1):
            result +=s[i]
        return result
            
    def reverseWords(self, s: str) -> str:
        words = s.split()
        for word in words:
            word = self.reverseWord(word)   
            #words没有发生改变。说明word是系统新创建的变量，
            #并且copy了words中元素的值，但是指针并没有指向数组中的元素
        return " ".join(words)
```

很奇怪。。输出的结果依然是原数组，没有发生任何改变，说明我的 word = self.reverseWord(word)这句赋值没有用。。参考博客[Python遍历数组，修改](https://www.jianshu.com/p/bc07934efeb5)明白，修改后ac：

```python
def reverseWords(self, s: str) -> str:
        words = s.split()
        for i in range(len(words)):
            words[i] = self.reverseWord(words[i])
        return " ".join(words)
```

```python
###可以用map方法： 
    def addBrackets(x) :
        return '<' + x + '>'
    newArr = map(addBrackets, arr)
    print(list(newArr))
```

## 复杂度分析

时间复杂度：O(n*max_len(word))

空间复杂度：O(max_len(word)) 

## python列表切片 ---冒号的用法

[m : ] 代表列表中的第m+1项到最后一项    [ : n] 代表列表中的第一项到第n项

list[start​ ​: end : ​step]
start:起始位置 end:结束位置 step:步长

```python
>>> s = 'abcdefgh'
>>> s[::-1]   # 可以视为翻转操作
'hgfedcba'
>>> s[::2]   # 隔一个取一个元素的操作
'aceg' 
```

## python中lamda的用法 

lambda表达式，通常是在**需要一个函数，但是又不想费神去命名一个函数**的场合下使用，也就是指**匿名函数**。

```python3
lambda x, y : x+y    ##结果为x，y的和
```

怎么使用lambda表达式呢？ 

**1、应用在函数式编程中**

Python提供了很多函数式编程的特性，如：map、reduce、filter、sorted等这些函数都支持函数作为参数，lambda函数就可以应用在函数式编程中。如下：

```python
# 需求：将列表中的元素按照绝对值大小进行升序排列
list1 = [3,5,-4,-1,0,-2,-6]
sorted(list1, key=lambda x: abs(x))
pairs = [(1, 'one'), (2, 'two'), (3, 'three'), (4, 'four')]
pairs.sort(key=lambda pair: pair[1])
```

等价于

```python
list1 = [3,5,-4,-1,0,-2,-6]
def get_abs(x):
    return abs(x)
sorted(list1,key=get_abs)
```

只不过这种方式的代码看起来不够Pythonic

**2、list的动态多列排序**

  主要利用的是lambda表达式的eval()函数，eval函数能够把字符串编译成python代码并运行，从而达到动态根据多个列或属性排序的目的。
  主要代码如下：

```python
def mysort(sortindex):
    keyset=""
    for i in range(sortindex):
        keyset+= "x["+str(i)+"],"   ###这里属于不知道一共有多少个属性的情况
    keyset = keyset.rstrip(",")
    result.sort(key=lambda x:eval(keyset),reverse=True)
```

**3、应用在闭包中**

```python
def get_y(a,b):
     return lambda x:ax+b
y1 = get_y(1,1)
y1(1) # 结果为2
```

当然，也可以用常规函数实现闭包，如下：

```python
def get_y(a,b):
    def func(x):
        return ax+b
    return func
y1 = get_y(1,1)
y1(1) # 结果为2
```

只不过这种方式显得有点啰嗦。

那么是不是任何情况下lambda函数都要比常规函数更清晰明了呢？

肯定不是。

Python之禅中有这么一句话：Explicit is better than implicit（明了胜于晦涩），就是说那种方式更清晰就用哪一种方式，不要盲目的都使用lambda表达式。

## 参考

[python -- lambda表达式](https://www.cnblogs.com/hf8051/p/8085424.html)

https://blog.csdn.net/zjuxsl/article/details/79437563

[Python3 list 排序函数详解](https://blog.csdn.net/lianshaohua/article/details/80483357)

[python多条件排序](https://blog.csdn.net/jacke121/article/details/78061738)