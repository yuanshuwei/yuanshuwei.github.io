---
title: redis笔记
date: 2019-06-30 21:59:49
tags:
categories:
---
redis笔记
<!--more-->

#### 与Memcache区别
- memcache支持简单数据类型
- 不支持持久化
- 不支持主从同步
- 不支持分片
- Redis数据类型丰富
- 支持持久化存储
- 支持主从
- 支持分片

#### Redis很快
- 基于内存
- 数据结构简单，不设计关联
- 单线程处理高并发，多核也可启动多实例
- 多路I/O复用模型，非阻塞IO

#### IO复用模型
- select系统调用
- 因地制宜选择IO多路复用函数
- 优先选择时间复杂度O(1), O(n)的select作为保底
- 基于react设计模式监听IO事件

### 常见数据类型

- String: 二级制安全的
```
> set k v
> get k
> incr k
```
- Hash: 类似map结构
```
> hmset lilei name "lilei" age 26 title "Senior"
> hget lilei name
> hset lilei title "Pricipal"
```
- List： 有序列表
```
> lpush mylist aaa
> lpush mylist bbb
> lpush mylist ccc
> lrange mylist 0 10
1) "ccc"
2) "bbb"
3) "aaa"
```
- Set: 不重复的无序集合，哈希表时间，复杂度是O(1)
```
> sadd myset 111
> sadd myset 222
```
- Sorted Set: 按权重排序的不重复集合, 小到大排序
```
> zadd myset 3 aaa
> zadd myset 1 bbb
> zadd myset 2 ccc
> zrangebyscore myset 0 10
```

### 海量数据里查询某一前缀的key

> 摸清边界，数据量

Keys指令对于线上数据量很大，对于内存的消耗和redis服务器都是隐患

`scan cursor [pattern] [count]`
- 基于游标的迭代器，需要基于上一次的游标延续之前的迭代过程
- 以0开始新一次的迭代，知道命令返回游标0完成一次遍历
- 不保证每次执行都返回某个给定数量的元素，支持模糊查询
- count只是大概的参数

```
scan 0 match k1* count 10
```

### 分布式锁

需要解决的问题：
- 互斥性: 同一时间只能一个线程获取到锁
- 安全性: 锁只能被持有锁的线程删除
- 死锁: 过期时间问题，获取到锁宕机问题
- 容错: redis部分宕机，需要还能继续获取到锁

SETNX 解决：
```
SETNX K V 
```
不存在则创建成功，已存在则创建失败，v可以存当前线程id或者机器id；  
还需要设置过期时间， `expire k [时间]`； 但两个命令不能保证原子性；可能创建成功没设置过期时间就宕机了，此时这个锁一直不能被其他线程获取到。于是redis提供了另外的一个命令:

```
set k v [EX second] [PX milliseconds] [NX|XX]
```
- EX second: 设置键的过期时间为second秒
- PX milliseconds： 设置键的过期时间是毫秒
- NX： 键不存在时，才对键进行设置操作

### Redis异步队列

```
> rpush mylist aaa
> rpush mylist bbb
> rpush mylist ccc
> lpop mylist
> aaa
```
- 缺点: 没有等待队列里有值就直接消费
- 弥补: 可以在应用层引入sleep机制去调用LPOP重试

不用sleep的话，直接用BLPOP，没有消息的时间就阻塞，超时后再返回。 `blpop testlist [超时时间]`

#### 主题订阅模式， pub/sub

- 发生消息，接收消息
```
> subscribe mytopic
> subscribe mytopic
> publish mytopic "hello"
```
这种无法保证可达，会丢失。

### 数据持久化

- RDB: 快照,每隔一段时间保存当前全量数据，多个文件；速度快文件小，相对容易丢失
- AOF: 保存所有写命令,保存命令；数据不易丢失。文件体积大，恢复时间长
- RDB-AOF混合 持久化方式(redis4.0后)： RDB保存全量数据-AOF保存增量数据
  
同时存在AOF和RDB文件，的数据恢复： 重启redis即可。优先加载AOF文件

###　pipeline

- 与linux的管道类似
- redis基于请求响应模式，单个请求处理需要一一应答
- pipeline批量执行指令，节省多次IO往返时间

### 主从复制

主从同步，从从同步。

**全量同步**
- slave发送sync命令到master
- master启动一个后台进程,将redis的数据快照保存在文件中
- master把文件发送给slave，slave对AOF文件加载

**增量同步**
- master的写命令存在AOF中,　将命令数据写入缓存然后发生给slave



### 整理

- 作用:高性能，高并发；　
- 雪崩: 宕机的处理
    - 事前：主从复制＋哨兵，redis　cluster维持高可用
    - 事中，限流，降级，增加本地缓存
    - 事后: redis持久化，快速恢复数据
