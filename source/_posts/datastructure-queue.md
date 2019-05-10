---
title: 数据结构之队列
date: 2019-03-02 16:23:45
tags:
- Data Structure
---

## 队列

队列是一种特殊的[线性表](https://baike.baidu.com/item/%E7%BA%BF%E6%80%A7%E8%A1%A8/3228081)，特殊之处在于它只允许在表的前端（front）进行删除操作，而在表的后端（rear）进行插入操作，和栈一样，队列是一种操作受限制的线性表。进行插入操作的端称为队尾，进行删除操作的端称为队头。

包括顺序队列、循环队列。有数组实现、链式实现

<!--more-->

## 队列的应用

- 迷宫问题。迷宫问题是指：给定给定一个M×N的迷宫图、入口与出口、行走规则。求一条从指定入口到出口的路径。所求路径必须是简单路径，即路径不重复。迷宫问题可以用栈或者队列来求解。其中使用队列求解出的路径是最短路径。

  求解思路：使用顺序队列（使用顺序队列的原因是：出队入队操作并不会删除结点，只是改变了队首队尾指针的值，最终还要通过队列中已出队节点来回溯得到路径），队列中的数据元素类型为格点坐标(i,j)和路径中上一格点在队列中的位置pre的封装。pre的设置是为了找到终点后由终点通过pre回溯到起点从而逆序打印出路径（采用递归实现）。在将一个能走的格点入队后，循环搜索它周围的四个格点，并将其中能走的入队，所以必须制定四个方向的搜索顺序（最后若有多条最短路径，则打印出哪一条由搜索顺序决定）。由于路径不重复，所以在在入队后将一个迷宫格点的值赋为-1，避免重复搜索。整体思路类似于广度优先搜索。https://blog.csdn.net/qq_35527032/article/details/78121942 

## Java 的实现

**Queue的实现**
1）没有实现的阻塞接口的LinkedList： 

​	实现了java.util.Queue接口和java.util.AbstractQueue接口**
　　内置的不阻塞队列： PriorityQueue 和 ConcurrentLinkedQueue
　　PriorityQueue 和 ConcurrentLinkedQueue 类在 Collection Framework 中加入两个具体集合实现。 
　　PriorityQueue 类实质上维护了一个有序列表。加入到 Queue 中的元素根据它们的天然排序（通过其 java.util.Comparable 实现）或者根据传递给构造函数的 java.util.Comparator 实现来定位。
　　ConcurrentLinkedQueue 是基于链接节点的、线程安全的队列。并发访问不需要同步。因为它在队列的尾部添加元素并从头部删除它们，所以只要不需要知道队列的大 小，　　　　    　　ConcurrentLinkedQueue 对公共集合的共享访问就可以工作得很好。收集关于队列大小的信息会很慢，需要遍历队列。

2)实现阻塞接口的：
　　java.util.concurrent 中加入了 BlockingQueue 接口和五个阻塞队列类。它实质上就是一种带有一点扭曲的 FIFO 数据结构。不是立即从队列中添加或者删除元素，线程执行操作阻塞，直到有空间或者元素可用。
五个队列所提供的各有不同：
  * ArrayBlockingQueue ：一个由数组支持的有界队列。
  * LinkedBlockingQueue ：一个由链接节点支持的可选有界队列。
  * PriorityBlockingQueue ：一个由优先级堆支持的无界优先级队列。
  * DelayQueue ：一个由优先级堆支持的、基于时间的调度队列。
  * SynchronousQueue ：一个利用 BlockingQueue 接口的简单聚集（rendezvous）机制。

## Python 的实现

**Python的Queue模块**中提供了同步的、线程安全的队列类，包括FIFO（先入先出)队列Queue，LIFO（后入先出）队列LifoQueue，和优先级队列PriorityQueue。这些队列都实现了锁原语，能够在多线程中直接使用。可以使用队列来实现线程间的同步。

## 用两个栈实现队列

```python
class QueueWithTwoStacks(object):
    def __init__(self):
        self._stack1 = []
        self._stack2 = []
        
    def appendTail(self,x):
        self._stack1.append(x)

    def deleteHead(self):
         if self._stack2:
             return self._stack2.pop()
         else:
             if self._stack1:
                while self._stack1:
                    self._stack2.append(self._stack1.pop())
                return self._stack2.pop()
             else:
                 return None
```

## 两个队列实现一个栈

1.栈为空：当两个队列都为空的时候，栈为空

