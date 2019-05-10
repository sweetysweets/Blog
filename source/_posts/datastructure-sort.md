---
title: 数据结构之排序
date: 2019-03-06 11:30:27
tags:
- Data Structure
---

## 排序

排序真的是心头大病，年年学年年考年年忘233，老是搞得晕头转向的，希望这次能记长久一点……

<!--more-->

**归并排序**

```python
class Solution: 
    def merge(self,seq, low, mid, height):
        """合并两个已排序好的列表，产生一个新的已排序好的列表"""
        # 通过low,mid height 将[low:mid) [mid:height)提取出来
        left = seq[low:mid]
        right = seq[mid:height]
        print('left:', left, 'right:', right)

        k = 0  # left的下标
        j = 0  # right的下标
        result = []  # 保存本次排序好的内容
        # 将最小的元素依次添加到result数组中
        while k < len(left) and j < len(right):
            if left[k] <= right[j]:
                result.append(left[k])
                k += 1
            else:
                result.append(right[j])
                j += 1
        # 将对比完后剩余的数组内容 添加到已排序好数组中
        result += left[k:]
        result += right[j:]
        # 将原始数组中[low:height)区间 替换为已经排序好的数组
        seq[low:height] = result
        # print("seq:", seq)


    def merge_sort(self,arr):
        i = 1
        while i < len(arr):
            low = 0
            # print('子数组 长度 : ', i)
            while low < len(arr):
                mid = low + i
                height = min(low + 2 * i, len(arr))
                if mid < height:
                    # print('low ', low, 'mid:', mid, 'height:', height)
                    self.merge(arr, low, mid, height)
                low += 2 * i
            i *= 2
```

**快速排序**

```python
# 1.选取一个数字作为基准，可选取末位数字
# 2.将数列第一位开始，依次与此数字比较，如果小于此数，将小数交换到左边，最后达到小于基准数的在左边，大于基准数的在右边，分为两个数组
# 3.分别对两个数组重复上述步骤

# 分治思想 递归调用
def quick_sort(array):
    if len(array) < 2:  # 基线条件（停止递归的条件）
        return array
    else:  # 递归条件
        baseValue = array[0]  # 选择基准值
        # 由所有小于基准值的元素组成的子数组
        less = [m for m in array[1:] if m < baseValue]
        # 包括基准在内的同时和基准相等的元素，在上一个版本的百科当中，并没有考虑相等元素
        equal = [w for w in array if w == baseValue]
        # 由所有大于基准值的元素组成的子数组
        greater = [n for n in array[1:] if n > baseValue]
    return quickSort(less) + equal + quickSort(greater)

# 非递归
def partition(arr, low, high):  #分区操作，返回基准线下标
    privot = arr[low]
    while low < high:
        while low < high and arr[high] >= privot:
            high -= 1
            arr[low] = arr[high]
            while low < high and arr[low] <= privot:
                low += 1
                arr[high] = arr[low]
                # 此时start = end
                arr[low] = privot
                return low

def quick_sort(arr):
    '''''模拟栈操作实现非递归的快速排序'''
	if len(arr) < 2:
		return arr
	stack = []
	stack.append(len(arr) - 1)
	stack.append(0)
	while stack:
		l = stack.pop()
		r = stack.pop()
		index = self.partition(arr, l, r)
        if l < index - 1:
            stack.append(index - 1)
			stack.append(l)
			if r > index + 1:
				stack.append(r)
                stack.append(index + 1)
     return arr
```

**插入排序**

```python
# 当前需要排序的元素(array[i])，跟已经排序好的最后一个元素比较(array[i-1])，如果满足条件继续执行后面的程序，否则循环到下一个要排序的元素。
# 缓存当前要排序的元素的值，以便找到正确的位置进行插入。
# 排序的元素跟已经排序号的元素比较，比它大的向后移动(升序)。
# 要排序的元素，插入到正确的位置。
def insert_sort(nums):
    for i in range(1,len(nums)-1):
        tmp = nums[i]  # current data waiting for sort
        j = i-1
        while j>=0 and nums[j] >tmp:
            nums[j+1] =nums[j] # 把已经排序好的元素后移一位，留下需要插入的位置
            j -= 1
        nums[j] = tmp         
```

**冒泡排序**

```python
# 冒泡排序的思想: 每次比较两个相邻的元素, 如果他们的顺序错误就把他们交换位置
def bubble_sort(nums):
    for i in range(len(nums)-1):
        for j in range(len(nums)-1-i):
        	if nums[j] > nums[j + 1]:
                nums[j], nums[j + 1] = nums[j + 1], nums[j]
```

**选择排序**

```python
# 选择排序注意点(假设第一层循环变量为：i;第二层循环变量为：j)：
# [0,i-1]是已经排序好的元素。
# 定义一个变量，用来记录本次循环下找到的最小元素的下标。
# 第二层循环是从[i,length -1]中找到最小元素的下标，用来与i元素交换。
def select_sort(nums):
    for i in range(len(nums)-1):
        for j in range(i+1,len(nums)-1):
            if nums[i]>nums[j]
            	nums[i],nums[j] = nums[j],nums[i]
```

