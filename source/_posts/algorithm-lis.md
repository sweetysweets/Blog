---
title: 动态规划算法笔记
date: 2018-12-15 09:11:53
tags:
- 算法
---

# 动态规划算法

### 问题描述

从一列数中筛除尽可能少的数使得从左往右看，这些数是从小到大再从大到小的。

### 解题思路

假设一个数组arr[n]，它的分段点是i（0-i递增，i到n-1递减），假设我们用方法LIS(i)（最长递增子序列）找到从0到i的递增子序列，LDS找到从i到n-1的最长递减子序列，那么它的总长度为LIS(i) + LDS(i) - 1，所以我们扫描整个数组，即让i从0到n-1，找出使LIS(i) + LDS(i) - 1最大的即可。

#### 预备知识--最长递增子序列的求解方法

以i结尾的序列的最长递增子序列和其[0, i - 1]“前缀”的最长递增子序列有关，设LIS[i]保存以i结尾的最长递增子序列的长度：
​    若i = 0，则LIS[i] = 1；
​    若i > 0，则LIS[i]的值和其[0, i - 1]前缀的最长递增子序列长度有关，用j遍历[0, i - 1]得到其最长递增子序列为LIS[j]，对每一个LIS[j]，如果序列array[j]  < array[i]并且LIS[j] + 1 > LIS[i]，则LIS[i]的值变成LIS[j] + 1。即：
​    LIS[i] = max{1, LIS[j] + 1}，其中array[i] > array[j] 且 j = [0, i - 1]。



我们假设a[0]....a[i-1] 有一个最长递增子序列，其长度f(i-1)<=i, 且该最长递增子序列的最后一个元素为b。

​      那么对于a[0].... a[i] 而言，如果b<a[i]，那么f(i)=f(i-1)+1，且最长递增子序列的最后一个元素变成了a[i]。如果b>=a[i]，那么f(i)=f(i-1)。

​      上面的过程有一个难点：如果a[0]....a[i-1] 有多个最大长度为f(i-1)的递增子序列怎么办？需不需要所有长度等于f(i-1)的递增子序列的最后一个元素b0...bi全部存储起来，再一一和a[i]比较大小呢？如果是这样，那么整个算法与上面的分治策略将没有什么不同了？

​      事实上，并不需要怎么做。我们举个例子： a[]={1、2、5、3、7}

​      a[0] ... a[3] 的最大递增子序列有两个{1,2,5}和{1,2,3}，当增加a[4]的时候，如果a[4]>5，则两个子序列都需要增加a[4]；如果a[4]>3，则{1,2,3}+a[4]将必定成为新的最大子序列，而{1,2,5}不确定。因此我们看出，只要保存所有最大序列的最小的末尾元素即可。

   代码如下所示。

```python

```




（2）二分查找+动态规划实现
​    假设存在一个序列d[1...9] = 2 1 5 3 6 4 8 9 7，可以看出它的LIS长度是5。
​    下面一步一步试着找到它。
​    我们定义一个序列B，然后令i = 1 to 9逐个考察这个序列。
​    此外，我们用一个变量len来记录现在的最长算到多少。
​    首先，把d[1]有序的放到B中，令B[1] = 2，就是说当只有一个数字2的时候，长度为1的LIS的最小末尾是2，这时len = 1；
​    然后，把d[2]有序的放到B中，令B[1] = 1，就是说长度为1的LIS的最小末尾是1，d[1] = 2已经没用了，很容易理解吧，这时len = 1；
​    接着，d[3] = 5，d[3] > B[1]，所以令B[1 + 1] = B[2] = d[3] = 5，就是说长度为2的LIS的最小末尾是5，很容易理解吧，这时B[1...2] = 1, 5，len = 2；
​    再来，d[4] = 3，它正好在1,5之间，放在1的位置显然不合适，因为1小于3，长度为1的LIS最小末尾应该是1，这样很容易推知，长度为2的LIS最小末尾是3，于是可以把5淘汰掉，这时B[1...2] = 1,3，len = 2；
​    继续，d[5] = 6，它在3的后面，因为B[2] = 3，而6在3后面，于是很容易推知B[3] = 6，这时B[1...3] = 1,3,6，还是很容易理解吧？这时len = 3；
​    第6个，d[6] = 4，你看它在3和6之间，于是就可以把6替换掉，得到B[3] = 4。B[1...3] = 1,3,4，这时len = 3；
​    第7个，d[7] = 8，它很大，比4大，于是B[4] = 8，这时len = 4；
​    第8个，d[8] = 9，得到B[5] = 9，len继续增大，这时len = 5；
​    最后一个，d[9] = 7，它在B[3] = 4和B[4] = 8之间，所以我们知道，最新的B[4] = 7, B[1...5] = 1,3,4,7,9，len = 5。
​    于是我们知道了LIS的长度为5。
​    注意，注意。这个1,3,4,7,9不是LIS，它只是存储了对应长度LIS的最小末尾。有了这个末尾，我们就可以一个一个地插入数据。虽然最后一个d[9] = 7更新进去对于这个数组数据没有什么意义，但是如果后面再出现两个数字8和9，那么就可以把8更新到d[5]，9更新到d[6]，得到LIS的长度为6。
​    然后应该发现一件事情了：在B中插入数据是有序的，而且进行替换而不需要移动——也就是说，可以使用二分查找，将每一个数字的插入时间优化到O(logn)，于是算法的时间复杂度就降低到了O(nlogn)了。
​    代码如下：
int LIS(int *array,int len)
{
​    //LIS数组中存储的是 递增子序列中最大值最小的子序列的最后一个元素（最大元素）在array中的位置
​    int *LIS = new int[len];
​    int left,mid,right;
​    int max=1;
​    LIS[0]=array[0];
​    for(int i = 1;i < len;++i)
​    {
​        left = 0;
​        right = max;
​        while(left <=right)
​        {
​            mid = (left+right)/2;
​            if(LIS[mid] < array[i])
​                left = mid +1;
​            else
​                right = mid -1;
​        }
​        LIS[left] = array[i];//插入
​        if(left > max)
​        {
​            max++;
​        }
​    }
​    delete LIS;
​    return max;
}
​    

    下面就开始实现“从一列数中筛除尽可能少的数使得从左往右看，这些数是从小到大再从大到小的“这个问题。
    双端LIS问题，用动态规划的思想可以解决，目标规划函数为max{B[i] + C[i] - 1}，其中B[i]是从左到右的，0~i个数之间满足递增的数字个数；C[i]为从右到左的，n- 1 ~ i个数之间满足递增的数字个数。最后结果为n - max + 1，其中动态规划的时候，可以用二分查找进行处理，如上述求最长递增子序列的方法二。
    代码如下。
