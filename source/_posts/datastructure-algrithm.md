---
title: 数据结构之算法思想
date: 2019-03-14 10:45:11
tags:
- Data Structure
---
## 递归
思考以下几个问题：
- 边界条件
- 递归前进段    （子问题的递推公式）
- 递归返回段    （最小子问题，即在程序的边界）
#### 优化的递归方法---记忆化搜索
（自顶向下,从n到1,先记录,后返回），dp是自底向上（从小到大）
用斐波那契来描述
```python
result = [-1 for i in range(n)]
def fibonacci(n,result):
	if n==0:
		return 0
	if n == 1:
		return 1
	if result[n] == -1:
		result[n] = fibonacci(n-1,result)+fibonacci(n-2,result)
	return result[n]
```
#### LeetCode上【70. 爬楼梯】
假设你正在爬楼梯。需要 n 阶你才能到达楼顶。
每次你可以爬 1 或 2 个台阶。你有多少种不同的方法可以爬到楼顶呢？
<!--more-->
```python
from functools import lru_cache
class Solution:
    @lru_cache(10**8)   # 抖机灵，设置缓存不会栈溢出，设置缓存有点像记忆递归
    def climbStairs(self, n):
        if n == 1:
            return 1
        elif n == 2:
            return 2
        else:
            return self.climbStairs(n - 1) + self.climbStairs(n - 2)
```
f(2)=f(1)+f(0)
f(3)=f(2)+f(1)
f(4)=f(3)+f(2)
.......
f(n)=f(n-2)+f(n-1)
```python
# 动态规划的简单写法 ，因为这个题目正好是n-1+n-2，可以这样写，如果n-3+n-5，最好用数组
		a = b = 1
        for i in range(n):
            a, b = b, a + b
        return a
```
```java
//动态规划的数组写法
class Solution {
    public int climbStairs(int n) {
        int sum[]=new int[n+1];
        sum[0]=0;sum[1]=1;sum[2]=2;
     for(int i=3;i<=n;i++){
         sum[i]=sum[i-2]+sum[i-1];
     }
        return sum[n];
    }
}
```
#### 递归的时间复杂度计算
https://www.cnblogs.com/cquljw/p/3807191.html 递归树非常好的例子

<1> 例：n! 
算法的递归方程为： T(n) = T(n - 1) + O(1); 

迭代展开： T(n) = T(n - 1) + O(1) 
= T(n - 2) + O(1) + O(1) 
= T(n - 3) + O(1) + O(1) + O(1) 
= ...... 
= O(1) + ... + O(1) + O(1) + O(1) 
= n * O(1) 
= O(n) 

这个例子的时间复杂性是线性的。 
<2> 例：如下递归方程： 
T(n) = 2T(n/2) + 2, 且假设n=2的k次方。 
T(n) = 2T(n/2) + 2 
= 2(2T(n/2*2) + 2) + 2 
= 4T(n/2*2) + 4 + 2 
= 4(2T(n/2*2*2) + 2) + 4 + 2 
= 2*2*2T(n/2*2*2) + 8 + 4 + 2 
= ... 
= 2的(k-1)次方 * T(n/2的(i-1)次方) + $(i:1~(k-1))2的i次方 
= 2的(k-1)次方 + (2的k次方) - 2 
= (3/2) * (2的k次方) - 2 
= (3/2) * n - 2 
= O(n) 
这个例子的时间复杂性也是线性的。 
<3> 例：如下递归方程： 
T(n) = 2T(n/2) + O(n), 且假设n=2的k次方。 
T(n) = 2T(n/2) + O(n) 
= 2T(n/4) + 2O(n/2) + O(n) 
= ... 
= O(n) + O(n) + ... + O(n) + O(n) + O(n) 
= k * O(n) 
= O(k*n) 
= O(nlog2n) //以2为底 
一般地，当递归方程为T(n) = aT(n/c) + O(n), T(n)的解为： O(n) (a<c && c>1) 
O(nlog2n) (a=c && c>1) //以2为底 
O(nlogca) (a>c && c>1) //n的(logca)次方，以c为底 
上面介绍的3种递归调用形式，比较常用的是第一种情况，第二种形式也有时出现，而第三种形式(间接递归调用)使用的较少，且算法分析 比较复杂。 下面举个第二种形式的递归调用例子。 
<4> 递归方程为：T(n) = T(n/3) + T(2n/3) + n 
为了更好的理解，先画出递归过程相应的递归树： 
n --------> n 
n/3 2n/3 --------> n 
n/9 2n/9 2n/9 4n/9 --------> n 
...... ...... ...... ....... ...... 
总共O(nlogn) 
累计递归树各层的非递归项的值，每一层和都等于n，从根到叶的最长路径是： 
n --> (2/3)n --> (4/9)n --> (12/27)n --> ... --> 1 
设最长路径为k，则应该有： 
(2/3)的k次方 * n = 1 
得到 k = log(2/3)n // 以(2/3)为底 
于是 T(n) <= (K + 1) * n = n (log(2/3)n + 1) 
即 T(n) = O(nlogn) 
由此例子表明，对于第二种递归形式调用，借助于递归树，用迭代法进行算法分析是简单易行的

