---
title: Redis五种数据类型
date: 2019-04-16 22:19:38
categories: 数据库
---
Redis五种数据类型的差异，适用场景等
<!--more-->

不同数据结构的差异在于value的结构不一样

|类型|对于Java|结构|应用|
|-|-|-|-|
|string(字符串)|ArrayList|动态字符串提前扩容|存储序列化的对象|
|list(列表)|LinkedList|链表|可做队列、栈；保存最新的10个用户，进来一个就删除一个，保持最新用户|
|hash(字段)|HashMap||可以分别的存用户的信息|
|set(集合)|HashSet|value为null的HashMap|存中奖用户id，因为不可重复的特点|
|zset(有序集合)|SortedSet和HashMap的结合|value有权重值的set|可以value为用户id，score为分数，按分数对用户排序操作(zrange <name> 0 -1)|

#### string(字符串)
- 相当于Java中的ArrayList
- redis中的字符串是动态字符串，是可以修改的字符串
- 动态分配内存
	- 字符串长度小于１MB时，扩容是加倍现有空间；
	- 字符串大于１MB时，每次增加１MB;
	- 字符串最大长度是512MB
- incr最大值是Long.Max 
- 保存完整序列化对象
#### list(列表)
- Java中的LinkedList
- 可做队列: 右push + 左pop
- 可做栈: 右push  + 右pop
- zipList: 数量较少时，连续的地址空间存储，节约头尾指针的内存空间
- 应用: 保存最新的10个用户，进来一个新的就删除一个旧的；得到最新10个用户
#### hash(字典)
- Java中的HashMap
- 单独存储用户信息的每个字段
#### set(集合)
- 中奖用户id，不可重复
Java中的HashSet
#### zset(有序列表)
- value带有权重的set