/*
*copyright@nciaebupt 转载请注明出处
*问题：从一列数中筛除尽可能少的数使得从左往右看，这些数是从小到大再从大到小的（网易）。
*比如数列1,4,3,5,6,7,2,0 删除的最少的数的个数为1
*求解思路：双端LIS问题，使用动态规划的思路求解，时间复杂度O(nlog(n))
*/
#include <cstdio>
#include <iostream>

using namespace std;

int DoubleEndLIS(int *array,int len)
{
​    int left,mid,right;
​    int max=0;
​    int k =0;

    //LIS数组中存储的是 递增子序列中最大值最小的子序列的最后一个元素（最大元素）在array中的位置
    int *LIS = new int[len];
    //从左到右LIS中最长子序列中最大值最小的子序列的最后一个元素所在的位置,也就是0~i的数字序列中最长递增子序列的长度-1
    int *B = new int[len];
    //从右到左LIS中最长子序列中最大值最小的子序列的最后一个元素所在的位置,也就是len-1~i的数字序列中最长递增子序列的长度-1
    int *C = new int[len];
    //从左到右
    for(int i = 0;i < len;++i)//LIS数组清零
    {
        B[i] = 0;
        LIS[i] = 0;
    }
    LIS[0] = array[0];
    for(int i = 1;i < len;++i)
    {
        left = 0;
        right = B[k];
        while(left <= right)
        {
            mid = (left + right)/2;
            if(array[i] < LIS[mid])
            {
                right = mid - 1;
            }
            else
            {
                left = mid + 1;
            }
        }
    
        LIS[left] = array[i];//将array[i]插入到LIS中
        if(left > B[k])
        {
            B[k+1] = B[k] + 1;
            k++;
        }
    }
    for(int i = 0;i < k;++i)
    {
        B[i]++;
    }
    //从右到左
    for(int i = 0;i < len;++i)//LIS数组清零
    {
        C[i] = 0;
        LIS[i] = 0;
    }
    k = 0;
    LIS[0] = array[len-1];
    for(int i = len-2;i >= 0;--i)
    {
        left = 0;
        right = C[k];
        while(left <= right)
        {
            mid = (left + right)/2;
            if(array[i] < LIS[mid])
            {
                right = mid - 1;
            }
            else
            {
                left = mid + 1;
            }
        }
        LIS[left] = array[i];
        if(left > C[k])
        {
            C[k+1] = C[k] + 1;
            k++;
        }
    }
    for(int i = 0;i <= k;++i)
    {
        C[i]++;
    }
    
    //求max
    for(int i = 0;i < len;++i)
    {
        //cout<<B[i]<<"  "<<C[i]<<endl;
        if(B[i]+C[i]>max)
            max=B[i] + C[i];
    }
    
    return len - max +1;
}

int main(int args,char ** argv)
{
​    int array[] = {1,4,3,5,6,7,2,0};
​    int len = sizeof(array)/sizeof(int);
​    int res = DoubleEndLIS(array,len);
​    cout<<res<<endl;
​    getchar();
​    return 0;
}
--------------------- 