## 回溯（DFS思想--有剪枝的DFS过程）
有时会遇到这样一类题目，它的问题可以分解，但是又不能得出明确的动态规划或是递归解法，此时可以考虑用回溯法解决此类问题。回溯法的优点 在于其程序结构明确，可读性强，易于理解，而且通过对问题的分析可以大大提高运行效率。但是，对于可以得出明显的递推公式迭代求解的问题，还是不要用回溯法，因为它花费的时间比较长。

为什么用DFS
深度优先搜索（DFS）和广度优先搜索（FIFO）
在 分支界限法中，一般用的是FIFO或最小耗费搜索；其思想是一次性将一个节点的所有子节点求出并将其放入一个待求子节点的队列。通过遍历这个队列（队列在 遍历过程中不断增长）完成搜索。而DFS的作法则是将每一条合法路径求出后再转而向上求第二条合法路径。而在回溯法中，一般都用DFS。为什么呢？这是因 为可以通过约束函数杀死一些节点从而节省时间，由于DFS是将路径逐一求出的，通过在求路径的过程中杀死节点即可省去求所有子节点所花费的时间。FIFO 理论上也是可以做到这样的，但是通过对比不难发现，DFS在以这种方法解决问题时思路要清晰非常多。 

回溯法可以被认为是一个有过剪枝的DFS过程
利用回溯法解题的具体步骤
初始化-试探-判断条件-符合前进，不符合回退-到头了就打印，全部到头了就返回
具体的如：

```java
void backtrack(int t)          
    //t表示递归深度，即当前扩展节点在解空间树的深度
{ 
    if ( t > n ) output(x);    
    //n控制递归深度，如果算法已经搜索到叶节点，记录输出可行解X
    else
    {
        for(int i = f(n,t) ; i <= g(n,t) ; i++)  
            //在深度t，i从未搜索过得起始编号到终止编号
        {
            x[t] = h(i);       //查看i这个点的值是否满足题目的要求
            if( constraint(t) && bound(t)) 
                backtrack(t+1)
       //constraint（t）为true表示在当前扩展节点处x[1:t]的取值满足问题的约束条件；
       //bound(t)为true表示当前扩展节点取值没有使目标函数越界；
       //为true表示还需要进一步的搜索子树，否则减去子树(即不再进行递归)
         }
    }
}
```



#### 利用回溯算法求解八皇后问题

将八位皇后放在一张8x8的棋盘上，使得每位皇后都无法吃掉别的皇后，（即任意两个皇后都不在同一条横线，竖线和斜线上），问一共有多少种摆法。

首先我们看一下特别暴力的方法：从8x8的格子里选8个格子，放皇后，然后测试是否满足条件，若满足则结果加1，否则换8个格子继续试。很显然，64选8，并不是个小数字，十亿级别的次数，够暴力。如果换成围棋的棋盘，画面就会太美而不敢算。
稍加分析，我们可以得到另一个不那么暴力的方法：显然，每行每列最多只能有一位皇后，如果基于这个事实再进行暴力破解，那结果会好很多。安排皇后时，第一行有8种选法，一旦第一行选定，假设选为（1，i），那么第二行只能选（2，j），其中，j不等于i，所以有7种选法。以此类推，需要穷举的情况有8！=40320种，比十亿级别的小很多了。
这看起来已经不错了，但尝试的次数还是随着问题规模按阶乘水平提高的，我们仍然不满意，所以，“递归回溯”的思想就被提出了，专治这种问题。

