---
title: leetcode-20 Valid Parentheses 有效的括号
date: 2019-02-27 09:14:52
tags: 
- leetcode
---

Given a string containing just the characters `'('`, `')'`, `'{'`, `'}'`, `'['` and `']'`, determine if the input string is valid.

An input string is valid if:

1. Open brackets must be closed by the same type of brackets.
2. Open brackets must be closed in the correct order.

Note that an empty string is also considered valid.

<!--more-->

**Example 1:**

```
Input: "()"
Output: true
```

**Example 2:**

```
Input: "()[]{}"
Output: true
```

**Example 3:**

```
Input: "(]"
Output: false
```

**Example 4:**

```
Input: "([)]"
Output: false
```

**Example 5:**

```
Input: "{[]}"
Output: true
```

这个题目其实就是一个栈的应用，和数据结构中学习过后缀表达式的判定一样

题目比较简单，但写代码还是写的太粗糙了，第一遍写的代码：

```python
class Solution:
    def isValid(self, s: str) -> bool:
        stack =  []
        for i in range(len(s)):
            tmp = s[i]
            if tmp == '(' or tmp =='[' or tmp =='{':
                stack.append(tmp)
            else:
                if stack[-1] == tmp:
                    stack.pop()
                else:
                    return False
        return True
```

过了一遍流程发现了两处错误

1. 判断stack栈顶元素错了，应该是 `当前右括号` 对应的 左边括号
2. 最终return 应该是判断栈的长度是不是为0，为0才True

修改后

```python
class Solution:
    def isValid(self, s: str) -> bool:
        stack =  []   
        for i in range(len(s)):
            tmp = s[i]
            if tmp == '(' or tmp =='[' or tmp =='{':
                stack.append(tmp)
            else:
                if tmp == ')' and stack[-1] == '(':
                    stack.pop()
                elif tmp == '}' and stack[-1] == '{':
                    stack.pop()
                elif tmp == ']' and stack[-1] == '[':
                    stack.pop()
                else:
                    return False
        return len(stack) == 0
```

运行用例‘{’没过，发现错误 直接判断 stack[-1] == '('会溢出，需要提前判断是不是stack已经空了

ac代码：

```python
class Solution:
    def isValid(self, s: str) -> bool:
        stack =  []   
        for i in range(len(s)):
            tmp = s[i]
            if tmp == '(' or tmp =='[' or tmp =='{':
                stack.append(tmp)
            else:
                if len(stack) == 0:
                    return False  
                if tmp == ')' and stack[-1] == '(':
                    stack.pop()
                elif tmp == '}' and stack[-1] == '{':
                    stack.pop()
                elif tmp == ']' and stack[-1] == '[':
                    stack.pop()
                else:
                    return False
        return len(stack) == 0
```

执行用时: 84 ms, 在Valid Parentheses的Python3提交中击败了4.89% 的用户

内存消耗: 13.3 MB, 在Valid Parentheses的Python3提交中击败了0.90% 的用户

进行修改，用字典存对应的括号关系,同时也可以简化代码，修改后：

```python
my_dict = {')':'(','}':'{',']':'['}
class Solution:
    def isValid(self, s: str) -> bool:      
        stack =  []   
        for i in range(len(s)):
            tmp = s[i]
            if tmp in my_dict.values():
                stack.append(tmp)
            else:
                if len(stack) == 0:
                    return False  
                if stack[-1] == my_dict[tmp]:
                    stack.pop()
                else:
                    return False
        return len(stack) == 0   
```

执行用时: 56 ms, 在Valid Parentheses的Python3提交中击败了25.75% 的用户

内存消耗: 13.1 MB, 在Valid Parentheses的Python3提交中击败了0.90% 的用户

官方解答：

```python
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

**复杂度分析**

- 时间复杂度：O(n)*O*(*n*)，因为我们一次只遍历给定的字符串中的一个字符并在栈上进行 O(1)*O*(1) 的推入和弹出操作。
- 空间复杂度：O(n)*O*(*n*)，当我们将所有的开括号都推到栈上时以及在最糟糕的情况下，我们最终要把所有括号推到栈上。例如 `((((((((((`。

### 思考

拿到题目之后有点太着急写代码，没有仔细分析，应该

- 读懂题目
- 自己写测试用例，考虑边缘情况
- 考虑数据结构和方法
- 编写代码
- 调试

另外，代码写的不如其他人的简洁，要`尽量把相同的或可以合并的场景合并在一起`





