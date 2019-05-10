---
title: leetcode-238 Product of Array Except Self 除自身以外数组的乘积
date: 2019-03-03 11:20:26
tags:
- leetcode
---

## 题目

Given an array `nums` of *n* integers where *n*> 1,  return an array `output` such that `output[i]` is equal to the product of all the elements of `nums` except `nums[i]`.

<!--more-->

**Example:**

```
Input:  [1,2,3,4]
Output: [24,12,8,6]
```

**Note:** Please solve it **without division** and in O(*n*).

**Follow up:**
Could you solve it with constant space complexity? (The output array **does not**count as extra space for the purpose of space complexity analysis.)

## 解题

一开始想如何遍历一遍就能得出所有的答案没想明白，后来想到了，要求除了自身的其他所有项，只要从左边遍历一遍，获得该位置左边所有项，再从右边遍历一遍，获得该位置右边所有项即可，完美避开当前位置的数字。即把数组看为nums[left,i,right]，代码一遍就ac了：

```python
"""
[1,2]
[1,2,3]
[-1,0,1]
"""
class Solution:
    def productExceptSelf(self, nums: List[int]) -> List[int]:
        n =len(nums)
        result = [1 for i in range(n)]
        result_left = 1
        result_right = 1
        for i in range(n-1):
            result_left *=nums[i]
            result[i+1] =result_left
        for i in range(n-1,0,-1):
            result_right *=nums[i]
            result[i-1] *= result_right
        return result   ###我这里只使用了一个数组，大部分解法都是左右两个数组，然后相乘
```

## 复杂度分析

时间复杂度：O(n) 遍历2n次

空间复杂度：O(n) 保存结果数组

## 思考

这道题有两种思路。

1.利用输出数组作为O(n)的空间，第一遍从左往右遍历数组，给输出数组每一位赋值从nums[0] 到 nums[i - 1]的乘积；第二遍从右往左遍历数组，给输出数组每一位赋值从nums[nums.length - 1] 到 nums[i + 1]的乘积，两遍乘积乘起来就是结果。

2.用一个变量保存从左到右的乘积，然后递归调用方法，利用函数的栈空间保存从右到左的乘积，两者相乘得到结果。这种本质上是O(1)空间，但是效率不好（这个代码写的，我看得贼别扭）

```java
public int[] productExceptSelf(int[] nums) {
        int[] result = new int[nums.length];
        calculate(nums,result,0,1);
        return result;
    }
    private int calculate(int[] nums,int[] result,int index,int leftProduct){
        int rightProduct = 1;
        if(index < nums.length - 1){
            rightProduct = calculate(nums,result,index+1,leftProduct * nums[index]);
        }
        result[index] = leftProduct * rightProduct;
        return rightProduct * nums[index];
    }
```

