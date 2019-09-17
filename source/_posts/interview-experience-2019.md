---
title: 2019面经
date: 2019-06-18 23:51:51
tags:
categories:
---
面经整理～～
<!--more-->
58金融
---

> [另一份](/images/58-interview.png)

- 微服务
  - 谈谈你理解的微服务 [什么是微服务](https://www.cnblogs.com/hongxf1990/p/6491014.html)---[微服务的来龙去脉](https://www.cnblogs.com/wangiqngpei557/p/6936136.html)---[什么是微服务架构](https://www.zhihu.com/question/65502802)
  - 服务的划分依据
  - 一次dubbo的完整调用过程 [[1]](https://www.cnblogs.com/aspirant/p/9002663.html) -- [[2]](https://www.jishuwen.com/d/2Hpf)
- mysql
  - 千万级别的数据，数据库需要考虑什么 [[1]](https://www.zhihu.com/question/19719997) --- [[2]](https://www.cnblogs.com/yycc/p/7518240.html)
  - 数据库分库分表需要注意什么 [[1]](https://www.infoq.cn/article/key-steps-and-likely-problems-of-split-table)--- [[2]](https://database.51cto.com/art/201809/583857.htm)
  - [索引需要考虑什么](https://yezhwi.github.io/java/2018/04/08/MySQL%E7%B4%A2%E5%BC%95%E5%8F%8A%E6%B3%A8%E6%84%8F%E4%BA%8B%E9%A1%B9/)
  - 最左原则
  - [说说B+树和B树](https://www.cnblogs.com/ivictor/p/5849061.html)
  - 讲讲幻读和不可重复读
  - 讲讲数据库的乐观锁和悲观锁
- redis
  - [redis持久化](https://segmentfault.com/a/1190000002906345)
- 队列
  - kafka为什么比mq快--[kafka为什么那么快](https://www.cnblogs.com/duanxz/p/4705164.html)
    - 生产者: 顺序写入、内存映射文件(同步或异步更新到磁盘)
    - 消费者: 全部以文件方式发送到消费者
- zookeeper
  - [分布式锁](https://www.cnblogs.com/garfieldcgf/p/6380816.html)
  - [zookeeper选举策略，双数节点可以选举成功吗](https://www.cnblogs.com/ASPNET2008/p/6421571.html)
- HashMap
  - HashMap结构，为什么是数量是8转为红黑树(总结)
  - [说说红黑树](https://www.cnblogs.com/skywang12345/p/3245399.html)
  - 怎么保证HashMap线程安全
- 线程
  - 线程生命周期，什么时候发生线程状态变为死亡--[线程的生命周期](https://www.cnblogs.com/sunddenly/p/4106562.html)
  - 讲讲CAS和AQS
  - [ThreadLocal结构，与线程池一起使用会有什么问题吗？](https://www.cnblogs.com/qifenghao/p/8977378.html)
    - 线程池中的线程在任务执行完成后会被复用，所以在线程执行完成时，要对 ThreadLocal 进行清理（清除掉与本线程相关联的 value 对象）。不然，被复用的线程去执行新的任务时会使用被上一个线程操作过的 value 对象，从而产生不符合预期的结果。
- jvm
  - 讲讲你熟悉的GC收集器，特点，你们用的哪个
  - jvm调优怎么做
- 排序
  - 快排
  
瓜子
---

#### 第一轮
- coding:
  - 文件夹后续遍历: 先打印文件然后追层向上打印
  - 无序数组，取出top N的N个元素
- ConcurrentHashMap 在JDK8的优化
- jvm内存结构

#### 第二轮

- 消息中间件的作用
- 并发
  - 为什么产生锁
  - threadlocal源码
- 数据库
  - mysql 索引
  - redis 雪崩　击穿
- jvm
  - 内存划分
  - 新建对象放在哪里 (堆和栈)
  - [G1收集器](https://zhuanlan.zhihu.com/p/52841787)
  - 增加吞吐量需要怎么设置参数 [jvm调优](https://zhuanlan.zhihu.com/p/57014847)
- 优化分析
  - 接口变慢可能是什么原因？怎么优化?
  - dubbo的限流
- 分布式
  - 接口幂等的实现方式
  - 分布式锁

洋钱罐
---

- 简单的sql手写(join)
- mysql事务隔离级别
- redis怎么用的
#### 算法手写
二叉树根节点到叶子的数据和的最大值(广度优先,每一级存当前与上一节点的和，叶子节点就是总和)


vipkid
---

- 项目遇到的难点
- redis cluster写热点key问题
- 一个线程池，3个线程执行完成就继续执行下去，写代码(CountdownLatch)
- 算法手写
    - 字符串反转
    - 和为n的连续正整数序列
    - 单链表反转
- 等等

转转
---

zk分布式锁的临时节点如何释放的  
select for update是什么锁

```
select from where id = ''
select from where name = ''
```

行锁还是表锁？  
触发不到索引的查询有哪些
水平分表,同时满足订单id,卖家id和卖家id分别查询  
dubbo为什么用代理(方法的多态,消费组)  
线程池参数  
算法
- 用map,  list, array, stack 实现LRU cache
- 反转单链表
- 不用Java Api实现字符串转整型
- 足够量大的动物的动物园有一种动物是一只的，其他种类是两只的，找出这种动物

知乎
---

- spring　aop ioc, 动态代理中jdk和clib的区别  
- redis线程模型为什么快(NIO)  
- redis分布式锁,多线程，某个线程阻塞超时，重新执行时锁已被其他获取，自己删除别人的这个锁(怎么解决标记锁所属线程，保证原子性)
- dubbo的执行过程, 代理监听和执行这中间做了什么
- HashMap为什么线程不安全(rehash时链表成环)
- ConcurrentHashMap怎么做到的分段锁，怎么得到的当前的大小
- 线程池参数
- AQS怎么做的线程放入队列，释放锁唤醒队列线程(源码细节)
- CAS
- mysql事务隔离级别
- 索引结构，　B+树数据全部存在叶子节点的好处(B树也可以把数据连起来啊)
- 回表,覆盖索引

新浪金融
---
算法
- leetcode 821题
- 斐波纳契数列(非递归实现)
- 二分查找
- ArrayList删除指定元素(考虑越界问题)
- 懒加载单例

java内存模型，垃圾回收策略，jvm调优(堆为什么也不能设置很大？)  
Spring AOP, IOC原理？ JDK动态代理和cglib的区别？ Spring事务嵌套？  
Mybatis如何防止SQL注入的  
dubbo生产者和消费者与zk的流程，负载均衡是在哪配置的？  
设计模式有哪些  
事务的特性ACID  
HashMap的存储结构，put的操作过程  
sleep和wait的区别  
synchronized用在不同方法的情况  
分布式锁  
讲讲你熟悉的框架，技术    

马蜂窝
---
- 字节码加载到卸载的全过程
- 新生代不够的情况，对象怎么分配
- 现在jvm内存设置的多少
- 业务的并发量(吞吐量)
- 并发用到过哪些地方
- mysql的索引 B与B+树
- 聚簇索引和非聚簇索引

京东
---

#### 一面

- http报文
- Http协议有哪些
- 传输
- kafka发送文件方式（同步，异步，批量）
- kafka架构
- Kafka遇到的问题解决
- 什么场景用的消息队列
- Redis用过哪些数据结构
- Dubbo的调用过程
- Dubbo通信协议，连接协议
- 集合用过哪些
- Hashmap与concurrenthashmap
- 锁有哪种，共享，独享
- 哪些类是共享，独享
- Mysql索引考虑什么
- 什么情况应用不到索引
- 表锁行锁全局锁，怎么加
- 限流怎么做的，原理
- 本地锁与分布式锁
- Zk与redis实现的区别
- jvm参数调优
- 哪些情况cpu升高
- 什么情况出现线程安全问题
- 内存划分
- 垃圾回收
- 用的什么回收器，为什么，特点
- 你们系统QPS多少，用户量
- [final变量为什么在使用前必须要进行初始化](http://www.voidcn.com/article/p-wftxeait-bqt.html)

#### 二面和三面

- dubbo调用其他系统时，被调用系统挂掉如何不影响调用方（降级）
- dubbo调用过程经过zookeeper吗，一个机器挂了，没有更新的调用方本地缓存，怎么办？
- dubbo限流有没有做过？
- mysql redis mongo使用场景
- mongo的锁问题有没有遇到过？（数据量大时出现）
- redis的数据结构，一个班学生用什么结构存
- innodb和myisam区别
- 用过哪些索引
- 索引创建需要考虑哪些
- 关于简历里的授权系统和联合登陆
- zookeeper和redis分布式锁，量大时zk文件节点的创建和销毁性能与redis重复尝试加锁的性能消耗？

#### 第四面
- 遇到过那些难题
- Mysql遍历量很大的数据，怎么查
- 这家公司学到了什么
- dubbo原理，具体执行过程都做了什么

蛋壳
---
- redis与mysql双写一致性问题
- 分布式锁应用场景
- 幂等性问题
- 栈与队列基于基础结构实现(数组/单链表/双链表)
- 手写排序，单例
- innodb与myisam的索引的区别
- 做过哪些sql优化
- 内存模型，volatile原理
- kafka架构模型
- redis线程模型，持久化，集群为什么设置16384个槽，怎么增加节点
- 了解哪些限流算法