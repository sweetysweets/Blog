---
title: 数据结构之树
date: 2019-03-09 11:32:19
tags:
- Data Structure
---

# 二叉树

二叉树是每个节点最多有两个子树的树结构。通常子树被称作“左子树”和“右子树”。二叉树常被用于实现二叉查找树和二叉堆。

二叉树的每个结点至多只有二棵子树(不存在度大于2的结点)，二叉树的子树有左右之分，次序不能颠倒。

<!--more-->

## Java实现

Java没有自己的二叉树类，不像队列和栈，而是让程序员自己去定义，只有TreeSet TreeMap，为什么没有基本的Tree呢？【树的逻辑非常多，二叉树，多叉树，红黑树，哈夫曼树，平衡树等等。。。这些逻辑是要自己去思考和构造的，就跟JAVA不可能有一个类直接帮你实现一个ERP，CRM系统一样。】

## Python实现

[一行Python代码实现树结构](http://codingpy.com/article/one-line-tree-in-python/)

```python
from collections import defaultdict
def tree(): 
    return defaultdict(tree)   #一棵树就是一个默认值也为树的字典。
users = tree()
users['codingpy']['username'] = 'earlgrey'
users['python']['username'] = 'Guido van Rossum'
```

## 实现一个二叉查找树，并且支持插入、删除、查找操作

二叉查找树（Binary Search Tree），又称为二叉搜索树、二叉排序树。其或者是一棵空树；或者是具有以下性质的二叉树：

若左子树不空，则左子树上所有结点的值均小于或等于它的根结点的值
若右子树不空，则右子树上所有结点的值均大于或等于它的根结点的值
左、右子树也分别为二叉排序树 

感想：树中有非常多的递归方法，写类的内部代码最好带个root节点进去，删除操作非常麻烦，回头可以注意一下。

```python
class Node(object):
    def __init__(self,data):
        self.data = data
        self.left_child = None
        self.right_child = None


class BinaryTree(object):
    def __init__(self,root=None):
        self.root = root

    def find_min(self):
        if self.root is None:
            return None
        tmp = self.root
        while tmp.left_child is not None:
            tmp = tmp.left_child
        return tmp.data

    def find_max(self):
        if self.root is None:
            return None

        tmp = self.root
        while tmp.left_child is not None:
            tmp = tmp.left_child
        return tmp.data

    @staticmethod
    def find_min_by_root(root):
        if root is None:
            return None
        tmp = root
        while tmp.right_child is not None:
            tmp = tmp.right_child
        return tmp.data

    def insert(self,data):
        if self.root is None:
            self.root = Node(data)
        tmp = self.root
        while True:
            if tmp.data == data:
                print("data already exists!")
            elif tmp.data > data:
                if tmp.left_child is None:
                    tmp.left_child = Node(data)
                    return
                tmp = tmp.left_child
            else:
                if tmp.right_child is None:
                    tmp.right_child = Node(data)
                    return
                tmp = tmp.right_child

    def search(self,data):
        if self.root is None:
            return None
        current = self.root
        while current is not None:
            if current.data == data:
                return current
            current = current.left_child if current.data > data else current.right_child
        return current  #not find


    # 如果待删除的节点是叶子节点，那么可以立即被删除
    # 如果节点只有一个儿子，则将此节点parent的指针指向此节点的儿子，然后删除节点
    # 如果节点有两个儿子，则将其右子树的最小数据代替此节点的数据，并将其右子树的最小数据删除
    # 想一想中序遍历输出的序列删除某一个值得操作

    def delete_node(self,root,data):
        if root is None:
            return None
        if root.data < data:
           root.right_child = self.delete_node(root.right_child,data)
        elif root.data > data:
            root.left_child =  self.delete_node(root.left_child,data)
        else:  # equal
            if root.left_child and root.right_child:
                root.data  = self.find_min_by_root(root.right_child) # 找到后继结点
                root.right_child = self.delete_node(root.right_child, root.data)  # 实际删除的是这个后继结点
            else:
                if root.left_child is None:
                    root = root.right_child
                elif root.right_child is None:
                    root = root.left_child     
                    ###如果两个都为空 已经包含在这里了
        return root


    def delete(self,data):
        self.delete_node(self.root,data)
```

在网上看到的另一种删除的写法，也写的挺好的，不过要存parent，我更喜欢自己的实现

```python
def find_min(self):   # Gets minimum node (leftmost leaf) in a subtree
    current_node = self
    while current_node.left_child:
        current_node = current_node.left_child
    return current_node

def replace_node_in_parent(self, new_value=None):
    if self.parent:
        if self == self.parent.left_child:
            self.parent.left_child = new_value
        else:
            self.parent.right_child = new_value
    if new_value:
        new_value.parent = self.parent
        
def binary_tree_delete(self, key):
    if key < self.key:
        self.left_child.binary_tree_delete(key)
    elif key > self.key:
        self.right_child.binary_tree_delete(key)
    else: # delete the key here
        if self.left_child and self.right_child: # if both children are present
            successor = self.right_child.find_min()
            self.key = successor.key
            successor.binary_tree_delete(successor.key)
        elif self.left_child:   # if the node has only a *left* child
            self.replace_node_in_parent(self.left_child)
        elif self.right_child:  # if the node has only a *right* child
            self.replace_node_in_parent(self.right_child)
        else: # this node has no children
            self.replace_node_in_parent(None)
```

## 实现查找二叉查找树中某个节点的后继、前驱节点

查找前驱步骤：先判断x是否有左子树，如果有则在left[x]中查找关键字最大的结点，即是x的前驱。如果没有左子树，则从x继续向上执行此操作，直到遇到某个结点是其父节点的右孩子结点，**此时该父节点就是前驱**

算法导论中给出了详细的求前驱结点和后继节点的算法，但是其中的节点数据结构包含了指向父亲节点的指针，但是一般的给出的节点不包含父亲指针，这就加大了就前驱节点和后继节点的难度。

在不含父指针的节点数据结构下时间复杂度为O(lgN)的求前驱后继结点的算法：

**前驱节点**
若一个节点有左子树，那么该节点的前驱节点是其左子树中val值最大的节点（也就是左子树中所谓的rightMostNode）
若一个节点没有左子树，那么判断该节点和其父节点的关系 
2.1 若该节点是其父节点的右边孩子，那么该节点的前驱结点即为其父节点。 
2.2 若该节点是其父节点的左边孩子，那么需要沿着其父亲节点一直向树的顶端寻找，直到找到一个节点P，P节点是其父节点Q的右边孩子，那么Q就是该节点的前驱节点
类似，我么可以得到求后继节点的规则。

**后继节点**
若一个节点有右子树，那么该节点的后继节点是其右子树中val值最小的节点（也就是右子树中所谓的leftMostNode）
若一个节点没有右子树，那么判断该节点和其父节点的关系 
2.1 若该节点是其父节点的左边孩子，那么该节点的后继结点即为其父节点 

2.2 若该节点是其父节点的右边孩子，那么需要沿着其父亲节点一直向树的顶端寻找，直到找到一个节点P，P节点是其父节点Q的左边孩子（可参考例子2的前驱结点是1），那么Q就是该节点的后继节点

```python
def find_max_by_root(root):
    if root is None:
        return None
    tmp = root
    while tmp.right_child is not None:
        tmp = tmp.right_child
    return tmp

def find_min_by_root(root):
    if root is None:
        return None
    tmp = root
    while tmp.left_child is not None:
        tmp = tmp.left_child
    return tmp

# 规则中我们是从下往上找，但实际代码中是不允许我们这么操作的（由于我们没有父亲指针），
# 我们可以在寻找对应val节点的过程中从上向下找，并且过程中记录下parent节点和firstRParent节点
# （最后一次在查找路径中出现右拐的节点）。
# 实现如下：
def find_pre_node(root,data):
    if root is None:
        return None
    parent = None
    firstRParent = None
    node = None

    while root:
        if root.data == data:
            node = root
            break
        parent = root
        if root.data > data:
            root = root.left_child
        else:
            firstRParent = root
            root = root.right_child

    if node is None:
        return None
    if node.left_child is not None:
        return find_max_by_root(node.left_child)

    if (parent is None) or (parent is not None and firstRParent is None):
        return None  #没有前驱节点的情况
    if node == parent.right_child:# 没有左子树 是其父节点的右边节点
        return parent
    else:   #//没有左子树 是其父节点的左边节点
        return firstRParent


# 同样，求后继节点我们不能从底向上找，也是从上向下找，
# 首先是找到对应val值的节点，顺便把其的parent节点和firstlParent节点（最后一次在查找路径中出现左拐的节点）。
# 实现如下：

def find_post_node(root,data):
    if root is None:
        return None
    parent = None
    firstLParent = None
    node = None
    while root:
        if root.data == data:
            node = root
            break
        parent = root
        if root.data < data:
            root = root.right_child
        else:
            firstLParent = root
            root = root.left_child

    if node is None:
        return None

    if node.right_child is not None:
        return find_min_by_root(root)

    if parent is None or (parent is not None and firstLParent is None):
        return None
    if parent.left_child == node:
        return parent
    else:
        return firstLParent
```

## 实现二叉树前、中、后序以及按层遍历

递归前序、中序、后序和非递归后序、按层遍历是借助队列

```python
def pre_order_traverse(root):
    if root is None:
        return
    print(root.data)
    pre_order_traverse(root.left_child)
    pre_order_traverse(root.right_child)

def in_order_traverse(root):
    if root is None:
        return
    in_order_traverse(root.left_child)
    print(root.data,end=" ")
    in_order_traverse(root.right_child)

def post_order_traverse(root):
    if root is None:
        return
    post_order_traverse(root.left_child)
    post_order_traverse(root.right_child)
    print(root.data)

def post_order_traverse_no_recursion(root):  #借助栈的逆序输出就是后序
    if root is None:
        return
    stack = [root]
    result = []
    while len(stack) > 0:
        current = stack.pop()
        result.append(current.data)
        if current.left_child is not None:
            stack.append(current.left_child)
        if current.right_child is not None:
            stack.append(current.right_child)
    print(result[::-1])
```

```python
def level_traverse(root):
    if not root:
        return
    print("Print binary tree by level")
    queue = [root]
    last = root
    level = 1
    print("Level " + str(level) + ':', end=' ')
    while queue:
        root = queue.pop(0)
        print(root.data, end=' ')
        if root.left_child:
            nlast = root.left_child
            queue.append(root.left_child)
        if root.right_child:
            nlast = root.right_child
            queue.append(root.right_child)
        if root == last and queue:
            last = nlast
            print()
            level += 1
            print("Level " + str(level) + ":", end=' ')


def level_traverse_v2(root):
    if root is None:
        return None
    result = []
    queue = [root]
    while queue:
        number_level = len(queue)
        level_result = []
        while number_level:
            current = queue.pop(0)
            level_result.append(current.data)
            if current.left_child:
                queue.append(current.left_child)
            if current.right_child:
                queue.append(current.right_child)
            number_level -= 1    
            ####注意自己写while循环总是忘记对变量-1或+1
        result.append(level_result)
    return result
```

# 红黑树

## 红黑树特征

红黑树是一棵二叉树， 有五大特征：
特征一： 节点要么是红色，要么是黑色（红黑树名字由来）。
特征二： 根节点是黑色的
特征三： 每个叶节点(nil或空节点)是黑色的。
特征四： 每个红色节点的两个子节点都是黑色的（相连的两个节点不能都是红色的）。
特征五： 从任一个节点到其每个叶子节点的所有路径都是包含相同数量的黑色节点。

从五大特征直观上总结出来几个点：
1 对每个红色节点，子节点只有两种情况：要么都没有，要么都是黑色的。（不然会违反特征四）
2 对黑色节点，如果只有一个子节点，那么这个子节点，必定是红色节点。（不然会违反特征五）
3 假设从根节点到叶子节点中，黑色节点的个数是h, 那么树的高度H范围 h<= H <= 2h（特征四五决定）。

正因为总结的第3点，决定红黑树的查找不会退化到线性查找。查找时间复杂度为O(lgn)。
https://blog.csdn.net/net_wolf_007/article/details/79706498

## 红黑树的操作

### 插入操作

如果插入的是黑色节点时，则每次插入都会违返性质5, 都需要重新调整树。所以 插入时，每次都认为只插入红色节点。这样调整的次数就会减少很多。 倡但是还是有要调整的情况

1 如果插入的是根节点，则直接把点变成黑色（性质二）， 示例中插入第一个节点5的情况
2 如果插入的节点的父节点是黑色节点，则不调整颜色。 示例中 插入点10 就属于这种情况
3 如果插入节点的父节点的红色节点（违反性质四），且父节点的兄弟节点为红色节点。 
    1) 把父节点及其兄弟节点变成黑色，把组父节点变成红色（使其不违反性质五）。 
    2)再检查祖父节点是否违反红黑树的性质（一或四）

