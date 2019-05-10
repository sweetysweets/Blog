---
title: 数据结构之字符串
date: 2019-03-08 14:16:43
tags:
- Data Structure
---

## 字符串



## 实现一个字符集，只包含 a～z 这 26 个英文字母的 Trie 树
Trie字典树主要用于存储字符串，**Trie** 的每个 **Node** 保存一个字符。用链表来描述的话，就是一个字符串就是一个链表。每个Node都保存了它的所有子节点。是一种哈希树的变种。典型应用是用于统计和排序大量的字符串（但不仅限于字符串），所以经常被搜索引擎系统用于文本词频统计。它的优点是：最大限度地减少无谓的字符串比较，查询效率比哈希表高。Trie树有一些特性：

<!--more-->

1）根节点不包含字符，除根节点外每一个节点都只包含一个字符。
2）从根节点到某一节点，路径上经过的字符连接起来，为该节点对应的字符串。
3）每个节点的所有子节点包含的字符都不相同。
4）如果字符的种数为n，则每个结点的出度为n，这也是空间换时间的体现，浪费了很多的空间。
5）插入查找的复杂度为O(n)，n为字符串长度。
基本思想（以字母树为例）：
1、插入过程
对于一个单词，从根开始，沿着单词的各个字母所对应的树中的节点分支向下走，直到单词遍历完，将最后的节点标记为红色，表示该单词已插入Trie树。
2、查询过程
同样的，从根开始按照单词的字母顺序向下遍历trie树，一旦发现某个节点标记不存在或者单词遍历完成而最后的节点未标记为红色，则表示该单词不存在，若最后的节点标记为红色，表示该单词存在。

```python
class Trie:
    def __init__(self):
        self.root = TrieNode()

    def insert(self, word):
        node = self.root
        for chars in word:
            child = node.data.get(chars)
            if not child :
                node.data[chars] = TrieNode()
            node = node.data[chars]
        node.is_word = True

    def search(self, word):
        node = self.root
        for chars in word:
            node = node.data.get(chars)
            if not node:
                return False
        return node.is_word    # 判断单词是否是完整的存在在trie树中

    def startsWith(self, prefix):
        node = self.root
        for chars in prefix:
            node = node.data.get(chars)
            if not node:
                return False
        return True

    def get_start(self, prefix):
        word_list = []
        if pre_node.is_word:
            word_list.append(pre)
            for x in pre_node.data.keys():
                word_list.extend(get_key(pre + str(x), pre_node.data.get(x)))
       	return word_list
	def get_key(pre, pre_node):
		words = []
        if not self.startsWith(prefix):
            return  words
        if self.search(prefix):
            words.append(prefix)
            return words
        node = self.root
        for chars in prefix:
            node = node.data.get(chars)
        return get_key(prefix, node)
```

关于trie一个好玩的问题https://www.zhihu.com/question/27168319

## 实现字符串匹配算法

**字符串回溯匹配(朴素算法)**

算法基本思想： 
将搜索词整个后移一位，再从头逐个比较。这样做虽然可行，但是效率很差，因为你要把”搜索位置”移到已经比较过的位置，重比一遍。

最坏情况是每趟比较都在最后出现不等，最多比较n－m＋1 趟，总比较次数为m*(n－m＋1)，所以算法时间复杂性为O(m*n)

```python
def nmatching(t, p):
    i, j = 0, 0
    n, m = len(t), len(p)
    while i < n and j < m:
        if t[i] == p[j]:
            i, j = i+1, j+1
        else:
            i, j = i-j+1, 0        #i-j+1是关键，遇字符不等时将模式串t右移一个字符
    if j == m:                     #找到一个匹配，返回索引值
        return i-j
    return -1                       #未找到，返回-1
```

**KMP算法** 
当字符不匹配时，你其实知道前面的字符是什么。KMP算法的想法是设法利用这个已知信息，不要把字符串t中的”搜索位置”移回已经比较过的位置，继续把搜索位置向后移。匹配中只做不得不做的字符比较，字符串t搜索位置i不回溯。可以针对搜索词，算出一张《部分匹配表》（Partial Match Table）

KMP算法流程： 
假设当前字符串t匹配到 i 位置，模式串P匹配到 j 位置.

if j = -1，或者当前字符匹配成功（即t[i] == p[j]），则i，j= i+1, j+1，继续匹配下一个字符；
if j != -1，且当前字符匹配失败（即t[i] != p[j]），则 i 不变，j = next[j]。此举意味着失配时，模式串P相对于字符串S向右移动了j - next [j] 位。

上面两个if判断在字符串没有搜索结束前，两个条件必定满足一个，两个条件互为否命题。
当匹配失败时，模式串向右移动的位数为：失配字符所在位置 - 失配字符对应的next 值，即移动的实际位数为：j - next[j]，且此值大于等于1。