- redis线程模型:  它采用 IO 多路复用机制同时监听多个 socket，将产生事件的 socket 压入内存队列中，事件分派器根据 socket 上的事件类型来选择对应的事件处理器进行处理。多个socket,IO多路复用，文件时间分派器，事件处理器
- redis效率高: 纯内存操作,IO多路复用的非阻塞IO,Ｃ语言实现,单线程避免上下文切换问题,单线程处理高并发，多核也可启动多实例
- 数据类型: string,hash,list,set,sorted sort
- 过期策略: 定时删除(默认100ms抽一部分有过期时间的key)＋惰性删除(用到时检查)＋内存淘汰机制(常用LRU, 空间不足时，删除最近最少使用的key)
- 持久化方式: RDB和AOF,RDB周期性保存全量的数据备份，AOF保存全部的写命令。
  - **RDB**适合做冷备份，对外读写服务影响小，保持高性能。但每隔5分钟甚至更长会导致丢失数据。　
  - **对于AOF**：每个１秒执行，适合误删除的紧急恢复，同一份数据AOF更大，恢复较慢，命令回访恢复数据，不够健壮。
  - **一般的方式**:综合两种方式，AOF保证数据的不丢失，作为第一选择；RDB做不同程度的冷备份。
- 主从复制: 
  - **第一次从连接主时**: 从发送命令`PSYNC`,主生成RDB文件发送给从，并在内存中缓存RDB生成后的所有写操作，当从加载完RDB文件，主将缓存中的写命令发送给从,完成数据的同步。
  - **主从复制断点续传**: redis2.8以后，主节点在内存中维护一个backlog,主和从都会保存一个replica offse还有一个run id,offset就是保存在backlog中,如果主从断开连接，从会让主从上次replica offset继续复制
- 哨兵: 
  - **作用**: 集群监控(监控主从节点是否正常)、消息通知(每个redis故障则通知给管理员)、故障转移(主从切换)、配置中心(故障发生，通知cilent客端新的主节点的地址)。哨兵本身也是分布式的。　
  - **自动发现机制**: 哨兵通过sub/pub,每个哨兵都向`__sentinel__:hello`这个channel发消息，同步自己的host,ip,runid还有对master的监控。来感知其他哨兵的存在，互相同步各自监控信息和配置信息。
  - **主从切换步骤**: 每个哨兵ping master,超过指定时间就认为宕机，此时是主观宕机；当哨兵集群中超过一半都认为master宕机了，此时到达了客观宕机，可以进行主从切换　
  - **选举算法**: ①跟master断开时长②slave优先级③复制offset(复制数据更多的那个)④run id(大小排序)
- 集群(redis cluster): 
  - 16384个槽，哪个master负责哪个槽用户可以指定。所有键都会被直派到具体的槽:CRC64(key)%16384,也就是key的校验和对16384取余得到
  - 每个节点有自己看来整个集群的状态，包括某个槽对应哪个节点
  - 当客户端向集群中任一节点发送与数据库键有关的命令时，接收命令的节点会计算出命令要处理的数据库键属于哪个槽（CRC16(key) & 16383），并检查这个槽是否指派给了自己： 
    - ·如果键所在的槽正好就指派给了当前节点，那么节点直接执行这个命令。 
    - ·如果键所在的槽并没有指派给当前节点，那么节点会向客户端返回一个MOVED错误，指引客户端转向（redirect）至正确的节点，并再次发送之前想要执行的命令。
  - [图解redis集群原理](https://blog.csdn.net/yejingtao703/article/details/78484151)
  - [原理](https://whetherlove.github.io/2018/10/07/Redis%E9%9B%86%E7%BE%A4-Cluster%E5%AE%9E%E7%8E%B0%E5%8E%9F%E7%90%86/)
  - [原理2](http://shanks.leanote.com/post/Redis-Cluster)

手写LRU　[参考](https://www.cnblogs.com/WJ5888/p/4371647.html)
```主和从都会保存一个replica offse还有一个run id,offset 
class LRUCache<K, V> extends LinkedHashMap<K, V> {
    private final int CACHE_SIZE;

    /**
     * 传递进来最多能缓存多少数据
     *
     * @param cacheSize 缓存大小
     */
    public LRUCache(int cacheSize) {
        // true 表示让 linkedHashMap 按照访问顺序来进行排序，最近访问的放在头部，最老访问的放在尾部。
        super((int) Math.ceil(cacheSize / 0.75) + 1, 0.75f, true);
        CACHE_SIZE = cacheSize;
    }

    @Override
    protected boolean removeEldestEntry(Map.Entry<K, V> eldest) {
        // 当 map中的数据量大于指定的缓存个数的时候，就自动删除最老的数据。
        return size() > CACHE_SIZE;
    }
}
```