4 如果插入节点的父节点的红色节点（违反性质四），且父节点的兄弟节点为黑色节点。  
  并且插入节点，父节点，及祖父节点同侧。 
  即node = node.parent.left && node.parent = node.parent.parent.left(同左则), 
  或 node = node.parent.right && node.parent = node.parent.parent.right（同右则）

处理方法： 把父节点变成黑色节点，把祖父节点变成红色节点， 同时反向旋转祖父节点（同左则，右旋； 同右则左旋）不需要再检查祖父节点，一定满足红黑树定义。

5 如果插入节点的父节点的红色节点（违反性质四），且父节点的兄弟节点为黑色节点。  
  并且插入节点，父节点，及祖父节点不同侧。

处理方法：旋转父节点，使期变成同则（第4种情况）， 再根据情况4来处理。

### 删除操作

？？？？？？？？？？？？

## 红黑树的应用

1. Linux内核进程调度由红黑树管理进程控制块。 
2. Epoll用红黑树管理事件块。 
3. nginx服务器用红黑树管理定时器。 
4. C++ STL中的map和set的底层实现为红黑树。 
5. Java中的TreeMap和TreeSet由红黑树实现。 
6. Java8开始，HashMap中，当一个桶的链表长度超过8，则会改用红黑树。

## 红黑树 VS 平衡二叉树

