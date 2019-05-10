---
title: 数据结构之图
date: 2019-03-11 23:51:44
tags:
- Data Structure
---

## 图

 **图**是一种多对多的**数据结构**

- 图的表示： `邻接表` 和 `邻接矩阵`
  - 这里可以分为 `有向图` 和`无向图`
    `无向图是一种特殊的有向图`
  - `有权图` 和 `无权图`
- 图的遍历： `DFS` `BFS` 常见可以解决的问题有： `联通分量` `Flood Fill` `寻路` `走迷宫` `迷宫生成` `无权图的最短路径` `环的判断`
- 最小生成树问题（Minimum Spanning Tree） `Prim` `Kruskal`
- 最短路径问题(Shortest Path) `Dijkstra` `Bellman-Ford`
- 拓扑排序(Topological sorting)

<!--more-->

## 图的应用

- 计算机网络
- 路径问题，寻找最优路径
- 地图
- 约束性满足问题

## Java 中的 图实现

图不应该定义为数据结构。 图实际可以理解为模型。比如最短路径，最大流，最小生成树等等。

图的表示和存储才是 数据结构，实际上，数组 和 邻接表就可以构图，而且图算法的应用实际是不广的。 大部分程序员一辈子都用不上图算法，于是 JDK 就没有 图都支持了。 

但是 C++ 有标准 GraphLib 库可以使用。

Java 版本也有开源图算法，好像没有像 GraphLib 那么强悍。

## Python中的图实现

## 实现有向图 无向图 有权图 无权图的邻接矩阵和邻接表表示方法

```python
class Graph(object):
    def __init__(self,v,graph=None):
        if graph :
            self.graph = graph
        else:
            self.graph = [[0 for i in range(v)] for j in range(v)]
        self.v = v
    

    def add_edge(self,begin,end,weight):
        if begin>self.v-1 or end < 0:
            print("edge error")
            return
        self.graph[begin][end] = weight

    def show_graph_matrix(self):

        for i in range(self.v):
            for j in range(self.v):
                print(self.graph[i][j],end=" ")
            print()

    def dfs_recursive(self,now,visited,result):  # 深度优先搜索
        visited[now] = True
        result.append(now)
        for i in range(self.v):
            if self.graph[now][i] != 0 and not visited[i]:
                self.dfs_recursive(i,visited,result)


    def dfs(self,start):
        visited = [False for i in range(self.v)]
        result = []
        self.dfs_recursive(start, visited, result)
        # for i in range(self.v):
        #     if not visited[i]:
        #         self.dfs_recursive(i,visited,result)
        return result

    # 初始化栈
    # 输出起始顶点，起始顶点改为“已访问”标志，将起始顶点进栈
    # 重复以下操作直至栈空：
    # 去栈顶元素顶点，找到未被访问的邻接结点W
    # 输出W，W改为“已访问”，将W进栈
    # 否则当前顶点退栈

    def dfs_by_stack(self,start):
        stack = [start]
        result = []
        while stack:
            now = stack.pop()
            result.append(now)
            for i in range(self.v):
                if self.graph[now][i]!= 0:
                    stack.append(self.graph[now][i])
        return  result


if __name__ == '__main__':
    matrix=[[0,0,1,1,1],[0,0,1,0,1],[1,1,0,0,1],[1,0,0,0,0],[1,1,1,0,0]]
    graph = Graph(5,matrix)

    # graph.add_edge(0,1,1)
    #
    # graph.add_edge(0, 1, 1)
    # graph.add_edge(0, 2, 1)
    # graph.add_edge(2, 3, 1)
    # graph.add_edge(3, 1, 1)
    graph.show_graph_matrix()
    print(graph.dfs(0))
    print(graph.dfs_by_stack(0))
```

## 实现图的深度优先搜索和广度优先搜索

深度优先算法：

（1）访问初始顶点v并标记顶点v已访问。
（2）查找顶点v的第一个邻接顶点w。
（3）若顶点v的邻接顶点w存在，则继续执行；否则回溯到v，再找v的另外一个未访问过的邻接点。
（4）若顶点w尚未被访问，则访问顶点w并标记顶点w为已访问。
（5）继续查找顶点w的下一个邻接顶点wi，如果v取值wi转到步骤（3）。直到连通图中所有顶点全部访问过为止。

广度优先算法：

