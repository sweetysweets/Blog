---
title: leetcode-26 Remove Duplicates from Sorted Array 删除排序数组中的重复项
date: 2019-02-28 09:52:04
tags:
- leetcode
---

### 题目

Given a sorted array *nums*, remove the duplicates [**in-place**](https://en.wikipedia.org/wiki/In-place_algorithm) such that each element appear only *once* and return the new length.

Do not allocate extra space for another array, you must do this by **modifying the input array in-place** with O(1) extra memory.

<!--more-->

**Example 1:**

```
Given nums = [1,1,2],

Your function should return length = 2, with the first two elements of nums being 1 and 2 respectively.

It doesn't matter what you leave beyond the returned length.
```

**Example 2:**

```
Given nums = [0,0,1,1,1,2,2,3,3,4],

Your function should return length = 5, with the first five elements of nums being modified to 0, 1, 2, 3, and 4 respectively.

It doesn't matter what values are set beyond the returned length.
```

### 解题 - 双指针法

今天的题目也是easy的，去除数组中的重复项，且要在原地改变，不能用新的数组存储。很容易就可以想到用两个指针，同时开始，遇到不同的数字就把index往后挪一个并赋值。

这种方法在链表中用的比较多，这个在数据结构学习中可以再复习和练习。

先在纸上画了画流程，想了想测试用例

```
[1,1,2,3,4,4,5]
[]
[1]
```

囊括了长度为1和长度为0的特殊情况，这样在写代码时就不会遗漏。今天代码五分钟就写完了，且第二遍就ac了，开心。第一遍没过是因为我写了一句 int i = 0，emmm Java写多了，要改要改

```python
class Solution:
    def removeDuplicates(self, nums: List[int]) -> int:
        before_length = len(nums)
        index = 0
        if before_length <= 1:
            return before_length
        for i  in range(1,before_length):
            if nums[i] == nums[index]:
                continue
            else:
                nums[index+1] = nums[i]
                index += 1         
        return index+1
```

### 复杂度分析

- 时间复杂度：*O*(*n*)， 假设数组的长度是 *n*，那么 *i* 和 *j* 分别最多遍历 *n*步。
- 空间复杂度：*O*(1)。

### 思考

翻了翻官方解答，代码如下：

```java
public int removeDuplicates(int[] nums) {
    if (nums.length == 0) return 0;
    int i = 0;
    for (int j = 1; j < nums.length; j++) {
        if (nums[j] != nums[i]) {
            i++;
            nums[i] = nums[j];
        }
    }
    return i + 1;
}
```

官方答案的代码总是写的非常简洁漂亮，但有时候太简洁的代码也有可能带给人不太好的理解感，见仁见智吧，这道题目逻辑很简单所以不大能看出来，在复杂的系统中可能会有所体现，突然想到之前老板的一句话，宁愿多写几句if else，也要让别人能看得懂你写的什么，也借此敦促自己养成编程的好习惯，多写注释（现在的我写注释的习惯真的是一点也没。。），写逻辑清晰的代码呀。