- 红黑树放弃了追求完全平衡，追求大致平衡，在与平衡二叉树的时间复杂度相差不大的情况下，保证每次插入最多只需要三次旋转就能达到平衡，实现起来也更为简单。
- 平衡二叉树追求绝对平衡，条件比较苛刻，实现起来比较麻烦，每次插入新节点之后需要旋转的次数不能预知。
  平衡二叉树又被称为AVL树（有别于AVL算法），且具有以下性质：它是一 棵空树或它的左右两个子树的高度差的绝对值不超过1，并且左右两个子树都是一棵平衡二叉树。
- 平衡二叉树的常用算法有红黑树、AVL、Treap等。 最小二叉平衡树的节点的公式如下 F(n)=F(n-1)+F(n-2)+1 这个类似于一个递归的数列，可以参考Fibonacci数列，1是根节点，F(n-1)是左子树的节点数量，F(n-2)是右子树的节点数量
- AVL 树是高度平衡的，频繁的插入和删除，会引起频繁的reblance，导致效率下降
  红黑树不是高度平衡的，算是一种折中，插入最多两次旋转，删除最多三次旋转

## 红黑树实现



## 红黑树在线模拟

https://sandbox.runjs.cn/show/2nngvn8w

# 堆

堆（英语：heap)是计算机科学中一类特殊的数据结构的统称。堆通常是一个可以被看做一棵树的数组对象。堆总是满足下列性质：

- 堆中某个节点的值总是不大于或不小于其父节点的值；
- 堆总是一棵完全二叉树。

将根节点最大的堆叫做最大堆或大根堆，根节点最小的堆叫做最小堆或小根堆。常见的堆有二叉堆、斐波那契堆等。

堆是线性数据结构，相当于一维数组，有唯一后继。

堆的定义如下：n个元素的序列{k1,k2,ki,…,kn}当且仅当满足下关系时，称之为堆。

(ki <= k2i,ki <= k2i+1)或者(ki >= k2i,ki >= k2i+1), (i = 1,2,3,4...n/2)

若将和此次序列对应的一维数组（即以一维数组作此序列的存储结构）看成是一个完全二叉树，则堆的含义表明，完全二叉树中所有非终端结点的值均不大于（或不小于）其左、右孩子结点的值。由此，若序列{k1,k2,…,kn}是堆，则堆顶元素（或完全二叉树的根）必为序列中n个元素的最小值（或最大值）。