![test](https://upload-images.jianshu.io/upload_images/10987373-90ccb49867f06a28?imageMogr2/auto-orient/"两个个皇后的封锁范围")

如此继续下去，能安放下一位皇后的位置越来越少，那么我们最终如何能安放完这8位皇后呢？
首先我们看一下特别暴力的方法：从8x8的格子里选8个格子，放皇后，然后测试是否满足条件，若满足则结果加1，否则换8个格子继续试。很显然，64选8，并不是个小数字，十亿级别的次数，够暴力。如果换成围棋的棋盘，画面就会太美而不敢算。
稍加分析，我们可以得到另一个不那么暴力的方法：显然，每行每列最多只能有一位皇后，如果基于这个事实再进行暴力破解，那结果会好很多。安排皇后时，第一行有8种选法，一旦第一行选定，假设选为（1，i），那么第二行只能选（2，j），其中，j不等于i，所以有7种选法。以此类推，需要穷举的情况有8！=40320种，比十亿级别的小很多了。
这看起来已经不错了，但尝试的次数还是随着问题规模按阶乘水平提高的，我们仍然不满意，所以，“递归回溯”的思想就被提出了，专治这种问题。
为了理解“递归回溯”的思想，我们不妨先将4位皇后打入冷宫，留下剩下的4位安排进4x4的格子中且不能互相打架，有多少种安排方法呢？如果按照上面方式穷举，需要4！=24次尝试吗？
现在我们把第一个皇后放在第一个格子，被涂黑的地方是不能放皇后的

![test](https://upload-images.jianshu.io/upload_images/10987373-eccf38a9e988ed9c?imageMogr2/auto-orient/"一个皇后的封锁范围")

第二行的皇后只能放在第三格或第四格，比如我们放在第三格：

这样一来前面两位皇后已经把第三行全部锁死了，第三位皇后无论放在第三行的哪里都难逃被吃掉的厄运。于是在第一个皇后位于第一格，第二个皇后位于第三格的情况下此问题无解。所以我们只能返回上一步，来给2号皇后换个位置：

![test](https://upload-images.jianshu.io/upload_images/10987373-433bd25fe98113ab?imageMogr2/auto-orient/"给2号皇后换个位置")

此时，第三个皇后只有一个位置可选。当第三个皇后占据第三行蓝色空位时，第四行皇后无路可走，于是发生错误，则返回上层调整3号皇后，而3号皇后也别无可去，继续返回上层调整2号皇后，而2号皇后已然无路可去，则再继续返回上层调整1号皇后，于是1号皇后往后移一格位置如下，再继续往下安排：

![test](https://upload-images.jianshu.io/upload_images/10987373-68a42d15b8ddd569?imageMogr2/auto-orient/"回溯重新安排1号皇后")

上面的图例正是回溯递归思想的展现，然而知易行难，在代码中我们怎样来实现这种算法呢？

```python
count = 1
def queen(A, cur=0):
    global count
    if cur == len(A):
        print("-----------",count,"---------")
        print(A)
        print()
        count +=1
        return 
    for col in range(len(A)):
        A[cur], flag = col, True
        for row in range(cur):
            if A[row] == col or abs(col - A[row]) == cur - row:
                flag = False   #满足条件
                break
        if flag:
            queen(A, cur+1)
if __name__ == '__main__':
    queen([None]*8)
```

非递归解这个问题，很显然是要去维护一个stack来保存一个路径了。简单来说，这个栈中维护的应该是“尚未尝试去探索的可能”，当我开始检查一个特定的位置，如果检查通过，那么应该做的是首先将本位置右边一格加入栈，然后再把下一行的第一个格子加入栈。注意前半个操作很容易被忽视，但是如果不将本位置右边一格入栈，那么如果基于本格有皇后的情况进行的递归最终没有返回一个结果的话，接下来就不知道往哪走了。如果使用了栈，那么用于扫描棋盘的游标就不用自己在循环里+=1了，循环中游标的移动全权交给栈去维护。

```python
def EightQueen(board):
    blen = len(board)
    stack = Queue.LifoQueue()
    stack.put((0,0))    # 为了自洽的初始化
    while not stack.empty():
        i,j = stack.get()
        if check(board,i,j):    # 当检查通过
            board[i] = j    # 别忘了放Queen
            if i >= blen - 1:
                print board    # i到达最后一行表明已经有了结果
              #  break   ###
            else:
                if j < blen - 1:    # 虽然说要把本位置右边一格入栈，但是如果本位置已经是行末尾，那就没必要了
                    stack.put((i,j+1))
                stack.put((i+1,0))    # 下一行第一个位置入栈，准备开始下一行的扫描
        elif j < blen - 1:
            stack.put((i,j+1))    # 对于未通过检验的情况，自然右移一格即可
```

** ps python global**
python 中，一个变量的作用域总是由在代码中被赋值的地方所决定的。

函数定义了本地作用域，而模块定义的是全局作用域。
如果想要在函数内定义全局作用域，需要加上global修饰符。
变量名解析：**LEGB原则**
当在函数中使用未认证的变量名时，Python搜索４个作用域

- [本地作用域(L)(函数内部声明但没有使用global的变量)，
- 之后是上一层结构中def或者lambda的本地作用域(E),
- 之后是全局作用域(G)（函数中使用global声明的变量或在模块层声明的变量），
- 最后是内置作用域(B)（即python的内置类和函数等）］

并且在第一处能够找到这个变量名的地方停下来。如果变量名在整个的搜索过程中都没有找到，Python就会报错。
补：上面的变量规则只适用于简单对象，当出现引用对象的属性时，则有另一套搜索规则:属性引用搜索一个或多个对象，而不是作用域，并且有可能涉及到所谓的"继承"

#### 利用回溯算法求解 0-1 背包问题



```python
bestV=0
curW=0
curV=0
bestx=None

def backtrack(i):
	global bestV,curW,curV,x,bestx
	if i>=n:
		if bestV<curV:
			bestV=curV
			bestx=x[:]
	else:
		if curW+w[i]<=c:
			x[i]=True
			curW+=w[i]
			curV+=v[i]
			backtrack(i+1)
			curW-=w[i]
			curV-=v[i]
		x[i]=False
		backtrack(i+1)
	

if __name__=='__main__':
	n=5
	c=10
	w=[2,2,6,5,4]
	v=[6,3,5,4,6]
	x=[False for i in range(n)]
	backtrack(0)
	print(bestV)
	print(bestx)
```



## 分治

#### 利用分治算法求一组数据的逆序对个数

给定一个数组N，求其中存在的逆序数对。
逆序数的定义，如果N[i]>N[j](i<j)，则为一对逆序数。
求解思路：
1：暴力求解（从第一个元素开始遍历，遇到一个比其小的就记录一下）。
2：分治思想：归并排序的副产物，在Merge()时记录逆序数对。
依次遍历list中每一个元素，对每一个元素，查找其之后的每一个元素并与其比较，出现逆序对则计数+1，时间复杂度为O(n^2)——(n-1)+(n-2)+…+1。

但对于这个算法，实际上有隐含的条件被我们忽略了，例如当我们发现a2<a1时，那么对于和a1比较过的所有元素，与a1形成逆序对的所有元素实际上也与a2形成逆序对（除a2外），实际上时间复杂度可以精简到O(nlogn)。
我们可以换一个思路来考虑这个问题：对于一个a1<a2<a3...<an的有序序列，逆序对是为0的，那么我们实际上是要进行一个排序，当发现一次逆序，计数+1，而排序算法的最优解法为O(nlogn)

代码python实现如下——基于分治思想的归并排序，将有序列表和逆序对计数一同返回的递归解法：


```python
def InversionNum(lst):
    # 改写归并排序,在归并排序中，每当R部分元素先于L部分元素插入原列表时，逆序对数要加L剩余元素数
    if len(lst) == 1:
        return lst,0
    else:
        n = len(lst) // 2
        lst1,count1 = InversionNum(lst[0:n])
        lst2,count2 = InversionNum(lst[n:len(lst)])
        lst,count = Count(lst1,lst2,0)
        return lst,count1+count2+count
 
def Count(lst1, lst2,count): 
    i = 0
    j = 0
    res = []
    while i < len(lst1) and j < len(lst2):
        if lst1[i] <= lst2[j]:
            res.append(lst1[i])
            i += 1
        else:
            res.append(lst2[j])
            count += len(lst1)-i # 当右半部分的元素先于左半部分元素进入有序列表时，逆序对数量增加左半部分剩余的元素数
            j += 1
    res += lst1[i:]
    res += lst2[j:]
    return res,count
 
 
print(InversionNum([11,10,9,8,7,6,5,4,3,2,1])) 
# 输出为：[1,2,3,4,5,6,7,8,9,10,11] 55
```

## 动态规划
**关键**：找到递推式（状态转移方程）
只要问题可以划分为`规模更小的子问题`，并且`原问题的最优解`中包含了`子问题的最优解`，则可以考虑用动态规划解决。动态规划的实质是分治思想和解决冗余。因此，动态规划是一种将问题实例分解为更小的/相似的子问题，并存储子问题的解，使得每个子问题只求解一次，最终获得原问题的答案，以解决最优化问题的算法策略。
**DP与贪心法**
1.与贪心法类似，都是将问题实例归纳为更小的、相似的子问题，并通过求解子问题产生一个全局最优解。
2.贪心法选择`当前最优解`，而动态规划通过求解`局部子问题的最优解`来达到全局最优解。
**DP与递归**
1.与递归法类似，都是将问题实例归纳为更小的、相似的子问题。
2.递归法需要对子问题进行重复计算，需要耗费更多的时间与空间，而动态规划对每个子问题只求解一次。对递归法进行优化，可以使用记忆化搜索的方式。它与递归法一样，都是自顶向下的解决问题，而动态规划是自底向上的解决问题。
递归问题——>重叠子问题——>  1.记忆化搜索（自顶向上的解决问题）2.动态规划（自底向上的解决问题）
**0-1 背包问题**
01背包问题为什么能列出状态转移方程？是因为`每个状态的最优解，都是根据之前的状态的最优解获得`。具体到背包问题，有以下几点：
　　a) 当物品备选情况（物品备选情况指：可供选择的物品的集合）一致时，背包容量M越大，那么sum_v一定大于等于原来的值。
　　b) 背包容量M确定时，可供选择的物品N越多，那么sum_v一定大于等于原来的值。
　　c) **由a)和b)可得，sum_v的最大值就是当M和N取到最大值时的sum_v**