（1）顶点v入队列。
（2）当队列非空时则继续执行，否则算法结束。
（3）出队列取得队头顶点v；访问顶点v并标记顶点v已被访问。
（4）查找顶点v的第一个邻接顶点col。
（5）若v的邻接顶点col未被访问过的，则col入队列。
（6）继续查找顶点v的另一个新的邻接顶点col，转到步骤（5）。直到顶点v的所有未被访问过的邻接点处理完。转到步骤（2）。

广度优先搜索在搜索访问一层时，需要记住已被访问的顶点，以便在访问下层顶点时，从已被访问的顶点出发搜索访问其邻接点。所以在广度优先搜索中需要设置一个队列Queue，使已被访问的顶点顺序由队尾进入队列。在搜索访问下层顶点时，先从队首取出一个已被访问的上层顶点，再从该顶点出发搜索访问它的各个邻接点。

```python
    def dfs(self,start):
        visited = [False for i in range(self.v)]
        result = []
        self.dfs_recursive(start, visited, result)
        for i in range(self.v):
            if not visited[i]:
                self.dfs_recursive(i,visited,result)
        return result

    # 初始化栈
    # 输出起始顶点，起始顶点改为“已访问”标志，将起始顶点进栈
    # 重复以下操作直至栈空：
    # 去栈顶元素顶点，找到未被访问的邻接结点W
    # 输出W，W改为“已访问”，将W进栈
    # 否则当前顶点退栈

    def dfs_by_stack(self,start):
        visited = [False for i in range(self.v)]

        stack = [start]
        result = []
        while stack:
            # print(stack)
            now = stack.pop()
            if not visited[now]:
                result.append(now)
                visited[now] = True
                for i in range(self.v):
                    if (not visited[i]) and self.graph[now][i]!= 0:#若当前邻接顶点没有被访问过，则进行访问并入栈
                        stack.append(i)
                    else:
                        # 若当前邻接顶点已经被访问过，则沿边找到下一个顶点
                        pass
                # 若某一方向被访问完，则回溯寻找未被访问的顶点

        return  result


    def bfs(self,start):
        queue = [start]
        result = []
        visited = [False for i in range(self.v)]

        while queue:
            now = queue.pop(0)
            if not visited[now]:
                visited[now] = True
                result.append(now)
            for i in range(self.v):
                if self.graph[now][i]!= 0 and  visited[i] is False:
                    queue.append(i)
        return result
```



## 实现 Dijkstra 算法、A*算法

```python
#find_lowest_cost_node(costs): 返回开销最低的点
def find_lowest_cost_node(costs):
    lowest_cost = float("inf")
    lowest_cost_node = None
    for node in costs:
        cost = costs[node]
        if cost < lowest_cost and node not in processed:
            lowest_cost = cost
            lowest_cost_node = node
    return lowest_cost_node


#Dijkstra implement
node = find_lowest_cost_node(costs) 
while node is not None:
    cost = costs[node] 
    neighbors = graph[node]
    for n in neighbors.keys():
        new_cost = cost + neighbors[n]
        if costs[n] > new_cost:
            costs[n] = new_cost
            parents[n] = node
    processed.append(node)
    node = find_lowest_cost_node(costs)
 
print(processed)
```

### 写了java版的最短路径算法

```java
package sample;

import java.util.*;

public class Graph {

    private  List<Vertex> vlist = new ArrayList<>();
    int[][] graph ;
    public Graph(int[][]  graph,char[] vertexs){
        this.graph = graph;
        for(int i = 0;i< vertexs.length;i++){
            this.vlist.add(new Vertex(i,vertexs[i]));
        }

    }
    private Vertex findVertexByChar(char v){
        for(Vertex i:vlist){
            if(i.info == v){
                return i;
            }
        }
        return null;
    }
    private Vertex findVertexName(int index){
        for(Vertex i:vlist){
            if(i.no == index){
                return i;
            }
        }
        return null;
    }

    public void dij(char start){

         Vertex current = findVertexByChar(start);
         if(current == null){
             System.out.println("no this point");
             return;
         }
         List<Vertex> notProcessed = new ArrayList<>() ;

         int[] result = new int[vlist.size()];
         String[] path = new String[vlist.size()];

         for(int i = 0;i<vlist.size();i++){
             result[i] = 0;
             path[i] = "";
             if(vlist.get(i).info!=start){
                 result[i] =Integer.MAX_VALUE;
                 notProcessed.add(vlist.get(i));
             }
         }

          // 两个集合，已经处理过的和未处理的

         while(!notProcessed.isEmpty()){
             for(int i= 0;i<vlist.size();i++){
                 if(graph[current.no][i]>0 && graph[current.no][i]+result[current.no]<result[i]){
                     result[i] = graph[current.no][i]+result[current.no];
                     path[i] = path[i].replace("->"+findVertexName(i).info,"");
                     path[i] += ("->"+findVertexName(current.no).info+"->"+findVertexName(i).info);

                 }
             }
             current =notProcessed.remove(0);

         }


        for(int i = 0;i<vlist.size();i++){
            System.out.println(path[i]+"    "+ result[i]);
        }


    }

    private class Vertex {
        int no;
        char info;

        public Vertex(int no, char info) {
            this.no = no;
            this.info = info;
        }
    }
    public static void main(String[] args) {
        int[][] graph = {
                {0,5,4,8},
                {0,0,6,0},
                {0,0,0,3},
                {0,0,0,0}};
        char[] v= {'a','b','c','d'};
        Graph g = new Graph(graph,v);
        g.dij('a');
    }
}

```