pnext 数组各值含义
pnext 数组各值含义：代表当前字符之前的字符串中，有多大长度的相同前缀后缀。例如next [j] = k，代表j 之前的字符串中有最大长度为k 的相同前缀后缀。（p0p1……pk-1 = pj-kpj-k+1……pj-1）。即：模式串向右移动的位数为：已匹配字符数 - 失配字符的上一位字符所对应的最大长度值
某个字符失配时，该字符对应的next 值会告诉你下一步匹配中，模式串应该跳到哪个位置（跳到next [j] 的位置）。
如果next [j] 等于0或-1，则跳到模式串的开头字符，若next [j] = k 且 k > 0，代表下次匹配跳到j 之前的某个字符，而不是跳到开头，且具体跳过了k 个字符。

```python
def matchingKMP(t,p,pnext):     #需要传入一个部分匹配表pnext
    i, j = 0, 0
    n, m = len(t), len(p)
    while i < n and j < m:
        if j == -1 or t[i] == p[j]: #如果j = -1，或者当前字符匹配成功（即S[i] == P[j]），都令i+1，j+1
            i, j = i+1, j+1
        else:                       #如果j != -1，且当前字符匹配失败（即S[i] != P[j]），则令 i 不变，j = next[j] # next[j]即为j所对应的next值
            j = pnext[j]
        if j == m:                  # 找到匹配，返回索引值
            return i - j

    return -1                       # 无法匹配，返回-1

def genPNext0(p):
    j, k, m = 0, -1, len(p)
    pnext = [-1]*m
    while j < m-1:                  #生成pnext
        while k >= 0 and p[j] != p[k]:
            k = pnext[k]            
        j, k = j+1, k+1
        pnext[j] = k                #考虑前面

    return pnext

#生成pnext表，作用：当模式串中的某个字符跟文本串中的某个字符匹配失配时，模式串下一步应该跳到哪个位置
def genPNext(p):                    
    j, k, m = 0, -1, len(p)
    pnext = [-1]*m
    while j < m-1:                  #生成pnext
        while k >= 0 and p[j] != p[k]:
            k = pnext[k]            #设k = pnext[k]
        j, k = j+1, k+1
        if p[j] == p[k]:            #递推过程
            pnext[j] = pnext[k]
        else:
            pnext[j] = k            
            #next [j] = k 且 k > 0，表示下次匹配跳到j 之前的某个字符，而不是跳到开头，且具体跳过了k 个字符
    return pnext

if __name__ == "__main__":
    t = 'bbc abcdab abcdabdabde'
    p = 'abcdabdab'
    print('------------------------------------')
    print(matchingKMP(t,p,genPNext(p)))
```



##  LeetCode 练习题

**Reverse String （反转字符串）**
英文版：[Loading...](https://leetcode.com/problems/reverse-string/)
中文版：[Loading…](https://leetcode-cn.com/problems/reverse-string/)

解题见我的[另一篇博客](https://sweets.ml/2019/03/02/leetcode-344/)

**Reverse Words in a String（翻转字符串里的单词）**
英文版：[Loading...](https://leetcode.com/problems/reverse-words-in-a-string/)
中文版：[Loading...](https://leetcode-cn.com/problems/reverse-words-in-a-string/)

解题见我的[另一篇博客](https://sweets.ml/2019/03/03/leetcode-557/)

**String to Integer (atoi)（字符串转换整数 (atoi)）**
英文版：[Loading...](https://leetcode.com/problems/string-to-integer-atoi/)
中文版：[Loading...](https://leetcode-cn.com/problems/string-to-integer-atoi/)

第一次超出时间限制了，发现是因为while循环中忘记对i进行++操作了

```python
class Solution:
    def myAtoi(self, str):
        """
        :type str: str
        :rtype: int
        """
        res = []
        flag = True          # 标记，默认字符串第一个非空格字符为有效整数或'-'或‘+’
        
        numslist = ['0','1','2','3','4','5','6','7','8','9'] 
        for i in range(len(str)):
            if str[i] ==' ' and flag:  
                # 如果均为空格字符，且无非空字符出现继续
                continue
                
            if str[i] == '+' and flag: 
                # 如果“+”字符第一次出现，则添加到列表中，标记修改为False并继续
                res.append(str[i])
                flag = False
                continue
                
            if str[i] == '-' and flag: 
                # 如果“-”字符第一次出现，则添加到列表中，标记修改为False并继续
                res.append(str[i])
                flag = False
                continue
                
            if str[i] not in numslist: # 除过上述情况，如果字符不为数字，则直接退出迭代
                break
            else:
                res.append(str[i])     # 反之有数字出现,添加到列表中，并修改标记为False
                flag = False
                
        res = ''.join(res)             # 拼接字符串 
        if res == '-' or res=='' or res=='+':
            return 0
        else :
            res = int(res)
        
        if res>2**31-1:               # 特殊情况处理
            return 2**31-1
        if res<-2**31:
            return -2**31
        else:
            return res
```

