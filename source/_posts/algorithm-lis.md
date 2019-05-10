---
title: 背包问题全解
date: 2018-12-15 09:11:53
tags:
- 算法
---

背包问题是算法设计中一种重要的设计问题，下面从背包问题及其变形中讲解背包问题的不同解决方法。

<!--more-->

# 0/1背包问题

0/1背包问题：给定n种物品和一个容量为w的背包，物品的重量是ni，其价值为vi，背包问题是如何使选择装入背包内的物品，使得装入背包中的物品的总价值最大。其中，每种物品只有全部装入背包或不装入背包两种选择。

分别用蛮力法、动态规划法、回溯法和分支限界法求解

## 蛮力法

对于有n种可选物品的0/1背包问题，其解空间由长度为n的0-1向量组成,可用子集数表示。在搜索解空间树时，深度优先遍历，搜索每一个结点，无论是否可能产生最优解，都遍历至叶子结点，记录每次得到的装入总价值，然后记录遍历过的最大价值。

暴力遍历2^n次

```python
def bag(n,w,vi,wi):
  best_v = 0
  for i in range(pow(2,n)):##equals 2**n
    binary_str = gen_binary(i,n)
    sum_v = 0
    sum_w = 0
    for i in range(len(binary_str)):
    	if binary_str[i]=='1':  ##choose
        sum_v += vi[i]
        sum_w +=wi[i]
    if sum_w<w and best_v<sum_v:
      best_v = sum_v
   	return best_v


def gen_binary(i,length):# 高位补0
  result = ''
  while i>0:
    result = str(i%2)+result
    i = i // 2
  if len(result) > length:
    raise ValueError("data error ")
    
  for i in range(length - len(result)):
    result = '0'+result

  
```



## 动态规划法

```python
def bag(n,w,vi,wi):
  sum_v = [ [0 for i in range(w+1)] for j in range(n+1) ]  
  ##里面的才是列
  for i in range(1,n+1):
    for j in range(1,w+1):
    	sum_v[i][j] = sum_v[i-1][j]
    	if vi[i-1] + sum_v[i-1][j-wi[i-1]] > sum_v[i][j]: 
        ## 放比不放价值更大
        sum_v[i][j]= vi[i-1] + sum_v[i-1][j-wi[i-1]] 
  return sum_v[n]
```

## 回溯法(DFS)

每个元素有两种可能:选或者不选，在树中分别由1，0表示。

使用递归，在遍历完n个数的时候，判断最终的数是否比最佳价值大，如果比最佳价值大，就把值赋给bestv。

```python
bestV = 0
ans = []
wi = []
vi = []
w = 0
n = 0

def bag(n_, w_, vi_, wi_):
    global bestV, wi, vi, w, n
    wi = wi_
    vi = vi_
    w = w_
    n = n_
    backtrace(0, 0, 0,[])

## 最好把后面几个参数写成全局变量进行使用更方便
def backtrace(i, sum_v, sum_w, result):
    global bestV,ans
    if i >= n:
        if bestV<sum_v:
            bestV = sum_v
            print(result)
            ans = result
        return
    if wi[i] + sum_w > w:
        backtrace(i+1,sum_v,sum_w,result)
    else:  ##这个ifelse可以合并
        result.append(i)
        ### 注意如果要递归传递数组两次不能直接传过去，要copy
        backtrace(i + 1, sum_v+vi[i], sum_w+wi[i], result.copy())
        result.pop()
        backtrace(i+1,sum_v,sum_w,result)
```

更好的代码

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

## 分支限界法（BFS）