关于A*算法的详细解释我是看的这篇文章

http://www.cppblog.com/christanxw/archive/2006/04/07/5126.html

时间仓促没整明白，后面再自己实现吧。

## 实现拓扑排序的Kahn算法、DFS算法

```python
Kahn算法：
将所有入度为0的顶点加入队列q；
	while（!q.empty() )
	{
		u = q.front();
		q.pop();
		list.push(u);
		for (u的每个邻接点v)
		{
			删除边(u, v)；
			if (indegree(v) == 0)
				q.push(v);
		}
	}
	if (图G还有边存在)
		return 存在环
	else
		return list;
    以上这种是比较典型的求拓扑排序的算法，算法复杂度为O(v+e)，常可用来判断该图是否是DAG（有向无环图）
    在算法导论上，介绍的则是另外一种算法，它是基于DFS的，实现十分简单，仅需要在DFS中多加一个语句即可。
    基于DFS的拓扑排序算法（前提：图是DAG）：
L ← 用于存放排序结果的数组
S ← 出度为0的顶点的集合
for (S中的每个顶点)
    dfs(n) 
void dfs(node n)
{
	if (!vis[n])
	{
		vis[n] = true;
		for (每一个顶点m，满足m->n)
			dfs(m);
	}
        L.push(n);
}
```

 可以看到，我们只是在dfs函数快退出时将结点加入到L中而已。（注意，for中的顶点m，满足的是m->n而不是n->m）

下面简单证明一下它的正确性：
对任意的边m->n，当调用dfs(n)的时候，有如下两种情况：
1) dfs(m)还没有被调用，此时会调用dfs(m)，只有dfs(m)返回之后，dfs(n)才会返回

2) dfs(m)已经被调用过并返回了

（由于本图是DAG，所以不存在dfs(m)已经被调用，但是在dfs(n)在被调用时还未返回的情况）

无论是以上哪一种情况，m都会先于n被添加到L中。所以对于任意边m->n，在L中，m总会在n前面。

本算法的复杂度为O(v+e)，需要注意的一些点是，本算法是建立在图为DAG的基础上的，当然，可以进行一些修改来做环路检测，另外， 本算法的起点是对每个出度为0的顶点进行dfs，而Kahn算法则是从每个入度为0的顶点出发。为何本算法需要从出度为0的顶点出发呢？因为出度为0的顶点必然排在最后面，而最先调用dfs的顶点最后才加入L中。

对比两种算法，有着异曲同工之妙，一个从入度为0的顶点出发，一个从出度为0的顶点出发；一个对m->n中的每个n进行操作，一个对m->n中的每个m进行操作。

如果需要判断该图是否为DAG，那么第一种算法是不错的选择，如果已经知道该图为DAG，则第二种算法更加简洁！

## leetcode 练习