在01背包问题中，在选择是否要把一个物品加到背包中，必须把该物品加进去的子问题的解与不取该物品的子问题的解进行比较，这种方式形成的问题导致了许多重叠子问题，使用动态规划来解决。n=5是物品的数量，c=10是书包能承受的重量，w=[2,2,6,5,4]是每个物品的重量，v=[6,3,5,4,6]是每个物品的价值，先把递归的定义写出来：

v(i,j)，其中(1<=j<=C)，(1<=i<=n)表示前i件物体在容量为j的情况下的最大价值，如果当前背包不够放第i件物品，最大价值就是前i-1件物品在当前容量下的价值，如果够放第i件物品，最大价值就是放进i后和前i-1件相比较大的那个。
　　　　1) **j<w(i)**      **V(i,j)=V(i-1,j)**
　　　　2) **j>=w(i)**     **V(i,j)=max**｛**V(i-1,j)**，**V(i-1,j-w(i))+v(i)** **｝**

```python
##代码中有两个注意点，一个是数组要多一个位置，保存选择0个物品和0价值的状态
##另一个是判断第i个物品的话是w【i-1】
def bag(n, c, w, v):
    # 置零，表示初始状态
    value = [[0 for j in range(c + 1)] for i in range(n + 1)]
    for i in range(1, n + 1):
        for j in range(1, c + 1):
            value[i][j] = value[i - 1][j]    
            # 背包总容量够放当前物体，遍历前一个状态考虑是否置换
            if j >= w[i - 1] and value[i][j] < value[i - 1][j - w[i - 1]] + v[i - 1]:
                value[i][j] = value[i - 1][j - w[i - 1]] + v[i - 1]
    for x in value:
        print(x)
    return value
```
**最小路径和（详细可看 Minimum Path Sum）**
给定一个m*n的数组，数组中包含非负数，从该数组左上角到该数组右下角的最小路径和。（只能向下或者向右移动）。数组格式如下：
[[1,3,1],
[1,5,1],
[4,2,1]]
思路:
从左上角开始对数组进行遍历，将grid（数组）内容存储为走到当前位置的最短路径和。故只考虑当前位置的左边和上边哪个小，就选择哪个路径即可。
```python
def minPathSum(matrix):
    m = len(matrix)
    n=len(matrix[0])
    ###注意这里是n和m，别把矩阵长和宽写错了
    path = [[0 for i in range(n)] for j in range(m)]
    path[0][0] = matrix[0][0]
    for i in range(1,m):
        path[i][0] = path[i-1][0]+matrix[i][0]
    for j in range(1,n):
        path[0][j] = path[0][j-1]+matrix[0][j]

    for i in range(1,m):
        for j in range(1,n):
            path[i][j] = matrix[i][j]+ min(path[i-1][j],path[i][j-1])
    for x in path:
        print(x)
    return path[m-1][n-1]
        
#recursive的记忆化搜索
class Solution(object):
    def minPathSum(self, grid):
        final = []
        m = len(grid)-1
        n = len(grid[0])-1
        print m
        print n
        def Iteration(i,j,sum_0):
            if i<m and j<n:
                Iteration(i+1,j,sum_0+grid[i+1][j])
                Iteration(i,j+1,sum_0+grid[i][j+1])
            if i==m and j==n:
                final.append(sum_0)
            if i==m and j<n:
                Iteration(i,j+1,sum_0+grid[i][j+1])
            if i<m and j==n:
                Iteration(i+1,j,sum_0+grid[i+1][j])
        Iteration(0,0,grid[0][0])
        return min(final)
```



