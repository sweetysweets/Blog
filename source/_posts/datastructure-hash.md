---
title: 数据结构之哈希 ! important
date: 2019-03-06 19:13:25
tags:
- Data Structure
---

### 散列表（哈希表）

[散列表](https://baike.baidu.com/item/%E6%95%A3%E5%88%97%E8%A1%A8/10027933)（Hash table，也叫哈希表），是根据关键码值(Key value)而直接进行访问的[数据结构](https://baike.baidu.com/item/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/1450)。也就是说，它通过把关键码值映射到表中一个位置来访问记录，以加快查找的速度。这个映射函数叫做[散列函数](https://baike.baidu.com/item/%E6%95%A3%E5%88%97%E5%87%BD%E6%95%B0/2366288)，存放记录的[数组](https://baike.baidu.com/item/%E6%95%B0%E7%BB%84/3794097)叫做[散列表](https://baike.baidu.com/item/%E6%95%A3%E5%88%97%E8%A1%A8/10027933)。

给定表M，存在函数f(key)，对任意给定的关键字值key，代入函数后若能得到包含该关键字的记录在表中的地址，则称表M为哈希(Hash）表，函数f(key)为哈希(Hash) 函数。

<!--more-->

**处理冲突**

1. [开放寻址法](https://baike.baidu.com/item/%E5%BC%80%E6%94%BE%E5%AF%BB%E5%9D%80%E6%B3%95)：Hi=(H(key) + di) MOD m,i=1,2，…，k(k<=m-1），其中H(key）为[散列函数](https://baike.baidu.com/item/%E6%95%A3%E5%88%97%E5%87%BD%E6%95%B0)，m为[散列表](https://baike.baidu.com/item/%E6%95%A3%E5%88%97%E8%A1%A8)长，di为增量序列，可有下列三种取法：

   1.1. di=1,2,3，…，m-1，称线性探测再散列；

   1.2. di=1^2,-1^2,2^2,-2^2，⑶^2，…，±（k)^2,(k<=m/2）称二次探测再散列；

   1.3. di=[伪随机数](https://baike.baidu.com/item/%E4%BC%AA%E9%9A%8F%E6%9C%BA%E6%95%B0)序列，称伪随机探测再散列。

2. 再[散列法](https://baike.baidu.com/item/%E6%95%A3%E5%88%97%E6%B3%95)：Hi=RHi(key),i=1,2，…，k RHi均是不同的[散列函数](https://baike.baidu.com/item/%E6%95%A3%E5%88%97%E5%87%BD%E6%95%B0)，即在同义词产生地址冲突时计算另一个散列函数地址，直到冲突不再发生，这种方法不易产生“聚集”，但增加了计算时间。

3. 链地址法（拉链法）

4. 建立一个公共溢出区？？？？

   其基本思想是：所有关键字和基本表中关键字为相同哈希值的记录，不管他们由哈希函数得到的哈希地址是什么，一旦发生冲突，都填入溢出表。

**著名的hash算法**

MD5 和 SHA-1 可以说是目前应用最广泛的Hash算法，而它们都是以 MD4 为基础设计的。那么他们都是什么意思呢?

**⑴ MD4**

MD4(RFC 1320）是 MIT 的 Ronald L. Rivest 在 1990 年设计的，MD 是 Message Digest 的缩写。它适用在32位[字长](https://baike.baidu.com/item/%E5%AD%97%E9%95%BF)的处理器上用高速软件实现--它是基于 32 [位操作](https://baike.baidu.com/item/%E4%BD%8D%E6%93%8D%E4%BD%9C)数的位操作来实现的。

**⑵ MD5**

MD5(RFC 1321）是 Rivest 于1991年对MD4的改进版本。它对输入仍以512位分组，其输出是4个32位字的级联，与 MD4 相同。MD5比MD4来得复杂，并且速度较之要慢一点，但更安全，在抗分析和抗差分方面表现更好

**⑶ SHA-1 及其他**

SHA1是由NIST NSA设计为同DSA一起使用的，它对长度小于264的输入，产生长度为160bit的散列值，因此抗穷举（brute-force）性更好。SHA-1 设计时基于和MD4相同原理，并且模仿了该算法。

**应用**

**⑴**[文件校验](https://baike.baidu.com/item/%E6%96%87%E4%BB%B6%E6%A0%A1%E9%AA%8C)

我们比较熟悉的校验算法有[奇偶校验](https://baike.baidu.com/item/%E5%A5%87%E5%81%B6%E6%A0%A1%E9%AA%8C)和CRC校验，这2种校验并没有抗[数据篡改](https://baike.baidu.com/item/%E6%95%B0%E6%8D%AE%E7%AF%A1%E6%94%B9)的能力，它们一定程度上能检测出数据传输中的信道误码，但却不能防止对数据的恶意破坏。

MD5 Hash算法的"数字指纹"特性，使它成为目前应用最广泛的一种文件完整性[校验和](https://baike.baidu.com/item/%E6%A0%A1%E9%AA%8C%E5%92%8C)（Checksum）算法，不少Unix系统有提供计算md5 checksum的命令。

**⑵**[数字签名](https://baike.baidu.com/item/%E6%95%B0%E5%AD%97%E7%AD%BE%E5%90%8D)

Hash 算法也是现代密码体系中的一个重要组成部分。由于[非对称算法](https://baike.baidu.com/item/%E9%9D%9E%E5%AF%B9%E7%A7%B0%E7%AE%97%E6%B3%95)的运算速度较慢，所以在[数字签名](https://baike.baidu.com/item/%E6%95%B0%E5%AD%97%E7%AD%BE%E5%90%8D)协议中，[单向散列函数](https://baike.baidu.com/item/%E5%8D%95%E5%90%91%E6%95%A3%E5%88%97%E5%87%BD%E6%95%B0)扮演了一个重要的角色。对 Hash 值，又称"数字摘要"进行数字签名，在统计上可以认为与对文件本身进行数字签名是等效的。而且这样的协议还有其他的优点。

**⑶ 鉴权协议**

如下的鉴权协议又被称作挑战--认证模式：在传输信道是可被侦听，但不可被篡改的情况下，这是一种简单而安全的方法。

## Java 实现

**Hash map & hash table**

HashMap是Hashtable的轻量级实现（非线程安全的实现），他们都完成了Map接口，主要区别在于HashMap允许空（null）键值（key）,由于非线程安全，效率上可能高于Hashtable。HashMap允许将null作为一个entry的key或者value，而Hashtable不允许。HashMap把Hashtable的contains方法去掉了，改成containsvalue和containsKey。因为contains方法容易让人引起误解。 Hashtable继承自Dictionary类，而HashMap是Java1.2引进的Map interface的一个实现。最大的不同是，Hashtable的方法是Synchronize的，而HashMap不是，在多个线程访问Hashtable时，不需要自己为它的方法实现同步，而HashMap 就必须为之提供外同步(Collections.synchronizedMap)。 Hashtable和HashMap采用的hash/rehash[算法](http://lib.csdn.net/base/datastructure)都大概一样，所以性能不会有很大的差异。

**Hashmap 存储结构**

  HashMap的使用那么简单，那么问题来了，它是怎么存储的，他的存储结构是怎样的，很多程序员都不知道，其实当你put和get的时候，稍稍往前一步，你看到就是它的真面目。其实简单的说HashMap的存储结构是由数组和链表共同完成的。如图：

![](https://raw.githubusercontent.com/sweetysweets/BlogResource/master/blog/datasource-hash-1.png) 

  从上图可以看出HashMap是Y轴方向是数组，X轴方向就是链表的存储方式。大家都知道数组的存储方式在内存的地址是连续的，大小固定，一旦分配不能被其他引用占用。它的特点是查询快，时间复杂度是O(1)，插入和删除的操作比较慢，时间复杂度是O(n)，链表的存储方式是非连续的，大小不固定，特点与数组相反，插入和删除快，查询速度慢。HashMap可以说是一种折中的方案吧。

**HashMap基本原理**

 1、首先判断Key是否为Null，如果为null，直接查找Enrty[0]，如果不是Null，先计算Key的HashCode，然后经过二次Hash。得到Hash值，这里的Hash特征值是一个int值。

 2、根据Hash值，要找到对应的数组，所以对Entry[]的长度length求余，得到的就是Entry数组的index。

 3、找到对应的数组，就是找到了所在的链表，然后按照链表的操作对Value进行插入、删除和查询操作。

HashMap的hash计算时先计算hashCode(),然后进行二次hash。代码如下： 

```
// 计算二次Hash    
int hash = hash(key.hashCode());

// 通过Hash找数组索引
int i = indexFor(hash, table.length);
```

**为什么二次索引**？

在hash找数组索引时，是按照` return h & (length-1);`来找，这样会导致如果length很小，比如16（二进制是00011111），做按位与操作的时候高位不参与计算（因为都是0）这样导致的结果就是只要是低位是一样的，高位无论是什么，最后结果是一样的，如果这样依赖，hash碰撞始终在一个数组上，导致这个数组开始的链表无限长，那么在查询的时候就速度很慢。

```
  static int hash(int h) {
        // This function ensures that hashCodes that differ only by
        // constant multiples at each bit position have a bounded
        // number of collisions (approximately 8 at default load factor).
        h ^= (h >>> 20) ^ (h >>> 12);      
        return h ^ (h >>> 7) ^ (h >>> 4);
        h ^= (h >>> 20) ^ (h >>> 12);
        意味着h=h的前12位不变+中间的8位位中间的8位和前8位异或值+后12位为后12位和前间12位和9-20位的异或值（+不是加法是连接，一共32位）
h ^ (h >>> 7) ^ (h >>> 4); 之后再用类似的方式
新h=前4位不变+前3位和5-7位异或值+前25位和后25位和4-28位的异或值
    }
```

为了解决这个问题，HashMap在做indexFor操作前，需要调用hash方法，使hash值的位值在高低位上尽量分布均匀.还是按前面的key，经过Object的hash方法后，分别为32，64来进行运算：
32调用hash运算过程如下：
   原始h为32的二进制: 
        100000
        h>>>20:  
        000000
    h>>>12:
        000000
    
接着运算 h^(h>>>20)^(h>>>12):
    结果：    100000

然后运算: h^(h>>>7)^(h>>>4),
过程如下：
    h>>>7:    000000
    h>>>4:    000010
最后运算: h^(h>>>7)^(h>>>4)，
    结果：    100010，即十进制34
    
    调用indexFor方法：
        100010 & 1111 => 2，即存放在Entry数组下标2的位置上
------------------------------------

64的运算结果为：1000100，十进制值为68
    调用indexfor方法：
        1000100 & 1111 => 4，即存放在Entry数组下标4的位置上

可以看到经过hash方法后，再调用indexFor方法，这样可以减少冲突。

**put 原理**

  (1) 首先判断key是否为null，如果是null，就单独调用putForNullKey(value)处理。默认就存储到table[0]开头的链表了。然后遍历table[0]的链表的每个节点Entry，如果发现其中存在节点Entry的key为null，就替换新的value，然后返回旧的value，如果没发现key等于null的节点Entry，就增加新的节点。

 (2) 计算key的hashcode，再用计算的结果二次hash，通过indexFor(hash, table.length);找到Entry数组的索引i。

  (3) 然后遍历以table[i]为头节点的链表，如果发现有节点的hash，key都相同的节点时，就替换为新的value，然后返回旧的value。

  (4) modCount是干嘛的啊? 让我来为你解答。众所周知，HashMap不是线程安全的，但在某些容错能力较好的应用中，如果你不想仅仅因为1%的可能性而去承受hashTable的同步开销，HashMap使用了Fail-Fast机制来处理这个问题，你会发现modCount在源码中是这样声明的。

```
 transient volatile int modCount;
```

volatile关键字声明了modCount，代表了多线程环境下访问modCount，根据JVM规范，只要modCount改变了，其他线程将读到最新的值。其实在Hashmap中modCount只是在迭代的时候起到关键作用。

使用Iterator开始迭代时，会将modCount的赋值给expectedModCount，在迭代过程中，通过每次比较两者是否相等来判断HashMap是否在内部或被其它线程修改，如果modCount和expectedModCount值不一样，证明有其他线程在修改HashMap的结构，会抛出异常。

所以HashMap的put、remove等操作都有modCount++的计算。

  (5)  如果没有找到key的hash相同的节点，就增加新的节点addEntry(),这里增加节点的时候取巧了，每个新添加的节点都增加到头节点，然后新的头节点的next指向旧的老节点。

(6) 如果HashMap大小超过临界值，就要重新设置大小，扩容，见第9节内容。

**get**

```
public V get(Object key) {
        if (key == null)
            return getForNullKey();
        int hash = hash(key.hashCode());
        for (Entry<K,V> e = table[indexFor(hash, table.length)];
             e != null;
             e = e.next) {
            Object k;
            if (e.hash == hash && ((k = e.key) == key || key.equals(k)))
                return e.value;
        }
        return null;
    }
```

别看这段代码，它带来的问题是巨大的，千万记住,HashMap是非线程安全的，所以这里的循环会导致死循环的。为什么呢?当你查找一个key的hash存在的时候，进入了循环，恰恰这个时候，另外一个线程将这个Entry删除了，那么你就一直因为找不到Entry而出现死循环，最后导致的结果就是代码效率很低，CPU特别高。一定记住。

**总结**：

　**HashMap**中键值 **允许为空** 并且是**非同步**的

　**Hashtable**中键值 **不允许为空** 是**同步**的

　**继承不同，但都实现了Map接口**

## Python 实现

**dict底层实现**

dict采用了哈希表，最低能在 O(1)时间内完成搜索。同样的java的HashMap也是采用了哈希表实现，不同是dict在发生哈希冲突的时候采用了开放寻址法，而HashMap采用了链接法。

产生哈希冲突时, python 会通过一个二次探测函数 f, 计算下一个候选位置,当下一个位置可用，则将数据插入该位置,如果不可用则再次调用探测函数 f,获得下一个候选位置，因此经过不断探测,总会找到一个可用的位置

**开放寻址法**

优点

1、记录更容易进行序列化（serialize）操作 

2、如果记录总数可以预知，可以创建完美哈希函数，此时处理数据的效率是非常高的

缺点

1、存储记录的数目不能超过桶数组的长度，如果超过就需要扩容，而扩容会导致某次操作的时间成本飙升，这在实时或者交互式应用中可能会是一个严重的缺陷 

2、使用探测序列，有可能其计算的时间成本过高，导致哈希表的处理性能降低 

3、由于记录是存放在桶数组中的，而桶数组必然存在空槽，所以当记录本身尺寸（size）很大并且记录总数规模很大时，空槽占用的空间会导致明显的内存浪费 

4、删除记录时，比较麻烦。比如需要删除记录a，记录b是在a之后插入桶数组的，但是和记录a有冲突，是通过探测序列再次跳转找到的地址，所以如果直接删除a，a的位置变为空槽，而空槽是查询记录失败的终止条件，这样会导致记录b在a的位置重新插入数据前不可见，所以不能直接删除a，而是设置删除标记。这就需要额外的空间和操作

**set底层实现**

set的去重是通过两个函数__hash__和__eq__结合实现的。
1、当两个变量的哈希值不相同时，就认为这两个变量是不同的
2、当两个变量哈希值一样时，调用__eq__方法，当返回值为True时认为这两个变量是同一个，应该去除一个。返回FALSE时，不去重　　
所以，如果想要将对象用set去重，需要重写__eq__和__hash__两个方法。 

## 实现一个基于链表法解决冲突问题的散列表

```python
class _ListNode(object):
    def __init__(self,key):
        self.key=key
        self.next=None
class HashMap(object):
    def __init__(self,tableSize):
        self._table=[None]*tableSize
        self._n=0  #number of nodes in the map
    def __len__(self):
        return self._n
    def _hash(self,key):
        return abs(hash(key))%len(self._table)  ### keypoint
    def __getitem__(self,key):
        j=self._hash(key)
        node=self._table[j]
        while node is not None and node.key!=key :
            node=node.next
        if node is None:
            raise KeyError,'KeyError'+repr(key)
        return node       
    def insert(self,key):
        try:
            self[key]
        except KeyError:
            j=self._hash(key)
            node=self._table[j]
            self._table[j]=_ListNode(key)
            self._table[j].next=node
            self._n+=1
    def __delitem__(self,key):
        j=self._hash(key)
        node=self._table[j]
        if node is not None:
            if node.key==key:
                self._table[j]=node.next
                self._-=1
            else:
                while node.next!=None:
                    pre=node
                    node=node.next
                    if node.key==key:
                        pre.next=node.next
                        self._n-=1
                        break
```



## 实现一个 LRU 缓存淘汰算法

LRU(Least Recently Used)，直译为“最近最少使用”，其实称“最久未被使用”更为恰当。这是一个非常重要的算法，在学操作系统的时候第一次遇见，在做leetcode的时候再次遇见，知道是用于做缓存的页面置换。但是LRU不仅仅用于这一个用途，凡是有数据更新策略的应用，LRU都可以是候选算法。比如redis、memcached、oracle等缓存和数据库、或在其它应用场景方面也有类似的需求。总之要达到的目的是：保持新鲜，剔除陈旧，减少交换。 

lru缓存的主要操作有两个，一个是get，获取数据是否在cache中，如果在，则把该数据放到缓存最前面；另一个的主要操作是set，在缓存中存放某个值，并且存放到最前面，如果缓存中有这个值，则更新，如果缓存满了，则删除缓存中最后面的值。总之，缓存中最前面的值是最近被使用过的，缓存有大小限制，超出要删除最久未被使用的值。要求所有操作时间复杂度均为o(1)。 

直觉看上去，数据从近到远以此排列，这是一个线性结构，列表（顺序线性表/数组），链表，队列，栈？ 
　　先考虑目的要求，要求最近使用的在最前，最久未使用的在最后，队列是FIFO的结构，栈是FILO的结构，都不符合要求。 
　　再考虑数据更新，要求o(1)复杂度下，把数据更新到最前面。列表被排除，无法满足要求。只有链式结构才可以在o(1)下完成更新。 
　　最后考虑数据查找，链式结构下，数据查找复杂度为o(n)，又不能满足o(1)的复杂度。看来必须依赖其它数据结构的辅助。 
　　用列表完成o(1)的更新，不可能啊，想想看，将数组中一个位置的数挪到最前面，那这个位置之前的数据都要后移一位，怎样都不能实现o(1)的开销。那就考虑链式结构下如果实现o(1)的查找吧。通常情况下，查找链表中一个位置上的值，需要从头结点开始，依次后移查找。如果有尾结点也一样依次向前移动，时间复杂度为o(n)。那么我们能不能将每个结点的位置记下来，直接去存放结点的那个位置查找呢？哈希表派上了用场。哈希表存放结点位置的对应关系，能够满足o(1)下的数据查找，同时链表能够实现o(1)的数据更新，符合预想的要求。 
　　总结：在Cache结构中，需要一个hash table，用于存放位置关系，需要一个链表，用于更新数据，链表我们使用双向链表。大体结构为：

```python
class Node(object):
    def __init__(self, key, value):
        self.key = key
        self.value = value
        self.prev = None
        self.next = None

class TwoWayLinkedList(object):
    def __init__(self):
        self.head = None
        self.tail = None
        self._length = 0
    def isEmpty(self):
        return self._length == 0

    @property
    def length(self):
        return self._length

    def insert_head(self, node):
        head = self.head
        self._length += 1
        if head:
            node.next = head
            head.prev = node
            self.head = node
            node.prev = None
        else:
            self.head = self.tail = node
            node.prev = node.next = None
            return

    def pop(self):
        tail = self.tail
        if tail:
            self._length -= 1
            prev = tail.prev
            self.tail = prev
            tail.prev = None
            if prev:
                prev.next = None
            else:
                self.head = None
            return tail
        else:
            return None

    def remove_node(self, node):
        if not node:
            return
        if node == self.head and node == self.tail:
            self.head = self.tail = None
        elif node == self.head:
            next = node.next
            self.head = next
            next.prev = None
            node.next = None
            return
        elif node == self.tail:
            prev = node.prev
            self.tail = prev
            prev.next = None
            node.prev = None
        else:
            node.prev.next = node.next
            node.next.prev = node.prev
```



```python
class LRU_Cache(object):
    def __init__(self, capacity):
        self.capacity = capacity
        self.size = 0
        self.table = {}
        self.linklist = TwoWayLinkedList()

    def get(self, node):
        key = node.key
        if key in self.table:
            node = self.table[key]
            self.linklist.remove_node(node)
            self.linklist.insert_head(node)
            return node
        else:
            return None

    def set(self, node):
        key = node.key
        if key in self.table:
            _node = self.table[key]
            self.table[key] = node
            self.linklist.remove_node(_node)
            self.linklist.insert_head(node)
        else:
            if self.size == self.capacity:
                _node = self.linklist.pop()
                del self.table[_node.key]
                self.size -= 1
            self.table[key] = node
            self.linklist.insert_head(node)
            self.size += 1　　
```
## leetcode 两数之和(1)

给定一个整数数组 nums 和一个目标值 target，请你在该数组中找出和为目标值的那两个整数，并返回他们的数组下标。
你可以假设每种输入只会对应一个答案。但是，你不能重复利用这个数组中同样的元素。
示例：
给定 nums = [2, 7, 11, 15], target = 9
因为 nums[0] + nums[1] = 2 + 7 = 9
所以返回 [0, 1]

```python
#把key值映射到哈希表中一个位置来访问，时间复杂度是O(N)。
class Solution:
    def twoSum(self, nums, target):
        nums_hash = {} 
        for i in range(len(nums)):
            dif = target - nums[i]
            if dif in nums_hash: 
                return [nums_hash[dif], i]
            nums_hash[nums[i]] = i
        return []
```

## Happy  Number(202)

编写一个算法来判断一个数是不是“快乐数”。
一个“快乐数”定义为：对于一个正整数，每一次将该数替换为它每个位置上的数字的平方和，然后重复这个过程直到这个数变为 1，也可能是无限循环但始终变不到 1。如果可以变为 1，那么这个数就是快乐数。

map返回list求和。sums = sum(map(lambda x:x**2,map(int,str(n)))) # lambda是一个匿名函数，外层map(a,b,b2,…)是一个模型，a是一个函数，b，b2…是可迭代对象，map本身就是可迭代对象。内层map(int,str(n))的意思是，将一个数转为字符串后，就变成了一个map迭代对象。即理解为，假设输入为19,此时为一个list=[1,9]。外层的map(labda函数…,map()),做的工作就是list=[1平方,9平方]，然后在sum(list)最后1平方+9平方得到82。

之后寻找快乐数，sums == 1 即True。另外无限循环，每次sums都存在hash字典里，所以只要后面有sums重复在字典里，即False。然后不断迭代。(hash即dict使用)

```python
class Solution:
    def __init__(self):
        self.hash_dict = {}
    def isHappy(self, n):
      	sums = sum(map(lambda x:x**2,map(int,str(n))))	#实现一个数round拆分平方求和
        if sums == 1:
            return True
        if sums in self.hash_dict:
            return False
        else:
            self.hash_dict[sums] = 0
        return self.isHappy(sums)
```

## 参考博客

[这篇写的是真好啊,下次再看的时候一定要再翻一遍啊啊！](https://www.cnblogs.com/wuhuangdi/p/4175991.html)

[hash性能优化](https://blog.csdn.net/zhaozhenzuo/article/details/35207485)