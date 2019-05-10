---
title: leetcode-43 Multiply Strings 字符串相乘
date: 2019-03-02 09:45:31
tags:
- leetcode
---

## 题目

Given two non-negative integers `num1` and `num2` represented as strings, return the product of `num1` and `num2`, also represented as a string.

<!--more-->

**Example 1:**

```
Input: num1 = "2", num2 = "3"
Output: "6"
```

**Example 2:**

```
Input: num1 = "123", num2 = "456"
Output: "56088"
```

**Note:**

1. The length of both `num1` and `num2`is < 110.
2. Both `num1` and `num2` contain only digits `0-9`.
3. Both `num1` and `num2` do not contain any leading zero, except the number 0 itself.
4. You **must not use any built-in BigInteger library** or **convert the inputs to integer** directly.

## 解题

这个题目的初衷，是为了让我们实现大数的乘法，比如java中int类型乘法可能会溢出，用字符串乘法就是避免这种情况，但是，Python的int可以无限大的。。所以这个题目用python可以直接转换输出`return str(int(num1) * int(num2))`（python强大的语言hhh）

一开始有点懵，后来看了其他人的思路，发现是模拟竖式进行计算，把每一位的结果保存在数组中，注意进位问题和数组的索引问题

ac代码：

```python
"""
num1 ="" num2=""
num1="" num2="123"
num1="1" num2="345"
num1="0" num2="1234"

"""
class Solution:
    def multiply(self, num1: str, num2: str) -> str:
        m = len(num1)
        n = len(num2)
        if n==0 or m ==0:
            return ""
        if num1=='0' or num2=='0':   ##某一个为0时快速输出结果，不需要再遍历
            return '0'
        result = [0 for i in range(m+n)]
        for i in range(m-1,-1,-1):  ##选择num1的第i位依次和num2的第j位进行乘法
            carry = 0
            for j in range(n-1,-1,-1):   
                tmp_result = (ord(num2[j])-ord('0'))*(ord(num1[i])-ord('0'))
                tmp_result_with_carry =tmp_result+carry+result[i+j+1]  
                ##别忘了既要加进位也要加原来的数值
                carry = tmp_result_with_carry//10
                result[i+j+1] = tmp_result_with_carry - carry*10
                
            if carry!=0:
                result[i] =carry  
  				#首位进位，此时num2的三位都运算完，这个进位需要加在某个位置 
                #相当于num1的 i ，num2的-1 两个位置

        i = 0
        while i < n+m: #此处有更好的写法，while i < len(ret) and ret[i] == '0': i++          
            i += 1
            if result[i] != 0:
                break
            i += 1
        result = result[i:]   ###最后要将前面多余的0删除
    	return "".join([str(test) for test in result])
```

遇到的报错：

```
Line 36: IndentationError: unindent does not match any outer indentation level
指python脚本的缩进有问题
```

## 复杂度分析

时间复杂度：O(mn)，循环的次数是长度相乘

空间复杂度：O(m+n),结果数组需要的空间

## 思考

看到了一种错误解法

```python
class Solution:
    def multiply(self, num1, num2):
        """
        :type num1: str
        :type num2: str
        :rtype: str
        """
        num=''
        num11=float(num1)
        num22=float(num2)
        num=str(int(num11*num22))
        return num
```

出现这种错误的原因主要是因为python中float只能提供约17位的精度，而int可以提供无限精度。

看到网上其他的解法通常都是先把num1和num2翻转，再把结果翻转一次，我觉得没有必要。

还有一种解法，解法差不多，不过它先不处理进位，等到全部算完再处理进位

```python
class Solution:
    # @param num1, a string
    # @param num2, a string
    # @return a string
    def multiply(self, num1, num2):
        num1 = num1[::-1]; num2 = num2[::-1]
        arr = [0 for i in range(len(num1)+len(num2))]
        for i in range(len(num1)):
            for j in range(len(num2)):
                arr[i+j] += int(num1[i]) * int(num2[j])
        ans = []
        for i in range(len(arr)):
            digit = arr[i] % 10
            carry = arr[i] // 10
            if i < len(arr)-1:
                arr[i+1] += carry
            ans.insert(0, str(digit))
        while ans[0] == '0' and len(ans) > 1:
            del ans[0]
        return ''.join(ans)
```

这种解题思路是先将两个字符串翻转过来，然后按位进行相乘，相乘后的数不要着急进位，而是存储在一个数组里面，**全部算完了再处理进位**，然后将数组中的数对10进行求余（%），就是这一位的数，然后除以10，即/10，就是进位的数。注意最后要将相乘后的字符串前面的0去掉。

## 参考

[LeetCode 43 Multiply Strings](http://jianlu.github.io/2016/11/07/leetcode43-Multiply-Strings/)

[[leetcode] Multiply Strings @ Python](https://www.cnblogs.com/zuoyuan/p/3781515.html)