堆的应用
1、堆排序。
分两个过程：建堆和排序，建堆的过程就是堆插入元素的过程，我们可以对初始数组原地建堆，然后再依次输出堆顶元素即可达到排序的目的。建堆的时间复杂度为 O(n)，排序过程的时间复杂度为 O(nlogn)，堆排序不是稳定的排序算法，因为在排序的过程中存在将堆的最后一个元素跟堆顶元素交换的操作，可能改变原始相对顺序。
2、优先级队列。
优先级队列，顾名思义，它首先应该是一个队列。队列最大的特性就是先进先出。不过，在优先级队列中，数据的出队顺序不是先进先出，而是按照优先级来，优先级最高的，最先出队。如何实现一个优先级队列呢？方法有很多，但是用堆来实现是最直接、最高效的。这是因为，堆和优先级队列非常相似。一个堆就可以看作一个优先级队列。很多时候，它们只是概念上的区分而已。往优先级队列中插入一个元素，就相当于往堆中插入一个元素；从优先级队列中取出优先级最高的元素，就相当于取出堆顶元素。
干说优先级队列可能有点抽象，下面举两个实用的例子来说明优先级队列。
1）合并有序小文件。
假如有 100 个小文件，每个小文件都为 100 MB，每个小文件中存储的都是有序的字符串，现在要求合并成一个有序的大文件，那么如何做呢？
直观的做法是分别取每个小文件的第一行放入数组，再比较大小，依次插入到大文件中，假如最小的行来自于文件 a，那么插入到大文件中后，从数组中删除该行，再取文件 a 的下一行插入到数组中，再次比较大小，取出最小的插入到大文件的第二行，依次类推，整个过程很像归并排序的合并函数。每次插入到大文件中都要循环遍历整个数组，这显然是低效的。
而借助于堆这种优先级队列就很高效。比如我们可以分别取 100 个文件的第一行建一个小顶堆，假如堆顶元素来自于文件 a，那么取出堆顶元素插入到大文件中，并从堆顶删除该元素（就是堆实现中 removeMax 函数）, 然后再从文件 a 中取下一行插入到堆顶中，重复以上过程就可以完成合并有序小文件的操作。
删除堆顶数据和往堆中插入数据的时间复杂度都是 O(logn)，n 表示堆中的数据个数，这里就是 100。是不是比原来数组存储的方式高效了很多呢？
2）高性能定时器。
假如有很多定时任务，如何设计一个高性能的定时器来执行这些定时任务呢？假如每过一个很小的单位时间（比如 1 秒），就扫描一遍任务，看是否有任务到达设定的执行时间。如果到达了，就拿出来执行。这显然是浪费资源的，因为这些任务的时间间隔可能长达数小时。
借助于堆这种优先级队列我们这可以这样设计：将定时任务按时间先后的顺序建一个小顶堆，先取出堆顶任务，查询其执行时间与当前时间之差，假如为 T 秒，那么在 T - 1 秒的时间内，定时器什么也不需要做，当 T 秒间隔达到时就取出任务执行，对应的从堆顶删除堆顶元素，然后再取下一个堆顶元素，查询其执行时间。
这样，定时器既不用间隔 1 秒就轮询一次，也不用遍历整个任务列表，性能也就提高了。

3、取 top k 元素。
取 top k 元素的情形可分为两类，一类是静态数据集合，也就是说数据确定后不再增加新的元素，另一类是动态数据集合，会随时增加元素，但依然求第 k 大元素。

对于静态数据，我们可以先从静态数据依次插入小顶堆中，维护一个大小为 k 的小顶堆，遍历其余数据，依次插入到大小为 k 的小顶堆中，如果元素比 k 小，则不做处理，继续遍历下一个数据，如果比 k 大，则删除堆顶堆，并将该值插入到堆顶中，这样遍历结束时，堆顶元素就是第 k 大元素。

遍历数组需要 O(n) 的时间复杂度，一次堆化操作需要 O(logK) 的时间复杂度，所以最坏情况下，n 个元素都入堆一次，所以时间复杂度就是 O(nlogK)。

对于动态数据，处理方法也是一样的，相当于实时求 top k，那么每次求 top k 时重新计算一下即可，时间复杂度仍是 O(nlogK)，n 表示当前的数据的大小。我们可以一直都维护一个 K 大小的小顶堆，当有数据被添加到集合中时，我们就拿它与堆顶的元素对比。如果比堆顶元素大，我们就把堆顶元素删除，并且将这个元素插入到堆中；如果比堆顶元素小，则不做处理。这样，无论任何时候需要查询当前的前 K 大数据，我们都可以里立刻返回给他。

4、取中位数。

对于一组静态数据，中位数是固定的，我们可以先排序，第 n/2 个数据就是中位数。每次询问中位数的时候，我们直接返回这个固定的值就好了。所以，尽管排序的代价比较大，但是边际成本会很小。但是，如果我们面对的是动态数据集合，中位数在不停地变动，如果再用先排序的方法，每次询问中位数的时候，都要先进行排序，那效率就不高了。

对于动态数据，假如个数为 n，当然 n 是会变化的。初始化时先对 n 个数据从小到大排序。
当 n 为偶数时，我们维护两个堆，将有序数据中的前面 n/2 个元素维护成大顶堆，后面 n/2 的维护成小顶堆，小顶堆中的元素都大于大顶堆中的元素，大顶堆的堆顶元素和小顶堆的堆顶元素就是中位数。
当 n 为奇数时，同样维护两个堆，将前面 n/2 + 1 个元素维护成大顶堆，将后面 n/2 个元素维护成小顶堆，这样大顶堆的堆顶元素就是中位数。
下面考虑新插入元素，当新插入元素大于等于小顶堆堆顶元素时就插入小顶堆，否则就插入大顶堆。
这样有可能导致大顶堆和小顶堆的元素个数不满足上述 n 为奇数和偶数的两种情况，当不满足时就转移元素，当小顶堆的元素多于大顶堆时，就将小顶堆堆顶元素插入到大顶堆中，同时从小顶堆中删除堆顶元素。