Number of Islands（岛屿的个数）
英文版：[Loading...](https://leetcode.com/problems/number-of-islands/description/)
中文版：[力扣](https://leetcode-cn.com/problems/number-of-islands/description/)

```python
class Solution(object):
    
    LAND = "1"
    WATER = "0"
    
    def numIslands(self, grid):
        """
        :type grid: List[List[str]]
        :rtype: int
        """
        if not grid or not grid[0]:
            return 0      
        
        island_num = 0
        
        row = len(grid)
        col = len(grid[0])
        for i in range(row):
            for j in range(col):
                if grid[i][j] == self.LAND:
                    island_num += 1
                    self.bfs(grid, i, j)
                    
        return island_num
    
    def bfs(self, grid, x, y):
        queue = [(x, y)]
        visited = set()
        visited.add((x, y))
        while queue:
            point = queue.pop(0)
            x_val, y_val = point
            grid[x_val][y_val] = self.WATER
            for neighbor_land in self.find_neighbor_lands(grid, x_val, y_val):
                if neighbor_land not in visited:
                    visited.add(neighbor_land)
                    queue.append(neighbor_land)
                
    def find_neighbor_lands(self, grid, x, y):
        neighbor_lands = []
        deltaX = [1, 0, 0, -1]
        deltaY = [0, 1, -1, 0]
        for i in range(4):
            neighbor_x = x + deltaX[i]
            neighbor_y = y + deltaY[i]
            if not self.in_bound(grid, neighbor_x, neighbor_y):
                continue
            if grid[neighbor_x][neighbor_y] == self.LAND:
                neighbor_lands.append((neighbor_x, neighbor_y))
        return neighbor_lands
        
    def in_bound(self, grid, x, y):
        row = len(grid)
        col = len(grid[0])
        return 0 <= x < row and 0 <= y < col
```

Valid Sudoku（有效的数独）
英文版：[Loading...](https://leetcode.com/problems/valid-sudoku/)
中文版：[力扣](https://leetcode-cn.com/problems/valid-sudoku/)

```python
class Solution:
    def isValidSudoku(self, board):
        """
        :type board: List[List[str]]
        :rtype: bool
        """
        # init data
        rows = [{} for i in range(9)]
        columns = [{} for i in range(9)]
        boxes = [{} for i in range(9)]

        # validate a board
        for i in range(9):
            for j in range(9):
                num = board[i][j]
                if num != '.':
                    num = int(num)
                    box_index = (i // 3 ) * 3 + j // 3
                    
                    # keep the current cell value
                    rows[i][num] = rows[i].get(num, 0) + 1
                    columns[j][num] = columns[j].get(num, 0) + 1
                    boxes[box_index][num] = boxes[box_index].get(num, 0) + 1
                    
                    # check if this value has been already seen before
                    if rows[i][num] > 1 or columns[j][num] > 1 or boxes[box_index][num] > 1:
                        return False         
        return True
```

官方思路
一个简单的解决方案是遍历该 9 x 9 数独 三 次，以确保：

行中没有重复的数字。
列中没有重复的数字。
3 x 3 子数独内没有重复的数字。
实际上，所有这一切都可以在一次迭代中完成。

方法：一次迭代
首先，让我们来讨论下面两个问题：

如何枚举子数独？
可以使用 box_index = (row / 3) * 3 + columns / 3，其中 / 是整数除法。

如何确保行 / 列 / 子数独中没有重复项？
可以利用 value -> count 哈希映射来跟踪所有已经遇到的值。

现在，我们完成了这个算法的所有准备工作：

遍历数独。
检查看到每个单元格值是否已经在当前的行 / 列 / 子数独中出现过：
如果出现重复，返回 false。
如果没有，则保留此值以进行进一步跟踪。
返回 true。

复杂度分析

时间复杂度：O(1)，因为我们只对 81 个单元格进行了一次迭代。(事实上，我认为官方思路中的复杂度和三次迭代没有任何区别，因为每一次循环中的有效计算都是三次）
空间复杂度：O(1)。

## 回溯法

回溯法(探索与回溯法)是一种选优搜索法，按选优条件向前搜索，以达到目标。但当探索到某一步时，发现原先选择并不优或达不到目标，就退回一步重新选择，这种走不通就退回再走的技术为回溯法，而满足回溯条件的某个状态的点称为“回溯点”。深度优先算法就是一种回溯法

有关回溯法的练习

```python
#####分配问题
def work( i , count , res_temp):
    global cost
    res_temp_copy = res_temp.copy()
    # print('result是：', result, 'cost是：', cost)
    if i > n :
        # global result
        if count == cost:
            # print('此时的res_temp是：', res_temp_copy, 'count是：', count)
            if res_temp_copy not in result:
                result.append(res_temp_copy)
            # print('count==cost的 result',  res)
        if count < cost:
            # print('此时的res_temp是：aaaa   ', res_temp_copy, 'count是：', count)
            result.clear()
            result.append(res_temp_copy)
            # print('count<cost的result ', res)
            cost = count
    if count <= cost:
        for j in range(1,n+1):
            if x[j] == 0 :
                res_temp.append(j)
                x[j] = 1
                work(  i + 1 , count+costMatrix[i][j] , res_temp)
                res_temp.pop()
                x[j] = 0
```

## 参考

https://blog.csdn.net/jiange_zh/article/details/48183267

https://www.cnblogs.com/chuninggao/p/7301082.html