​     分支限界法类似于[回溯法](http://www.cnblogs.com/ttltry-air/archive/2012/07/31/2617137.html)，也是在问题的解空间上搜索问题解的算法。一般情况下，分支限界法与回溯法的求解目标不同。回溯法的求解目标是找出解空间中满足约束条件的所有解；而分支限界法的求解目标则是找出满足约束条件的一个解，或是在满足约束条件的解中找出使某一目标函数值达到极大或极小的解，即在某种意义下的最优解。

​      由于求解目标不同，导致分支限界法与回溯法对解空间的搜索方式也不相同。回溯法以深度优先的方式搜索解空间，而分支限界法则以广度优先或以最小耗费优先的方式搜索解空间。

​      分支限界法的搜索策略是，在扩展结点处，先生成其所有的儿子结点(分支)，然后再从当前的活结点表中选择下一扩展结点。为了有效地选择下一扩展结点，加速搜索的进程，在每一个活结点处，计算一个函数值(限界)，并根据函数值，从当前活结点表中选择一个最有利的结点作为扩展结点，使搜索朝着解空间上有最优解的分支推进，以便尽快地找出一个最优解。这种方式称为分支限界法。人们已经用分支限界法解决了大量离散最优化的问题。

分支限界法首先确定一个合理的限界函数，并根据限界函数确定目标函数的界[down, up]；然后按照[广度优先策略](https://baike.baidu.com/item/%E5%B9%BF%E5%BA%A6%E4%BC%98%E5%85%88%E7%AD%96%E7%95%A5/9028521)[遍历](https://baike.baidu.com/item/%E9%81%8D%E5%8E%86/9796023)问题的解空间树，在某一分支上，依次搜索该结点的所有孩子结点，分别估算这些孩子结点的目标函数的可能取值（对最小化问题，估算结点的down，对最大化问题，估算结点的up）。如果某孩子结点的目标函数值超出目标函数的界，则将其丢弃（从此结点生成的解不会比如今已得的更好），否则入待处理表 [1]  。



**常见的两种分支限界法**

1. 队列式（FIFO）分支限界法

   按照先进先出原则选取下一个节点为扩展节点。 活结点表是先进先出队列。

   LIFO分支限界法：活结点表是堆栈。

2. LC（least cost）分支限界法（优先队列式分支限界法）：按照优先队列中规定的优先级选取优先级最高的节点成为当前扩展节点。 活结点表是优先权队列，LC分支限界法将选取具有最高优先级的活结点出队列，成为新的E-结点。

​     **FIFO分支限界法搜索策略：**

一开始，根结点是唯一的活结点，根结点入队。

从活结点队中取出根结点后，作为当前扩展结点。

对当前扩展结点，先从左到右地产生它的所有儿子，用约束条件检查，把所有满足约束函数的儿子加入活结点队列中。

再从活结点表中取出队首结点（队中最先进来的结点）为当前扩展结点，……，直到找到一个解或活结点队列为空为止。

**优先队列式分支限界法搜索策略：**

对每一活结点计算一个优先级（某些信息的函数值）；

根据这些优先级从当前活结点表中优先选择一个优先级最高（最有利）的结点作为扩展结点，使搜索朝着解空间树上有最优解的分支推进，以便尽快地找出一个最优解。

再从活结点表中下一个优先级别最高的结点为当前扩展结点，……，直到找到一个解或活结点队列为空为止。

在背包问题中

- **FIFO限界：**若当前分支的“装载的价值上界”，比现有的最大装载的价值小，则该分支就无需继续搜索。（剩余的物品最有可能能取到的最大价值）
- **优先队列限界：**优先队列搜索得到当前最优解作为一个“界”，对上界（或下界）不可能达到（大于）这个界的分支则不去进行搜索，这样就缩小搜索范围，提高了搜索效率。

​      算法首先检查当前扩展结点的左儿子结点的可行性。如果该左儿子结点是可行结点，则将它加入到子集树和活结点优先队列中。当前扩展结点的右儿子结点一定是可行结点，仅当右儿子结点满足上界约束时才将它加入子集树和活结点优先队列。当扩展到叶节点时为问题的最优值。

<https://blog.csdn.net/XlxfyzsFdblj/article/details/84840720>

```
   1: import java.util.Stack;
   2:  
   3: class HeapNode {
   4:     double upbound; // 结点的价值上界
   5:     double value; // 结点所对应的价值
   6:     double weight; // 结点所相应的重量
   7:  
   8:     int level; // 活节点在子集树中所处的层序号
   9:  
  10:     public HeapNode() {
  11:     }
  12: }
  13:  
  14: // 分支限界法实现01背包问题
  15: public class BB_Knapsack01 {
  16:  
  17:     int[] weight;
  18:     int[] value;
  19:     int max; // 背包的最大承重量
  20:  
  21:     int n;
  22:  
  23:     double c_weight; // 当前背包重量
  24:     double c_value; // 当前背包价值
  25:  
  26:     double bestv; // 最优的背包价值
  27:  
  28:     Stack<HeapNode> heap;
  29:  
  30:     public BB_Knapsack01() {        
  31:         weight = new int[] { 15, 16, 15, 0 };
  32:         value = new int[] { 25, 45, 25, 0 };
  33:         max = 30;
  34:  
  35:         n = weight.length - 1;
  36:  
  37:         c_weight = 0;
  38:         c_value = 0;
  39:         bestv = 0;
  40:  
  41:         heap = new Stack<HeapNode>();
  42:     }
  43:  
  44:     // 求子树的最大上界
  45:     private double maxBound(int t) {
  46:         double left = max - c_weight;
  47:         double bound = c_value;
  48:         // 剩余容量和价值上界
  49:         while (t < n && weight[t] <= left) {
  50:             left -= weight[t];
  51:             bound += value[t];
  52:             t++;
  53:         }
  54:         if (t < n)
  55:             bound += (value[t] / weight[t]) * left; // 装填剩余容量装满背包        
  56:         return bound;
  57:     }
  58:  
  59:     // 将一个新的活结点插入到子集树和最大堆heap中
  60:     private void addLiveNode(double upper, double cvalue, double cweight,
  61:             int level) {
  62:         HeapNode node = new HeapNode();
  63:         node.upbound = upper;
  64:         node.value = cvalue;
  65:         node.weight = cweight;
  66:         node.level = level;
  67:         if (level <= n)
  68:             heap.push(node);
  69:     }
  70:  
  71:     // 利用分支限界法，返回最大价值bestv
  72:     private double knapsack() {
  73:         int i = 0;
  74:         double upbound = maxBound(i);
  75:         // 调用maxBound求出价值上界，bestv为最优值
  76:         while (true) // 非叶子结点        
  77:         {
  78:             double wt = c_weight + weight[i];
  79:             if (wt <= max)// 左儿子结点为可行结点            
  80:             {
  81:                 if (c_value + value[i] > bestv)
  82:                     bestv = c_value + value[i];
  83:                 addLiveNode(upbound, c_value + value[i], c_weight + weight[i],
  84:                         i + 1);
  85:             }
  86:             upbound = maxBound(i + 1);
  87:             if (upbound >= bestv) // 右子树可能含最优解
  88:                 addLiveNode(upbound, c_value, c_weight, i + 1);
  89:             if (heap.empty())
  90:                 return bestv;
  91:             HeapNode node = heap.peek();
  92:             // 取下一扩展结点
  93:             heap.pop();
  94:             //System.out.println(node.value + " ");
  95:             c_weight = node.weight;
  96:             c_value = node.value;
  97:             upbound = node.upbound;
  98:             i = node.level;
  99:         }
 100:     }
 101:  
 102:     public static void main(String[] args) {
 103:         // TODO Auto-generated method stub
 104:         BB_Knapsack01 knap = new BB_Knapsack01();
 105:         double opt_value = knap.knapsack();
 106:         System.out.println(opt_value);
 107:     }
 108: }
```

## 贪心法

01背包问题不可以用贪心法求解，因为贪心法要求整个问题的最优解一定由在贪心策略中存在的子问题的最优解得来的。
对于01背包，贪心策略：选取价值最大者是不正确的

# 完全背包

完全背包问题是指每种物品都有无限件，所以对于某一件物品也不再是拿（1）不拿（0）。而是变为了拿0件，1件，2件...k件,按照0-1背包问题的状态转移方程同样可以写出完全背包的状态转移方程：

f[i] [j] = max(f[i-1] [j-k*weight[i]] +ｋ＊value[i])   其中  0<=k<= j / weight[i]（j是当前重量）

于是可以把第 i 种物品转化为 W/w[i]件费用及价值均不变的物品，然后求解这个 0-1 背包问题。

当然 可以优化 使用一维数组

for i = 1......n

forj = 1.....m

f[j] = max(f[j],f[j-weight[i]] + value[i])

<https://www.cnblogs.com/zpfbuaa/p/4966335.html>

<https://www.jianshu.com/p/7a4e6071bc02. 这个ok

```python
   for i=1..N
       for w=0..W
f[w]=max{f[w],f[w-cost]+weight}
```

```python
def solve3(vlist,wlist,totalWeight,totalLength):
    """完全背包问题"""
    resArr = np.zeros((totalWeight)+1,dtype=np.int32)
    for i in range(1,totalLength+1):
        for j in range(1,totalWeight+1):
            if wlist[i] <= j:
                resArr[j] = max(resArr[j],resArr[j-wlist[i]]+vlist[i])
    return resArr[-1]

if __name__ == '__main__':
    v = [0,60,100,120]
    w = [0,10,20,30]
    weight = 50
    n = 3
    result = solve3(v,w,weight,n)
    print(result)
```



```python
def CompletePack(N, V, weight, value):
    """
    完全背包问题(每个物品可以取无限次)
    :param N: 物品个数, 如 N=5
    :param V: 背包总容量, 如V=15
    :param weight: 每个物品的容量数组表示, 如weight=[5,4,7,2,6]
    :param value: 每个物品的价值数组表示, 如value=[12,3,10,3,6]
    :return: 返回最大的总价值
    """
    #初始化f[N+1][V+1]为0，f[i][j]表示前i件物品恰放入一个容量为j的背包可以获得的最大价值
    f = [[0 for col in range(V + 1)] for row in range(N + 1)]
    for i in range(1, N+1):
      for j in range(1, V+1):
          # 注意由于weight、value数组下标从0开始，第i个物品的容量为weight[i-1],价值为value[i-1]
          # V/weight[i-1]表示物品i最多可以取多少次
          f[i][j] = f[i - 1][j]  # 初始取k=0为最大，下面的循环是把取了k个物品i能获得的最大价值赋值给f[i][j]
          for k in range(j/weight[i-1]+1):
              if f[i][j] < f[i-1][j-k*weight[i-1]]+k*value[i-1]:
                  f[i][j] = f[i-1][j-k*weight[i-1]]+k*value[i-1]  # 状态方程

          # 上面的f[i][j]也可以通过下面一行代码求得
          #  f[i][j] = max([f[i-1][j-k*weight[i-1]]+k*value[i-1] for k in range(j/weight[i-1]+1)])
  max_value = f[N][V]
  return max_value
```
# 多重背包

多重背包问题限定了一种物品的个数，解决多重背包问题，只需要把它转化为0-1背包问题即可。比如，有2件价值为5，重量为2的同一物品，我们就可以分为物品a和物品b，a和b的价值都为5，重量都为2，但我们把它们视作不同的物品。

多重背包是每个物品有不同的个数限制，如第i个物品个数为num[i]。 
同样可以用f[i][j]表示前i间物品恰放入一个容器为j的背包可以获得的最大价值，且每个物品数量不超多num[i]。则其状态转移方程为： 
f[i] [j] = max{f[i-1] [j-k * weight[i]]+k * value[i]} ,其中(0<=k<=min{j/weight[i], num[i]})

# 部分背包

## 贪心法

何谓贪心法，只要你够贪心，就能领略贪心算法之精髓。

部分背包问题和0/1背包问题的区别就是：部分背包问题中的单个物品，可以取一部分装入背包。而0/1背包问题则是要么全部拿走，要么一无所有（这里引用了LOL卡牌大师的台词）。 那么作为一个so greed的你，肯定应该知道按照什么顺序拿物品的把。没错，看着值钱的先抢！ 这里所说的值钱，指的是单位重量所产生的价值越大（即value/weight的比值越大）。那么问题很简单咯，把"值钱"的东西排在前面，每次拿抢的时候，问问看背包君够不够承受得住，承受的了，就全部抢过来。承受不住，那么只能按照所能承受的重量，取物品的一部分了。当然价值也得按照比例来哦~

**部分背包问题的贪心策略的正确性证明**

贪心策略是：**总是优先选择单位重量下价值最大的物品**

正确性证明 是：使用该贪心策略，可以获得最优解。在这里，最优解就是带走的物品价值最大。

**证明思路：先考察一个全局最优解，然后对该解加以修改(一般是采用“剪枝”技巧)，使其采用贪心选择，这个选择将原问题变成一个相似的、但是更小的问题。**

先假设 物品集合S={W1，W2....Wn}已经按 单位重量价值从小到大排好序了。

并假设 一个全局最优解是：S(i)={Wi1，Wi2，.....Win}。Wi1，Wi2，.....Win是有序的。对于贪心选择而言，总是会优先 选择 Wn 的物品，当Wn 没有后，再选择Wn-1 .....

如果Win = Wn 问题已经得证。因为，我们的最优解S(i)中，已经包含了贪心选择。只要继续归纳下去，Wi(n-1) 就是 Wn-1 ....

如果Win != Wn 运用剪枝技巧，剪掉Win 并 贴上 Wn  此时，**得到的是一个更优的解(因为价值更大了 ，Wn > Win)**。因为，Wn 是单位重量价值最高的那个物品啊，我们的贪心选择**应该**选择它，但是这里的最优解S(i)**却没有选择它**，于是我们用剪枝技巧，将它加入到S(i)中去，并把S(i)中的Win除去。  

这就证明了，如果用贪心策略来进行选择，得到的是最优解。从而证明了贪心算法的正确性。

其实，也就是证明了一定存在一个最优解，这个最优解就是由贪心选择组成的。





问题描述：假设有n个物体C1,n分别标记为：1, 2, …, n。其价值分别为：V1, V2,…, Vn，重量分别为：W1， W2， …, Wn。背包的容量为W。则部分背包问题可以描述为：存

在一个n元向量（X1， X2， …, Xn），在 的条件（记为条件1）下，背包的总价值 最大（其中0≤Xi≤1）。假设C1,n的标号是

按照单位价值Vi/Wi从大到小排好序的，即：V1/W1 >  V2/W2  >  …  >  Vn/Wn。如果不是，则对C1,n重新编号即可。

于是部分背包问题的贪心选择性质可以描述为：每次从Ci,j中选择物品，都是优先考虑选择物品i，且在满足条件1的情况下，Xi 越接近1越好。下面用数学归纳法证明这一贪心选择性质：

记Ai,j为从物品Ci,j中选择装进背包的最优解，则原问题为求A1,n。再记k为第k次从C中选物品进背包。则：

Ⅰ）当k=1时，满足贪心选择性质，即第一次选物品p进背包，且p=1。下面用反证法证明：

若p≠1，则p≥2。但在此情况下不能保证A1,n最优。试考虑W1=W的情况下，另外一个解A1,n’={1}的价值V’更大（因为V1/W1 > V2/W2 > …> Vn/Wn）。既A1,n不是最优解，产生矛盾。所以p=1。

Ⅱ）在满足条件1的情况下，假设k≤z时，满足贪心选择性质。既前z（包括z）次从Cz,n中选择物品，都是优先考虑选择物品z，且在满足条件1的情况下，Xi 越接近1越好。

Ⅲ）在满足条件1的情况下，当k=z+1时，证明也满足贪心选择性质，既第k=z+1次选物品（z+1）。

先证明A1,n的子问题Az+1,n也具有最优性质：如果存在Cz+1,n中选择物品的子问题的解Az+1,n’的总价值比Az+1,n的总价值更大，那么Az+1,n’与A1,z合并后的原问题的解A1,n’的总价值比A1,n的总价值更大。这与A1,n是最优解矛盾。所以Az+1,n也具有最优性质。

于是第k=z+1次选择物品等价于子问题Az+1,n的第一次选择物品，又因为在Ⅱ）假设成立的情况下，C1,n的前z个物品已经被选了，所以转换成Az+1,n从Cz+1,n中选择第一个物品。根据Ⅰ），显然优先选择物品（z+1）。所以结论得证。

∴ 综合Ⅰ）Ⅱ）Ⅲ），得证部分背包问题具有最优选择性质。



代码

```python

```