1）中位数引申出的 99% 响应时间

如何快速求接口的 99% 响应时间？

中位数的概念就是将数据从小到大排列，处于中间位置，就叫中位数，这个数据会大于等于前面 50% 的数据。99 百分位数的概念可以类比中位数，如果将一组数据从小到大排列，这个 99 百分位数就是大于前面 99% 数据的那个数据。

弄懂了这个概念，我们再来看 99% 响应时间。如果有 100 个接口访问请求，每个接口请求的响应时间都不同，比如 55 毫秒、100 毫秒、23 毫秒等，我们把这 100 个接口的响应时间按照从小到大排列，排在第 99 的那个数据就是 99% 响应时间，也叫 99 百分位响应时间。

同样地，维护两个堆，一个大顶堆，一个小顶堆。假设当前总数据的个数是 n，大顶堆中保存 n99% 个数据，小顶堆中保存 n1% 个数据。大顶堆堆顶的数据就是我们要找的 99% 响应时间。

每次插入一个数据的时候，我们要判断这个数据跟大顶堆和小顶堆堆顶数据的大小关系，然后决定插入到哪个堆中。如果这个新插入的数据比大顶堆的堆顶数据小，那就插入大顶堆；如果这个新插入的数据比小顶堆的堆顶数据大，那就插入小顶堆。

来个有趣味的问题：

如何在单机 1 GB 的内存中从 10 亿条搜索关键词日志记录中找出 top 10 最热门的关键词，假如每条关键词平均占用 50 字节。

这里给出方法（来自极客专栏，详见前文 工作后，为什么还要学习数据结构与算法（文末福利））

10 亿的关键词还是很多的。我们假设 10 亿条搜索关键词中不重复的有 1 亿条，如果每个搜索关键词的平均长度是 50 个字节，那存储 1 亿个关键词起码需要 5GB 的内存空间，而机器只有 1GB 的可用内存空间，所以我们无法一次性将所有的搜索关键词加入到内存中。这个时候该怎么办呢？
相同数据经过哈希算法得到的哈希值是一样的。我们可以利用哈希算法的这个特点，将 10 亿条搜索关键词先通过哈希算法分片到 10 个文件中。
具体可以这样做：我们创建 10 个空文件 00，01，02，……，09。我们遍历这 10 亿个关键词，并且通过某个哈希算法对其求哈希值，然后哈希值同 10 取模，得到的结果就是这个搜索关键词应该被分到的文件编号。
我们针对每个包含 1 亿条搜索关键词及其搜索记录数（通过hash算法得到相同搜索关键词的记录数）的文件，利用散列表和堆，分别求出 Top 10，然后把这个 10 个 Top 10 放在一块，然后取这 100 个关键词中，出现次数最多的 10 个关键词，这就是这 10 亿数据中的 Top 10 最频繁的搜索关键词了。
优先级队列是一种特殊的队列，优先级高的数据先出队，而不再像普通的队列那样，先进先出。实际上，堆就可以看作优先级队列，只是称谓不一样罢了。求 Top K 问题又可以分为针对静态数据和针对动态数据，只需要利用一个堆，就可以做到非常高效率的查询 Top K 的数据。求中位数实际上还有很多变形，比如求 99 百分位数据、90 百分位数据等，处理的思路都是一样的，即利用两个堆，一个大顶堆，一个小顶堆，随着数据的动态添加，动态调整两个堆中的数据，最后大顶堆的堆顶元素就是要求的数据。

## Java中的堆与栈

Java把内存划分成两种：一种是栈内存，一种是堆内存。 

1. 栈(stack)与堆(heap)都是Java用来在Ram中存放数据的地方。与C++不同，Java自动管理栈和堆，程序员不能直接地设置栈或堆。

2. 栈的优势是，存取速度比堆要快，仅次于直接位于CPU中的寄存器。但缺点是，存在栈中的数据大小与生存期必须是确定的，缺乏灵活性。另外，栈数据可以共享。

3. 堆的优势是可以动态地分配内存大小，生存期也不必事先告诉编译器，Java的垃圾收集器会自动收走这些不再使用的数据。但缺点是，由于要在运行时动态分配内存，存取速度较慢。  

4. Java中的数据类型有两种。

5. 一种是基本类型(primitive types), 共有8种，即int, short, long, byte, float, double, boolean, char(注意， 并没有string的基本类型)。这种类型的定义是通过诸如int a = 3; long b = 255L;的形式来定义的，称为自动变量。值得注意的是， 
   自动变量存的是字面值，不是类的实例，即不是类的引用，这里并没有类的存在。如int a = 3; 这里的a是一个指向int类型的引用， 指向3这个字面值。这些字面值的数据，由于大小可知，生存期可知(这些字面值固定定义在某个程序块里面，程序块退出后，字段值就消失了)， 出于追求速度的原因，就存在于栈中。 另外，栈有一个很重要的特殊性，就是存在栈中的数据可以共享。假设我们同时定义：  
   int a = 3;  
   int b = 3;  
   编译器先处理int a = 3；首先它会在栈中创建一个变量为a的引用，然后查找有没有字面值为3的地址，没找到，就开辟一个存放3这个字面值的地址，然后将a指向3的地址。接着处理int b = 3；在创建完b的引用变量后，由于在栈中已经有3这个字面值，便将b直接指向3的地址。这样，就出现了a与b同时均指向3的情况。  
   特别注意的是，这种字面值的引用与类对象的引用不同。假定两个类对象的引用同时指向一个对象，如果一个对象引用变量修改了这个 对象的内部状态，那么另一个对象引用变量也即刻反映出这个变化。相反，通过字面值的引用来修改其值，不会导致另一个指向此字面值的引用的值也跟着改变的情况。如上例，我们定义完a与b的值后，再令a=4；那么，b不会等于4，还是等于3。在编译器内部，遇到a=4；时，它就会重新搜索栈中是否有4的字面值，如果没有，重新开辟地址存放4的值；如果已经有了，则直接将a指向这个地址。因此a值的改变不会影响到b的值。 

