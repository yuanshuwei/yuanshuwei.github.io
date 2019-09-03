---
title: Java集合总述
date: 2018-08-28 15:39:19
categories: Java
---
整体总结概述各集合特点，底层数据结构
<!--more-->
#### 结构
- Collection
	- **List**
		- `* ArrayList`: 
		Object数组实现，每次扩容为原来的1.5倍；非线程安全；
		- `Vector` :  
		Object数组实现，相对ArrayList，用synchronized关键字加锁，实现线程安全；
		- `* LinkedList`: 
		双向循环链表；非线程安全；
	- **Map**
		- `* HashMap`: 
		存储<K,V>对象，用K计算的hash值匹配到bucket，K.equals()定位具体的<K,V>对象来找到value；负载因子0.75，也就是说容量达到75%时扩容，初始16，每次扩容变为原来的2倍。
		- `Hashtable`: 
		遗留类，基本被淘汰，放入null会报错。它是在HashMap基础加了synchronized关键字，所以并发性不好；替代的推荐用ConcurrentHashMap
		- `* LinkedHashMap`: 
		HashMap的一个子类；链表实现
		- `* TreeMap`：
		TreeMap实现SortedMap接口，保存的是按key排序的，默认是按key升序排序，也可以指定排序；红黑树
		- `* ConcurrentHashMap`: 
		JDK1.8 中取消了Segment分段锁，采用CAS和synchronized来保证并发安全。数据结构跟HashMap1.8的结构类似，数组+链表/红黑二叉树。不允许键值null；
	- **Set** 
		- `* HashSet`: 
		无序，唯一；基于 HashMap 实现的，底层采用 HashMap 来保存元素；判断对象重复的方式与HashMap判断K的hash然后equals一致；
		- `* TreeSet`：
		有序，唯一；红黑树。基于TreeSet
		- `LinkedHashSet`：
		基于LinkedHashMap实现

>推荐: [源码学习](https://github.com/Snailclimb/JavaGuide)
#### HashMap分析 
- 允许键值为null；  
- 非线程安全；如果需要满足线程安全，可以用 Collections.synchronizedMap()方法使HashMap具有线程安全的能力，或者使用ConcurrentHashMap。  
- JDK1.7前，以数组+链表的链表数组存储，数组是主体，链表是为了解决hash冲突的存在。存储的结构是<K,V>的实体，以K的hash值定位到bucket，发生hash冲突是通过K.equals()找到需要的实体部分。  
- JDK1.8，当链表长度>8时，链表转换为红黑树，当链表长度<6，红黑树转为链表。另外改进了hash计算方法，使得分布更加均匀(右移16位相异或，使得高位和低位的特征都存在，hash碰撞率低)
- 负载因子过大，则链表过长；负载因子过小，则hash桶过多，空间复杂度过高

##### 为什么HashMap长度是2的N次？
```HashMap为了存取高效，要尽量较少碰撞，就是要尽量把数据分配均匀，每个链表长度大致相同，这个实现就在把数据存到哪个链表中的算法；
这个算法实际就是取模，hash%length，计算机中直接求余效率不如位移运算，源码中做了优化hash&(length-1)，
hash%length==hash&(length-1)的前提是length是2的n次方；
为什么这样能均匀分布减少碰撞呢？2的n次方实际就是1后面n个0，2的n次方-1  实际就是n个1；
例如长度为9时候，3&(9-1)=0  2&(9-1)=0 ，都在0上，碰撞了；
例如长度为8时候，3&(8-1)=3  2&(8-1)=2 ，不同位置上，不碰撞；
```

##### HashMap put方法逻辑
- 如果未初始化过，则初始化
- 对key求hash，然后计算下标
- 如果没有碰撞，直接放入桶中
- 如果碰撞了，已链表方式链接到后面
- 如果链表长度超过阈值，链表转为红黑树
- 如果节点已存在就替换旧值
- 如果桶满了就resize(扩容2倍后重排)

##### 为什么8节点变为红黑树？
- [n/2与log(n)](https://blog.csdn.net/xingfei_work/article/details/79637878)
- [概率统计得到](https://www.javazhiyin.com/34651.html)


#### ConcurrentHashMap
[ConcurrentHashMap size方法](https://juejin.im/post/5ae75584f265da0b873a4810)

##### ConcurrentHashMap put过程
1. key和value任意为空，抛出异常
2. 计算key的hash值，遍历table数组
3. table为null或空，调用initTable初始化
4. 为保证可见性，table数组取数据，CAS保证可见性和线程安全性
5. 若当前桶位hash值MOVED，说明正在扩容，则帮助迁移
6. 元素不止一个，synchronized给头节点加锁，链表或红黑树的插入节点