---
title: redis笔记
date: 2019-06-30 21:59:49
tags:
categories:
---
redis笔记
<!--more-->

- 作用:高性能，高并发；　雪崩: 宕机，事前：主从复制＋哨兵，redis　cluster维持高可用，事中，限流，降级，增加本地缓存，事后: redis持久化，快速恢复数据
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
- 集群: 
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