6. 另一种是包装类数据，如Integer, String, Double等将相应的基本数据类型包装起来的类。这些类数据全部存在于堆中，Java用new() 语句来显示地告诉编译器，在运行时才根据需要动态创建，因此比较灵活，但缺点是要占用更多的时间。   

7. 在JAVA中，有六个不同的地方可以存储数据：  
   1. 寄存器（register）。这是最快的存储区，因为它位于不同于其他存储区的地方——处理器内部。但是寄存器的数量极其有限，所以寄存器由编译器根据需求进行分配。你不能直接控制，也不能在程序中感觉到寄存器存在的任何迹象。  

   2. 堆栈（stack）。位于通用RAM中，但通过它的“堆栈指针”可以从处理器哪里获得支持。堆栈指针若向下移动，则分配新的内存；若向上移动，则释放那些内存。这是一种快速有效的分配存储方法，仅次于寄存器。创建程序时候，JAVA编译器必须知道存储在堆栈内所有数据的确切大小和生命周期，因为它必须生成相应的代码，以便上下移动堆栈指针。这一约束限制了程序的灵活性，所以虽然某些JAVA数据存储在堆栈中——特别是`对象引用`，但是JAVA对象不存储其中。  

   3. 堆（heap）。一种通用性的内存池（也存在于RAM中），用于存放所以的JAVA对象。堆不同于堆栈的好处是：编译器不需要知道要从堆里分配多少存储区域，也不必知道存储的数据在堆里存活多长时间。因此，在堆里分配存储有很大的灵活性。当你需要创建一个对象的时候，只需要new写一行简单的代码，当执行这行代码时，会自动在堆里进行存储分配。当然，为这种灵活性必须要付出相应的代码。用堆进行存储分配比用堆栈进行存储存储需要更多的时间。  

   4. 静态存储（static storage）。这里的“静态”是指“在固定的位置”。静态存储里存放程序运行时一直存在的数据。你可用关键字static来标识一个对象的特定元素是静态的，但JAVA对象本身从来不会存放在静态存储空间里。  

   5. 常量存储（constant storage）。常量值通常直接存放在程序代码内部，这样做是安全的，因为它们永远不会被改变。有时，在嵌入式系统中，常量本身会和其他部分分割离开，所以在这种情况下，可以选择将其放在ROM中  

   6. 非RAM存储。如果数据完全存活于程序之外，那么它可以不受程序的任何控制，在程序没有运行时也可以存在。

   7. 就速度来说，有如下关系：  
       寄存器 < 堆栈 < 堆 < 其他 


在函数中定义的一些基本类型的变量和对象的引用变量都在函数的栈内存中分配。  
当在一段代码块定义一个变量时，Java就在栈中为这个变量分配内存空间，当超过变量的作用域后，Java会自动释放掉为该变量所分配的内存空间，该内存空间可以立即被另作他用。  
堆内存用来存放由new创建的对象和数组。 在堆中分配的内存，由Java虚拟机的自动垃圾回收器来管理。`在堆中产生了一个数组或对象后，还可以在栈中定义一个特殊的变量，让栈中这个变量的取值等于数组或对象在堆内存中的首地址，栈中的这个变量就成了数组或对象的引用变量.` 引用变量就相当于是为数组或对象起的一个名称，以后就可以在程序中使用栈中的引用变量来访问堆中的数组或对象。
```java
String str1 = "abc";  
String str2 = "abc";  
System.out.println(str1==str2); //true 可以看出str1和str2是指向同一个对象的。 

String str1 =new String ("abc");  
String str2 =new String ("abc");  
System.out.println(str1==str2); // false 用new的方式是生成不同的对象。每一次生成一个。  

String s1 = "ja";  
String s2 = "va";  
String s3 = "java";  
String s4 = s1 + s2;  
System.out.println(s3 == s4);//false  
System.out.println(s3.equals(s4));//true 
```
比较类里面的数值是否相等时，用equals()方法；当测试两个包装类的引用是否指向同一个对象时，用==，用第一种方式创建多个”abc”字符串,在内存中其实只存在一个对象而已. 这种写法有利与节省内存空间. 同时它可以在一定程度上提高程序的运行速度，因为JVM会自动根据栈中数据的实际情况来决定是否有必要创建新对象。而对于String str = new String("abc")；的代码，则一概在堆中创建新对象，而不管其字符串值是否相等，是否有必要创建新对象，从而加重了程序的负担。  
另一方面, 要注意: 我们在使用诸如String str = "abc"；的格式定义类时，总是想当然地认为，创建了String类的对象str。（不一定，因为如果事先没有，那么就会创建，这就是创建对象了，如果原来就有，那就指向那个原来的对象就可以了）！对象可能并没有被创建！而可能只是指向一个先前已经创建的对象。只有通过new()方法才能保证每次都创建一个新的对象。由于String类的immutable性质，当String变量需要经常变换其值时，应该考虑使用StringBuffer类，以提高程序效率。

## 实现一个小顶堆、大顶堆、优先级队列