2.入栈操作：当队列2为空的时候，将元素入队到队列1；当队列1位空的时候，将元素入队到队列2；如果队列1 和 队列2 都为空的时候，那就选择入队到队列1.

3.出队操作：当两个队列都为空的时候，引发错误“栈为空”；当队列2位空的时候，如果队列1中只有一个元素，则直接将队列1中的元素出队；如果队列1不止一个元素的时候，就将队列1的元素出队然后入队到队列2，知道队列1中只有一个元素，然后将队列1中的元素出队即可。当队列1位空的时候，如果队列2中只有一个元素，则直接将队列2中的元素出队；如果队列2不止一个元素的时候，就将队列2的元素出队然后入队到队列1，知道队列2中只有一个元素，然后将队列2中的元素出队即可。

```python
class StackWithTwoQueues(object):
    def __init__(self):
        self._stack1 = []
        self._stack2 = []
    def push(self,x):
        if len(self._stack1) == 0:
            self._stack1.append(x)
        elif len(self._stack2) == 0:
            self._stack2.append(x)
        if len(self._stack2) == 1 and len(self._stack1) >= 1:
            while self._stack1:
                self._stack2.append(self._stack1.pop(0))
        elif len(self._stack1) == 1 and len(self._stack2) > 1:
            while self._stack2:
                self._stack1.append(self._stack2.pop(0))
    def pop(self):
        if self._stack1:
            return self._stack1.pop(0)
        elif self._stack2:
            return self._stack2.pop(0)
        else:
            return None
```
## Leetcode 相关练习

Design Circular Deque（设计一个双端队列）
英文版：[Loading...](https://leetcode.com/problems/design-circular-deque/)
中文版：[Loading...](https://leetcode-cn.com/problems/design-circular-deque/)

```python
class MyCircularDeque:

    def __init__(self, k: int):
        """
        Initialize your data structure here. Set the size of the deque to be k.
        """
        self.queue = []
        self.size = k  

    def insertFront(self, value: int) -> bool:
        """
        Adds an item at the front of Deque. Return true if the operation is successful.
        """
        if self.isFull():
            return False
        self.queue.insert(0,value) 
        return True
        
    def insertLast(self, value: int) -> bool:
        """
        Adds an item at the rear of Deque. Return true if the operation is successful.
        """
        if self.isFull():
            return False
        self.queue.append(value)
        return True

    def deleteFront(self) -> bool:
        """
        Deletes an item from the front of Deque. Return true if the operation is successful.
        """
        if self.isEmpty():
            return False
        self.queue.pop(0)
        return True

    def deleteLast(self) -> bool:
        """
        Deletes an item from the rear of Deque. Return true if the operation is successful.
        """
        if self.isEmpty():
            return False
        self.queue.pop()
        return True     

    def getFront(self) -> int:
        """
        Get the front item from the deque.
        """
        if self.isEmpty():
            return -1
        return self.queue[0]
        

    def getRear(self) -> int:
        """
        Get the last item from the deque.
        """
        if self.isEmpty():
            return -1
        return self.queue[-1]

    def isEmpty(self) -> bool:
        """
        Checks whether the circular deque is empty or not.
        """
        if len(self.queue) == 0:
            return True
        return False

    def isFull(self) -> bool:
        """
        Checks whether the circular deque is full or not.
        """
        if len(self.queue) == self.size:
            return True
        return False
# Your MyCircularDeque object will be instantiated and called as such:
# obj = MyCircularDeque(k)
# param_1 = obj.insertFront(value)
# param_2 = obj.insertLast(value)
# param_3 = obj.deleteFront()
# param_4 = obj.deleteLast()
# param_5 = obj.getFront()
# param_6 = obj.getRear()
# param_7 = obj.isEmpty()
# param_8 = obj.isFull()
```

Sliding Window Maximum（滑动窗口最大值）
英文版：[Loading...](https://leetcode.com/problems/sliding-window-maximum/)
中文版：[Loading...](https://leetcode-cn.com/problems/sliding-window-maximum/)

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

## 参考

[Python剑指offer之两个栈实现一个队列-两个队列实现一个栈](https://blog.csdn.net/songyunli1111/article/details/79348034)

[用两个栈实现队列与用两个队列实现栈（Python实现）](https://www.cnblogs.com/hwf-73/p/7705100.html)

[Python 队列](https://blog.csdn.net/dta0502/article/details/80840480)