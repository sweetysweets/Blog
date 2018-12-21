---
title: python读写方式
date: 2018-12-11 09:09:56
tags:
- python
---

python有多种从控制台输入方式： 
1.使用input()、raw_input()函数 
这两个函数在python的内置库里。 
input()函数返回一个数值（整型或浮点型） 
raw_input()函数返回字符串 
用例：

```
value = input('input a int:')
print value
hello = raw_input('input a string:')
print hello
```

- 在Python3中对input和raw_input函数进行了整合，仅保留了input函数（认为raw_input函数是冗余的）。同时改变了input的用法——将所有的输入按照字符串进行处理，并返回一个字符串。
- 所以在Python3中想要获得其他类型的输入，要做强制类型转换

2.使用read()、readline()函数 
这两个函数来自sys.stdin库，使用前需导入 
用例

```
print 'input :'
value = stdin.readline()
print value
```

注意：read()以及readline()函数参数并不是提示字符串。