堆的定义： 堆实际上是一棵完全二叉树。 
堆满足两个性质: 
1、堆的每一个父节点都大于（或小于）其子节点； 
2、堆的每个左子树和右子树也是一个堆。 
堆的分类：  
1、最大堆（大顶堆）：堆的每个父节点都大于其孩子节点； 
2、最小堆（小顶堆）：堆的每个父节点都小于其孩子节点； 

**堆的存储：** 
一般都用数组来表示堆，i结点的父结点下标就为(i – 1) / 2。它的左右子结点下标分别为2 * i + 1和2 * i + 2。
堆排序： 
由上面的介绍我们可以看出堆的第一个元素要么是最大值（大顶堆），要么是最小值（小顶堆），这样在排序的时候（假设共n个节点），直接将第一个元素和最后一个元素进行交换，然后从第一个元素开始进行向下调整至第n-1个元素。所以，如果需要升序，就建一个大堆，需要降序，就建一个小堆。 
堆排序的步骤分为三步: 
1、建堆（升序建大堆，降序建小堆）； 
2、交换数据； 
3、向下调整。

只实现了大顶堆，最小堆类似

```python
# parent(i) = floor((i - 1)/2)
# left(i)   = 2i + 1
# right(i)  = 2i + 2
class MaxHeap:
    def __init__(self):
        self.list = []
        self.size = 0


    def insert(self,data):
        self.list.append(data)
        self.size += 1
        self.shift_up(self.size -1)


    def shift_up(self,index):
        if (index-1)//2>=0 and self.list[(index-1)//2] < self.list[index]:
            self.list[(index - 1) // 2] , self.list[index] = self.list[index], self.list[(index - 1) // 2]
            self.shift_up((index - 1) // 2)


    def shift_down(self,index):
        if index * 2 + 1 <= self.size-1:
            child = index * 2 + 1
            if child+1 <= self.size-1:
                if self.list[child] < self.list[child+1]:
                    child += 1
            if self.list[child] > self.list[index]:
                self.list[child] , self.list[index] = self.list[index] , self.list[child]
                self.shift_down(child)


    def pop(self):
        if self.size == 0:
            return None
        self.list[0],self.list[self.size-1] =  self.list[self.size-1],self.list[0]
        item = self.list.pop()
        self.size -= 1
        self.shift_down(0)
        return item

if __name__ == '__main__':
    MaxHeap = MaxHeap()
    MaxHeap.insert(3)
    MaxHeap.insert(4)
    MaxHeap.insert(5)
    print(MaxHeap.list)
    print(MaxHeap.pop())
    print(MaxHeap.list)
```
优先队列(priority queue)
普通的队列是一种先进先出的数据结构，元素在队列尾追加，而从队列头删除。在优先队列中，元素被赋予优先级。当访问元素时，具有最高优先级的元素最先删除。优先队列具有最高级先出 （first in, largest out）的行为特征。通常采用堆数据结构来实现。
```python
from heapq import heappush, heappop
class PriorityQueue:
    def __init__(self):
        self._queue = []
 
    def put(self, item, priority):
        heappush(self._queue, (-priority, item))
 
    def get(self):
        if len(self._queue) == 0:
            return None
        return heappop(self._queue)[-1]
 
q = PriorityQueue()
q.put('world', 1)
q.put('hello', 2)
print q.get()
```

## 利用优先级队列合并 K 个有序数组
用优先队列解决的就是每次比较当前k个数值，其中k是链表的个数，然后最小的那个数值对应的链表后移。
先将所有列表按照第一个元素的大小放入小根堆中，然后每次取回最小的元素的列表，将该列表下一个元素放入小根堆中。直到小根堆为空。这样就得到了排好序的一个列表。小根堆构建的复杂度为O(NlogN),每次取最小值的复杂度为O(logN),向小根堆添加元素的复杂度为O(logN)。所有总的实现复杂度为O(NlogN)。
```python
def combine_k_sorted_arr(lists):
    queue = PriorityQueue()
    result = []
    for i in range(len(lists)):
        queue.put(lists[i],-lists[i][0])

    while not queue.is_empty():
        current = queue.get()
        result.append(current.pop(0))
        if len(current)!= 0:
            queue.put(current,-current[0])   ###优先级高的先出
    return result

```
## 求一组动态数据集合的最大 Top K
取 top k 元素的情形可分为两类，一类是静态数据集合，也就是说数据确定后不再增加新的元素，另一类是动态数据集合，会随时增加元素，但依然求第 k 大元素。

对于静态数据，我们可以先从静态数据依次插入小顶堆中，维护一个大小为 k 的小顶堆，遍历其余数据，依次插入到大小为 k 的小顶堆中，如果元素比 k 小，则不做处理，继续遍历下一个数据，如果比 k 大，则删除堆顶堆，并将该值插入到堆顶中，这样遍历结束时，堆顶元素就是第 k 大元素。

遍历数组需要 O(n) 的时间复杂度，一次堆化操作需要 O(logK) 的时间复杂度，所以最坏情况下，n 个元素都入堆一次，所以时间复杂度就是 O(nlogK)。

对于动态数据，处理方法也是一样的，相当于实时求 top k，那么每次求 top k 时重新计算一下即可，时间复杂度仍是 O(nlogK)，n 表示当前的数据的大小。我们可以一直都维护一个 K 大小的小顶堆，当有数据被添加到集合中时，我们就拿它与堆顶的元素对比。如果比堆顶元素大，我们就把堆顶元素删除，并且将这个元素插入到堆中；如果比堆顶元素小，则不做处理。这样，无论任何时候需要查询当前的前 K 大数据，我们都可以里立刻返回给他。

## 堆排序复习

## leetcode习题

