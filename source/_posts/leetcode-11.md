---
title: leetcode-11 Container With Most Water 盛最多水的容器
date: 2019-02-28 10:17:39
tags:
- leetcode
---

## 题目

Given *n* non-negative integers *a1*, *a2*, ..., *an* , where each represents a point at coordinate (*i*, *ai*). *n* vertical lines are drawn such that the two endpoints of line *i* is at (*i*, *ai*) and (*i*, 0). Find two lines, which together with x-axis forms a container, such that the container contains the most water.

**Note:** You may not slant the container and *n*is at least 2.

<!--more-->

![img](https://s3-lc-upload.s3.amazonaws.com/uploads/2018/07/17/question_11.jpg)

The above vertical lines are represented by array [1,8,6,2,5,4,8,3,7]. In this case, the max area of water (blue section) the container can contain is 49.

 

**Example:**

```
Input: [1,8,6,2,5,4,8,3,7]
Output: 49
```

## 解题

#### 暴力法

思路很简单，就是循环两遍，找出所有可能的组合中最大的那一个。python代码超时了，java可以通过

```python
   class Solution:
    def maxArea(self, height: List[int]) -> int:
        maxArea = 0
        for i in range(len(height)):
            for j in range(i,len(height)):
                tmpArea = (j-i)*min(height[i],height[j])
                if maxArea < tmpArea:
                    maxArea = tmpArea
        return maxArea
```

#### 双指针法

首先我们来想想面积是如何计算的：对于给定的长度，area = min(height1，height2) * length

最初我们考虑由最外围两条线段构成的区域。现在，为了使面积最大化，我们需要考虑更长的两条线段之间的区域。如果我们试图将指向较长线段的指针向内侧移动，矩形区域的面积将受限于较短的线段而不会获得任何增加。但是，在同样的条件下，移动指向较短线段的指针尽管造成了矩形宽度的减小，但却可能会有助于面积的增大。因为移动较短线段的指针会得到一条相对较长的线段，这可以克服由宽度减小而引起的面积减小。

可能很多同学会跟我一样疑惑双向指针为什么不会错过最优解，以下是我的理解.首先按照解的逻辑是，l,r两个指针，l指向的H[l]比H[r]要小，然后让l指针往左移动， 那么就分析漏掉解。 漏掉就是那些l指针不动，丢失的就是r在这个范围(l<...<r)的解. 那么分析一下这个范围的解，如果我们保持H[l]较小的值不动，去移动H[r]较大的值会出现什么情况？

H[r-1] > H[r],这种情况，毋庸置疑，作为底的(r - l）减少了，由于新的H[r-1]比H[r]更大，那么高必然还是H[l]这个较小的值，所以这个解一定比之前的那个解更小的水容量。
H[r-1] < H[r] 并且H[r-1]>H[l]情况，这种情况其实跟上面一种是一样的，因为相当于底减少了1，高不变，水容量肯定更小。
H[r-1] < H[l]，这种情况，底减少了1，高从本来的H[l]变成了更小的H[r-1]，那么水容量肯定变得更小。
以上三种移动更大的H[r]的情况所得到的解都是得到更小的水容量，然后我们在分析移动更小的l指针会得到什么情况.

最坏的情况，新的H[l+1]比H[l]更小，那么肯定水容量变小.
H[l]<H[l+1]<H[r]，这个情况就有可能出现更优解，因为底虽然减少了，但是最为高的H[l+1]增大的值之后计算出来的水容积是可能比之前那个解大的.
H[l+1]>H[r],这个情况其实跟上面类似，只不过高变成r指针的H[r],一样可以理解为底变小，高变大。
综上，如果移动更小指针的高度，可能得到比原始解更优的解，如果移动更大指针高度，就必然得到比原始解更差的解。

```python
class Solution:
    def maxArea(self, height: List[int]) -> int:
        j = len(height) - 1
        i = 0
        maxArea = 0
        while i < j and i < len(height) - 1 and j > 0:
            height1 = height[i]
            height2 = height[j]
            tmpArea = (j - i) * (height1 if height1 < height2 else height2)
            if maxArea < tmpArea:
                maxArea = tmpArea
            if height1 > height2:
                j -= 1
            else:
                i += 1
        return maxArea
```

在评论中看到的更简洁好看的代码：

```python
class Solution:
   def maxArea(self, height):
       len_h = len(height)
       i = 0
       k = -1
       area = 0
       while len_h+k-i>=1:
           area = max(area,(min(height[i],height[k])*(len_h+k-i))
           if height[i] <= height[k]:
               i += 1
           else:
               k -= 1
       return area
```

## 动态规划学习

- 什么是动态规划？ 
  这里参考百度百科，动态规划是求解决策过程最优化的数学方法。把多阶段过程转化为一系列单阶段问题，利用各阶段之间的关系，逐个求解，创立了解决这类过程优化问题的新方法——动态规划。

  动态规划算法的核心是——记住已经解决过的子问题的解。用看到的一个小例子来描述：

> A * "1+1+1+1+1+1+1+1 =？" *
>
> A : "上面等式的值是多少"
> B : *计算* "8!"
>
> A *在上面等式的左边写上 "1+" *
> A : "此时等式的值为多少"
> B : *quickly* "9!"
> A : "你怎么这么快就知道答案了"
> A : "只要在8的基础上加1就行了"
>
> A : "所以你不用重新计算因为你记住了第一个等式的值为8!动态规划算法也可以说是 '记住求过的解来节省时间'"

动态规划一般可分为线性动规，区域动规，树形动规，背包动规四类。(后面要分类详细学习)

- 什么时候要用动态规划？ 
  如果要求一个问题的最优解（通常是最大值或者最小值），而且该问题能够分解成若干个子问题，并且小问题之间也存在重叠的子问题，则考虑采用动态规划。

- 怎么使用动态规划？ 
  我把下面称为动态规划五部曲： 

1. 判题题意是否为找出一个问题的最优解 
2. 从上往下分析问题，大问题可以分解为子问题，子问题中还有更小的子问题 
3. 从下往上分析问题 ，找出这些问题之间的关联（状态转移方程） 
4. 讨论底层的边界问题 
5. 解决问题（通常使用数组进行迭代求出最优解）

看起来有点像递归呢，一般递归问题都可以转化为动态规划。

## 复杂度分析

时间复杂度：O(n)，一次扫描。

空间复杂度：O(1)，使用恒定的空间。

## 思考

在写这篇文章的时候，已经把代码写完了，评论和官方解答也看了些。看到这个题目的时候就意识到暴力不可取，因为之前的作业题中有相似的，所以也很快就想到了这种解法。趁着这个机会，复习一下动态规划。

附上之前的类似题目：

```python
"""
子矩阵问题
Description

给定一个矩形区域，每一个位置上都是1或是0，求该矩阵中每一个位置上都是1的最大子矩阵区域中1的个数


Input

输入的每一行是用空格隔开的0或1


Output

输出一个值。


Sample Input 1

1 0 1 1
1 1 1 1
1 1 1 0
Sample Output 1
6

"""


import sys


class Solution(object):
    def maximalRectangle(self, matrix):
        """
        :type matrix: List[List[str]]
        :rtype: int
        """
        if not matrix or not matrix[0]: return 0
        M, N = len(matrix), len(matrix[0])
        height = [0] * N
        res = 0
        for row in matrix:
            for i in range(N):
                if row[i] == '0':
                    height[i] = 0
                else:
                    height[i] += 1
            res = max(res, self.maxRectangleArea(height))
        return res

    def maxRectangleArea(self, height):
        if not height: return 0
        res = 0
        stack = list()
        height.append(0)
        for i in range(len(height)):
            cur = height[i]
            while stack and cur < height[stack[-1]]:
                w = height[stack.pop()]
                h = i if not stack else i - stack[-1] - 1
                res = max(res, w * h)
            stack.append(i)
        return res


if __name__ == '__main__':

    S = Solution()
    matrix = []
    for line in sys.stdin:
        if not line:
            break
        if line == '\n':
            break
        arr = line.replace(' ', '').replace('\n', '')
        matrix.append(arr)
        print(matrix)
    max_area = S.maximalRectangle(matrix)
    print(max_area)
```

## 参考博客

[动态规划从入门到精通（一）-入门篇](https://blog.csdn.net/weixin_38278878/article/details/80037455)

[算法-动态规划 Dynamic Programming](https://blog.csdn.net/u013309870/article/details/75193592)

[常见的动态规划问题分析与求解](https://www.cnblogs.com/wuyuegb2312/p/3281264.html)

