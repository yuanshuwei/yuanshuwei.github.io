---
title: 剑指Java面试知识点笔记
date: 2019-06-20 20:16:50
tags:
categories: 面试
---
剑指面试视频，笔记
<!--more-->
Redis
---

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

`zcan cursor [pattern] [count]`
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

**哨兵(Sentinel)**

- 监控主从是否运行正常
- 提醒管理员发生故障通知
- 主从切换(主观下线和客观下线)，多个哨兵使用(流言协议)gossip协议同步信息，并投票觉得是否故障迁移，类似zookeeper

### redis集群

- 数据分片
- 不同的key放在不同的节点，分片存储。
- 这样就需要寻址，开始时是hash值与节点数去模，但动态增加或减少节点时或导致大量的节点无法被命中
- 然后出现了一致性hash算法
对2的32次方取模,将哈希值空间组织成虚拟的圆环。　宕机和增加节点之后影响逆时针的第一个节点的数据。；　　
当节点数很少时，会出现数据倾斜问题，可以把一个节点设置为分布分散在环上的多个节点，使最终的数据分布尽量均匀


### Shell

- 文件检索: find
```
find ~ -name "build.sh"　#　精确查找


find ~ -name "build.*"　# 通配符

find ~ -iname "build.sh"  # 忽略大小写

man find
```

- 文件内容检索: grep

  - grep [options] pattern file

    ```
    grep "moo" target* # 查询包含内容"moo"并且文件名以target开头的文件和目标字段串所在的行的内容 
    ```
  - `grep "ss" ss.log`  查找包含你指定内容的行，并打印你呢
  - `grep -o` 只显示想要的数据部分
  - `grep -v` 过滤掉目标数据
- 管道操作符 |  输出作为后面的输入    
  ```
  find ~ | grep "target"
  ```
- 文件呢内容的统计: awk
```
awk [options] 'cmd' file ＃ 特别适合表格类数据，统计等
awk '{print $1,$4}' netstat.txt #打印第一行和第四行, awk默认以空格分割
```
- 批量替换文本内容: sed
```
sed -i 's/^Str/String/' xx.java #Str开头的Str替换为String
sed -i '/\.$/\;/' xx.java #.结尾的替换为;
sed -i '/Jack/me/g' xx.java ＃Jack替换成me，g是整行替换
```