**[Binary Tree Level Order Traversal](https://leetcode-cn.com/problems/binary-tree-level-order-traversal/) 二叉树层次遍历(102,107)** 

见上面的层次遍历

Invert Binary Tree（翻转二叉树）
英文版：[Loading...](https://leetcode.com/problems/invert-binary-tree/)
中文版：[力扣](https://leetcode-cn.com/problems/invert-binary-tree/)

```python
# recursive
class Solution:
    def invertTree(self, root: TreeNode) -> TreeNode:
        if root is not None:
            root.left,root.right = root.right,root.left
            self.invertTree(root.left)
            self.invertTree(root.right)
        return root
# non-recursive queue
    def invertTree(self, root: TreeNode) -> TreeNode:
        if root is None:
            return root
        queue = []
        queue.append(root)        
        while queue:
            next_queue = []
            for node in queue:
                node.left, node.right = node.right, node.left
                if node.left:
                    next_queue.append(node.left)
                if node.right:
                    next_queue.append(node.right)
            queue = next_queue
        return root
```

Maximum Depth of Binary Tree（二叉树的最大深度）
英文版：[Loading...](https://leetcode.com/problems/maximum-depth-of-binary-tree/)
中文版：[力扣](https://leetcode-cn.com/problems/maximum-depth-of-binary-tree/)

```python
class Solution:
    def maxDepth(self, root: TreeNode) -> int:
        if root is None:
            return 0
        left_max = self.maxDepth(root.left)
        right_max = self.maxDepth(root.right)
        return max(left_max,right_max)+1
```
时间复杂度：我们每个结点只访问一次，因此时间复杂度为 \mathcal{O}(N)O(N)， 其中 NN 是结点的数量。
空间复杂度：在最糟糕的情况下，树是完全不平衡的，例如每个结点只剩下左子结点，递归将会被调用 N 次（树的高度），因此保持调用栈的存储将是 O(N)。但在最好的情况下（树是完全平衡的），树的高度将是log(N)。因此，在这种情况下的空间复杂度将是O(log(N))。
```python
#我们的想法是使用 DFS 策略访问每个结点，同时在每次访问时更新最大深度。
#所以我们从包含根结点且相应深度为 1 的栈开始。然后我们继续迭代：将当前结点弹出栈并推入子结点。每一步都会更新深度。
class Solution:
    def maxDepth(self, root):
        stack = []
        if root is not None:
            stack.append((1, root))       
        depth = 0
        while stack != []:
            current_depth, root = stack.pop()
            if root is not None:
                depth = max(depth, current_depth)
                stack.append((current_depth + 1, root.left))
                stack.append((current_depth + 1, root.right))
        return depth
```
Validate Binary Search Tree（验证二叉查找树）
英文版：[Loading...](https://leetcode.com/problems/validate-binary-search-tree/)
中文版：[力扣](https://leetcode-cn.com/problems/validate-binary-search-tree/)
```python
# 中序遍历二叉搜索树变一个由小到大的序列
        for i in range(1,len(self.List)):
            if self.List[i]<=self.List[i-1]:
                return False
        return True 

```
Path Sum（路径总和）
英文版：[Loading...](https://leetcode.com/problems/path-sum/)
中文版：[力扣](https://leetcode-cn.com/problems/path-sum/)
[这个博客写的很棒诶](https://blog.csdn.net/coder_orz/article/details/51595815)
```python
# 递归 减法思想
class Solution:
    def hasPathSum(self, root: TreeNode, sum: int) -> bool:
        if not root:
            return False
        if not root.left and not root.right:
            return root.val == sum
        return self.hasPathSum(root.left,sum-root.val) or self.hasPathSum(root.right,sum-root.val)

#用栈也可以实现
class Solution:
    def hasPathSum(self, root: TreeNode, sum: int) -> bool:
        stack = [(root,sum)]
        while stack:
        	root,num = stack.pop()
        	if root:
                if root.val == sum:
                    return True
                if root.left:
                    stack.append((root.left,sum-root.val))
                    stack.append((root.left,sum-root.val))
        return False
##后序遍历也可以
class Solution(object):
    def hasPathSum(self, root, sum):
        pre, cur = None, root
        tmp_sum = 0
        stack = []
        while cur or len(stack) > 0:
            while cur:
                stack.append(cur)
                tmp_sum += cur.val
                cur = cur.left
            cur = stack[-1]
            if not cur.left and not cur.right and tmp_sum == sum:
                return True
            if cur.right and pre != cur.right:
                cur = cur.right
            else:
                pre = cur
                stack.pop()
                tmp_sum -= cur.val
                cur = None
        return False
```
打印所有路径（257）
参考  https://blog.csdn.net/coder_orz/article/details/51706119
```python
class Solution:
    def binaryTreePaths(self, root):
        res, stack = [], [(root, '')]
        while stack:
            node, curs = stack.pop()
            if node:
                if not node.left and not node.right:
                    res.append(curs + str(node.val))
                stack.append((node.left, curs + str(node.val) + '->'))
                stack.append((node.right, curs + str(node.val) + '->'))
        return res
```


## python 对象动态格式化输出

重写str方法

```python
    def gatherAttrs(self):
        return ",".join("{}={}"
                        .format(k, getattr(self, k))
                        for k in self.__dict__.keys())

    def __str__(self):
        return "[{}:{}]".format(self.__class__.__name__, self.gatherAttrs())

```

## 参考

https://blog.csdn.net/zhaoyunfullmetal/article/details/479033197

https://blog.csdn.net/songyunli1111/article/details/81706801

https://blog.csdn.net/qq_33404395/article/details/80249536

[堆的实现及工程应用(Python)](https://zhuanlan.zhihu.com/p/57667927)