**编程实现莱文斯坦最短编辑距离**

最小编辑距离或莱文斯坦距离（Levenshtein），指由字符串A转化为字符串B的最小编辑次数。允许的编辑操作有：删除，插入，替换。具体内容可参见：[维基百科—莱文斯坦距离](https://link.jianshu.com?t=https://zh.wikipedia.org/zh-cn/%E8%90%8A%E6%96%87%E6%96%AF%E5%9D%A6%E8%B7%9D%E9%9B%A2)。
 一般代码实现的方式都是通过动态规划算法，找出从A转化为B的每一步的最小步骤。

```python
def normal_leven(str1, str2):
      len_str1 = len(str1) + 1
      len_str2 = len(str2) + 1
      #create matrix
      matrix = [0 for n in range(len_str1 * len_str2)]
      #init x axis
      for i in range(len_str1):
          matrix[i] = i
      #init y axis
      for j in range(0, len(matrix), len_str1):
          if j % len_str1 == 0:
              matrix[j] = j // len_str1
          
      for i in range(1, len_str1):
          for j in range(1, len_str2):
              if str1[i-1] == str2[j-1]:
                  cost = 0
              else:
                  cost = 1
              matrix[j*len_str1+i] = min(matrix[(j-1)*len_str1+i]+1,
                                          matrix[j*len_str1+(i-1)]+1,
                                          matrix[(j-1)*len_str1+(i-1)] + cost)
          
      return matrix[-1]
```

**编程实现查找两个字符串的最长公共子序列**

见下文

动态规划做过的，复习一下

**编程实现一个数据序列的最长递增子序列**（LIS）

求最长递增子序列的递推公式为：

F[1] = 1;

F[i] = max{1,F[j]+1|aj<ai && j<i}

```python
class myStack:
    #找出以元素i结尾的最长递增子序列
    #每一次为ｉ进行分配时，要检查前面所有的算法ai(i<x)
    #若ai小于ax，则说明ax可以跟在ai后形成一个新的递增子序列
    #否则，以ax结尾的递增子序列的最长长度为1
    def getHeight(self, men, n):
        longest = {}    #c存一个字典
        longest[0] = 1
        for i in range(1, len(men)):
            maxlen = -1
            for j in range(0, i):
                if men[i]>men[j] and maxlen<longest[j]:
                    maxlen = longest[j]
            if maxlen>=1:    #说明之前的递增序列中，有ax可以跟的
                longest[i] = maxlen +1
            else:
                longest[i] = 1
        return max(longest.values())
```

附上另一道练习

先升后降
Description

从一列数中筛除尽可能少的数使得从左往右看，这些数是从小到大再从大到小的。


Input

输入时一个数组，数值通过空格隔开。


Output

输出筛选之后的数组，用空格隔开。如果有多种解雇哦，则一行一种结果。

Sample Input 1
1 2 4 7 11 10 9 15 3 5 8 6

Sample Output 1

1 2 4 7 11 10 9 8 6
"""

"""
2 1 5 3 6 4 8 9 7

```python
def all_lis(nums, B, peak_index, lis, all_lis_list):
    if B[peak_index] == 1:
        lis.append(nums[peak_index])
        lis.reverse()
        all_lis_list.append(lis)
    else:
        curr_len = B[peak_index]
        lis.append(nums[peak_index])
        for i in range(peak_index):
            if B[i] == curr_len-1 and nums[i] < nums[peak_index]:
                curr_lis = lis.copy()
                all_lis(nums, B, i, curr_lis, all_lis_list)


def all_lds(nums, C, peak_index, lds, all_lds_list):
    if C[peak_index] == 1:
        lds.append(nums[peak_index])
        all_lds_list.append(lds)
    else:
        curr_len = C[peak_index]
        lds.append(nums[peak_index])
        for i in range(peak_index, len(nums)):
            if C[i] == curr_len-1 and nums[i] < nums[peak_index]:
                curr_lds = lds.copy()
                all_lds(nums, C, i, curr_lds, all_lds_list)


if __name__ == '__main__':
    nums = [int(x) for x in input().split(" ")]

    n = len(nums)
    B = [0] * n
    C = [0] * n
    for i in range(n):
        B[i] = 1
        for j in range(i):
            if nums[j] < nums[i] and B[j] + 1 > B[i]:
                B[i] = B[j] + 1

    for i in range(n - 1, -1, -1):
        C[i] = 1
        for j in range(n - 1, i - 1, -1):
            if nums[j] < nums[i] and C[j] + 1 > C[i]:
                C[i] = C[j] + 1

    max_len = 0
    div = []
    for i in range(n):
        len_i = B[i] + C[i] - 1
        if len_i > max_len:
            max_len = len_i
            div.clear()
            div.append(i)
        elif len_i == max_len:
            div.append(i)

    for peak_index in div:
        all_lis_list = []

        all_lis(nums, B, peak_index, [], all_lis_list)
        all_lds_list = []
        all_lds(nums, C, peak_index, [], all_lds_list)

        for i in range(len(all_lis_list)):
            for j in range(len(all_lds_list)):
                lis = all_lis_list[i].copy()
                lds = all_lds_list[j].copy()
                lis.extend(lds[1:])
                one = [str(x) for x in lis]
                one_str = " ".join(one)
                print(one_str)
```



## LeetCode 练习题
#### 实战递归：

#### Letter Combinations of a Phone Number(17)

厉害的非递归

    class Solution:
        def letterCombinations(self, digits):
            if not digits:
                return []
    
            digit2chars = {
                '2': 'abc',
                '3': 'def',
                '4': 'ghi',
                '5': 'jkl',
                '6': 'mno',
                '7': 'pqrs',
                '8': 'tuv',
                '9': 'wxyz'
            }
    
            res = [ i for i in digit2chars[digits[0]] ]
    
            for i in digits[1:]:
                res = [ m+n for m in res for n in digit2chars[i] ]
                print (res)
    
            return res   
递归思路：

一种是传送生成器，另一种是传统递归。

1.递归生成器

```
class Solution:
	def letterCombinations(self, digits):
		return list(self.recur(digits))     

	def recur(self, x):               #由于LeetCode验证代码时不会自动将生成器转化为列表，所以只能生成器写在外面，主函数只打印答案
		dic = {'2': 'abc', '3': 'def', '4': 'ghi', '5': 'jkl', '6': 'mno', '7': 'pqrs', '8': 'tuv', '9': 'wxyz'}
		if len(x) == 0:                 #将digits为空值拿出来特别处理
			return []
		for i in dic[x[0]]:
			if len(x) == 1:             #递归到digits只剩最后一个值挨个生成该值的映射  
				yield i
			else:
				for j in self.recur[1:]):       #这里返回的生成器，可以用来迭代
					yield i + j           #拼接字符串，一层一层向上返还
```

 2.传统递归

```
class Solution:
    def letterCombinations(self, digits):
        out = []
        dic = {'2': 'abc', '3': 'def', '4': 'ghi', '5': 'jkl', '6': 'mno', '7': 'pqrs', '8': 'tuv', '9': 'wxyz'}
        if len(digits) == 0:
            return []
        for i in dic[digits[0]]:
            if len(digits) == 1:
                out.append(i)
                if i == dic[digits[0]][-1]:
                    return out
            else:
                for j in self.letterCombinations(digits[1:]):
                    out.append(i+j)
                    if i == dic[digits[0]][-1] and  j == self.letterCombinations(digits[1:])[-1]:
                        return out
```

#### permutations全排列(46)

递归、迭代、动归

```python
class Solution(object):
    def permute(self, nums):
        if len(nums)<=1:
            return [nums]
        output = [[nums[0],nums[1]],[nums[1],nums[0]]]
        for i in range(2,len(nums)):            
            tmp = nums[i]
            output_new = []
            for j in range(len(output)):
                output[j].append(tmp)
                output_new.append(output[j])
                for k in range(len(output[j])-1):
                    line = [n for n in output[j]]
                    line[len(line)-1] = line[k]
                    line[k] = tmp
                    output_new.append(line)
            output = output_new
        return output

 #使用python自带的permutations函数，直接进行全排列。

from itertools import permutations
class Solution(object):
    def permute(self, nums):
        return list(permutations(nums))

    #recursive
class Solution(object):
    def permute(self, nums):
        res = []
        self.dfs(nums, res, [])
        return res
        
    def dfs(self, nums, res, path):
        if not nums:
            res.append(path)
        else:
            for i in xrange(len(nums)):
                self.dfs(nums[:i] + nums[i + 1:], res, path + [nums[i]])
                
       #回溯
class Solution(object):
    def permute(self, nums):
        """
        :type nums: List[int]
        :rtype: List[List[int]]
        """
        visited = [0] * len(nums)
        res = []
        
        def dfs(path):
            if len(path) == len(nums):
                res.append(path)
            else:
                for i in range(len(nums)):
                    if not visited[i]:
                        visited[i] = 1
                        dfs(path + [nums[i]])
                        visited[i] = 0
        
        dfs([])
        return res
```



#### 实战DP：Leetcode上Palindrome  Partitioning  II(132)

给定一个s，可以将s拆成若干个回文子字符串之和，如果拆成了m个子字符串，那么我们称s可以被m-1 cut。那么返回s的最小cut。

这里需要二重动态规划，一个用来记录p[i][j]判断s[i][j]是否回文字符串，另外一个ans[i]代表s[:i]的最小cut是多少。如果s[i :j]是回文字符串，那么ans[j] = min(ans[j],ans[i - 1] + 1)。

```python
class Solution(object):
    def minCut(self, s):
        """
        :type s: str
        :rtype: int
        """
        size = len(s)
        ans = [i for i in range(size)]
        p = [[False for i in range(size)] for j in range(size)]
        j = 1
        while j < size:
            i,ans[j] = j - 1,min(ans[j],ans[j - 1] + 1) 
            p[j][j] = True
            while i >= 0:
                if s[i] == s[j] and ((j - i) < 2 or  p[i+1][j-1]):
                    p[i][j] = True
                    if i == 0:
                        ans[j] = 0
                    else:
                        ans[j] = min(ans[j],ans[i - 1] + 1)
                i -= 1
            j += 1
        return ans[size - 1]
```



## 参考

https://www.jianshu.com/p/65c8c60b83b8

https://www.cnblogs.com/franknihao/p/9416145.html

https://blog.csdn.net/jushang0235/article/details/78841915

https://www.jianshu.com/p/466cf6624e26
https://blog.csdn.net/u013166817/article/details/85449218