**堆排序** 参见https://www.jianshu.com/p/d174f1862601

```python
# 
from collections import deque
def swap_param(L, i, j):
    L[i], L[j] = L[j], L[i]
    return L
def heap_adjust(L, start, end):
    temp = L[start]

    i = start
    j = 2 * i

    while j <= end:
        if (j < end) and (L[j] < L[j + 1]):
            j += 1
        if temp < L[j]:
            L[i] = L[j]
            i = j
            j = 2 * i
        else:
            break
    L[i] = temp
def heap_sort(L):
    L_length = len(L) - 1

    first_sort_count = L_length / 2
    for i in range(first_sort_count):
        heap_adjust(L, first_sort_count - i, L_length)

    for i in range(L_length - 1):
        L = swap_param(L, 1, L_length - i)
        heap_adjust(L, 1, L_length - i - 1)

    return [L[i] for i in range(1, len(L))]
def main():
    L = deque([50, 16, 30, 10, 60,  90,  2, 80, 70])
    L.appendleft(0)
    print heap_sort(L)
if __name__ == '__main__':
    main()
```

**计数排序**

```python
class Solution:
    # 时间复杂度n^2 空间复杂度1
    def count_sort(self,nums):
        n = len(nums)
        b = [None] * n
        for i in range(n):
            p = 0
            q = 0
            for j in range(n):
                if nums[j] < nums[i]:
                    p += 1
                elif nums[j] == nums[i]:
                    q += 1
            for k in range(p, p + q):  
            ##说明这几个位置的值都相同，也可以只判断小的,,不可以，会有none
                b[k] = nums[i]
        return b
```

## leetcode 返回滑动窗口最大值(队列)

```python
class Solution:
    def maxSlidingWindow(self, nums: List[int], k: int) -> List[int]:
        if not nums:return []
        res,window = [],[]
        for i,x in enumerate(nums):
            if i >= k and window[0] <= i - k:
                window.pop(0)
            while window and nums[window[-1]] <= x:
                window.pop()
            window.append(i)
            if i >= k - 1:
                res.append(nums[window[0]])
        return res
```

## O(n) 时间复杂度内找到一组数据的第 K 大元素

TopK问题,即寻找最大的K个数,这个问题非常常见,比如从1千万搜索记录中找出最热门的10个关键词.

方法一:先排序,然后截取前k个数.

时间复杂度：O(n*logn)+O(k)=O(n*logn)。

方法二:最小堆.

维护容量为k的最小堆.根据最小堆性质,堆顶一定是最小的,如果小于堆顶,则直接pass,如果大于堆顶,则替换掉堆顶,并heapify整理堆,其中heapify的时间复杂度是logk.

时间复杂度:O(k+(n-k)*logk)=O(n*logk)

方法三:
quick select算法.其实就类似于快排.不同地方在于quick select每趟只需要往一个方向走.

时间复杂度:O(n).

```python
def qselect(A,k):
    if len(A)<k:return A
    pivot = A[-1]
    right = [pivot] + [x for x in A[:-1] if x>=pivot]
    rlen = len(right)
    if rlen==k:
        return right
    if rlen>k:
        return qselect(right, k)
    else:
        left = [x for x in A[:-1] if x<pivot]
        return qselect(left, k-rlen) + right
 
for i in range(1, 10):
    print qselect([11,8,4,1,5,2,7,9], i)
```

## 海量数据之topk



## 查找

**二分查找 实现一个有序数组的二分查找算法**  非递归

```python
def binary_chop(alist, data):
    n = len(alist)
    first = 0
    last = n - 1
    while first <= last:
        mid = (last + first) // 2
        if alist[mid] > data:
            last = mid - 1
        elif alist[mid] < data:
            first = mid + 1
        else:
            return True
    return False
```

**实现模糊二分查找算法（比如大于等于给定值的第一个元素）**

```python
def fuzzyHalfSearch(nums,num):
    if not nums:
        return 
    if num>nums[-1]:
        return
    if num <= nums[0]:
        return 0
    elif num == nums[-1]:
        return len(nums)-1
    
    low = 0
    high = len(nums)-2
    while low <= high:
        medim = (high+low)/2
        if num>nums[medim] and num<=nums[medim+1]:
            return medim+1
        elif num > nums[medim+1]:
            low = medim+1
        elif num <= nums[medim]:
            high = medim
    return
```

## Leetcode Sqrt(x) （x 的平方根）
英文版：[Loading...](https://leetcode.com/problems/sqrtx/)
中文版：[Loading...](https://leetcode-cn.com/problems/sqrtx/)

```python
# https://en.wikipedia.org/wiki/Integer_square_root#Using_only_integer_division
class Solution:
    def mySqrt(self, x):
        """
        :type x: int
        :rtype: int
        """
        if x <= 1:
            return x
        r = x
        while r > x / r:
            r = (r + x / r) // 2
        return int(r)
```

