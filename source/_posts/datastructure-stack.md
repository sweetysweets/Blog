---
title: 数据结构之栈
date: 2019-03-02 14:45:31
tags:
- Data Structure
---

## 栈

栈是限定仅在表头进行插入和删除操作的线性表。引入到计算机领域里，就是指数据暂时存储的地方，所以才有进栈、出栈的说法。栈的特点是--先进后出

栈可以用来在[函数](https://baike.baidu.com/item/%E5%87%BD%E6%95%B0)调用的时候存储断点，做[递归](https://baike.baidu.com/item/%E9%80%92%E5%BD%92)时要用到栈！

栈分顺序栈和链式栈。

<!--more-->

## 栈的应用

- 逆序输出  

  首先把所有元素依次入栈，然后把所有元素出栈并输出，这样就实现了逆序输出。

- 括号匹配检查

- 表达式求值 & 中缀表达式转后缀表达式

- 递归求解

- 迷宫求解

  求迷宫从入口到出口的所有路径是一个经典的程序设计问题。由于计算机解迷宫时，通常用的是“穷举求解”的方法，即从入口出发，顺某一方向向前探索，若能走通，则继续往前走；否则沿原路退回，换一个方向再继续探索，直至所有可能的通路都探索到为止。为了保证在任何位置都能沿原路退回，显然需要用一个后进先出的结构来保存从入口到当前位置的路径。因此在迷宫求解时应用“栈”也就是自然而然的事情了。

- 数制转换

  十进制数N和其他d进制数的转换是计算机实现计算的基本问题，其解决方法很多，其中一个简单算法基于下列原理：

  > N = (N div d) * d + N mod d(其中：div为整除运算，mod为求余运算)

  上述过程是从低位到高位产生8进制的各个数位，然后从高位到低位进行输出，结果数位的使用具有后出现先使用的特点，因此生成的结果数位可以使用一个栈来存储，然后从栈顶开始依次输出即可得到相应的转换结果。

## java中的Stack实现

```java
public class Stack<E> extends Vector<E> {}
```

底层数组保存数据，一个栈顶指针指向栈顶元素。 
入栈，top++指向该元素， 
出栈，出top指向元素，top–指向下一个元素 

当元素满了之后，数组长度自动扩展为原来的两倍 （vector中的方法）

## python中的实现

可以通过list方便的实现，list自带pop、append功能

```python
list.pop([index=-1])  ##默认删除最后一个元素 返回从列表中移除的元素对象。
```

## 实现浏览器的前进后退功能

我们使用两个栈X和Y，我们把首次浏览的页面依次压入栈X，当点击后退按钮时，再依次从栈X中出栈，并将出栈的数据一次放入Y栈。当点击前进按钮时，我们依次从栈Y中取出数据，放入栈X中。当栈X中没有数据时，说明没有页面可以继续后退浏览了。当Y栈没有数据，那就说明没有页面可以点击前进浏览了。这让我想起了一个问题：[用两个栈实现一个队列](https://www.cnblogs.com/MrListening/p/5697459.html)

```python
class WebViewList(object):
    def __init__(self):
        self._stackA = Stack()
        self._stackB = Stack()

    def forward(self):
        if self._stackB.size() == 0:  ###此处是0，原因是
            return None
        url = self._stackB.pop()
        self._stackA.push(url)
        return self._stackA.peek()

    def back(self):
        if self._stackA.size() == 1:   ###此处是1 ，原因是退到最后一个页面无法再推
            return None
        url = self._stackA.pop()
        self._stackB.push(url)
        return self._stackA.peek()

    def load(self,url):
        self._stackA.push(url)
        return url
```

## Leetcode实战

Valid Parentheses（有效的括号）
英文版：[Loading...](https://leetcode.com/problems/valid-parentheses/)
中文版：[Loading...](https://leetcode-cn.com/problems/valid-parentheses/)

```python
my_dict = {')':'(','}':'{',']':'['}
class Solution:
    def isValid(self, s: str) -> bool:
        stack =  []   
        mapping = {")": "(", "}": "{", "]": "["}
        for char in s:
            if char in mapping:
                top_element = stack.pop() if stack else '#'
                if mapping[char] != top_element:
                    return False
            else:
                stack.append(char)
        return not stack
```

Longest Valid Parentheses（最长有效的括号）
英文版：[Loading...](https://leetcode.com/problems/longest-valid-parentheses/)
中文版：[Loading...](https://leetcode-cn.com/problems/longest-valid-parentheses/)

动态规划，一次遍历，每次循环记录以当前括号结束的（包含当前括号）的最长有效长度（画图理解有奇效），属于空间(4MB)换时间。

加入辅助数组，左括号直接进栈，辅助数组对应编号置0， 右括号进看前面是否有配对，有左括号配对将前面的左括号出栈，将当前右和左括号在辅助数组中对应位置置1，最后看连续的1的最大数返回。 

```python
class Solution:
    def longestValidParentheses(self, s):
        st, b = [], [0]*len(s)
        for i, val in enumerate(s):
            if val == '(':
                st.append(i)
            elif st:
                b[st.pop()], b[i] = 1, 1
        c, mc = 0, 0
        for i in b:
            if i:
                c += 1
            else:
                mc = max(c, mc)
                c = 0
        return max(c, mc)
```

Evaluate Reverse Polish Notatio（逆波兰表达式求值）
英文版：[Loading...](https://leetcode.com/problems/evaluate-reverse-polish-notation/)
中文版：[Loading...](https://leetcode-cn.com/problems/evaluate-reverse-polish-notation/)

```python
"""
[]
[2]
[2,+,4]
"""
class Solution:
    def evalRPN(self, tokens: List[str]) -> int:	
    	stack = []
        cal = {"+" : lambda x,y :x+y, "-" : lambda x,y : x-y, "*" : lambda x,y : x*y, "/" : lambda x,y : int(x/y)}     
        for i in tokens:  
            if i not in ["+","-","*","/"]:
                stack.append(int(i))
            else:
                b = stack.pop()
                a = stack.pop()
                stack.append(cal[i](a,b))
        return stack[0]
```

## 挖坑-python 中 lamda表达式的学习 

待续。。

## 挖坑-java 中的内存堆栈

待续。。

## 参考

https://blog.csdn.net/dta0502/article/details/80790721

[使用栈结构简易实现浏览器的后退与前进功能(以Android为例)](https://blog.csdn.net/mgsky1/article/details/71036856)

[栈的应用](https://blog.csdn.net/gavin_john/article/details/71374487 )