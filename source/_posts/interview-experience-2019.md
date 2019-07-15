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
  - 谈谈你理解的微服务
  - 服务的划分依据
  - 一次dubbo的调用过程
- mysql
  - 千万级别的数据，数据库需要考虑什么
  - 数据库分库分表需要注意什么
  - 索引需要考虑什么
  - 最左原则
  - 说说B+树和B树
  - 讲讲幻读和不可重复读
  - 讲讲数据库的乐观锁和xx锁
- redis
  - redis持久化
- 队列
  - kafka为什么比mq快
- zookeeper
  - 分布式锁
  - zookeeper选举策略，双数节点可以选举成功吗
- HashMap
  - HashMap结构，为什么是8转为红黑树
  - 说说红黑树
  - 怎么保证HashMap线程安全
- 线程
  - 线程生命周期，什么时候发生线程状态变为死掉
  - 讲讲CAS和AQS
  - ThreadLocal结构，与线程池一起使用会有什么问题吗？
- jvm
  - 讲讲你熟悉的GC收集器，特点，你们用的哪个
  - jvm调优怎么做
- 排序
  - 快排
- 爬虫难点
  
瓜子
---

#### 第一轮
- coding:
  - 文件夹后续遍历: 先打印文件然后追层向上打印
  - 无序数组，取出top N的N个元素
- ConcurrentHashMap JDK8的优化
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
  - 新建对象放在哪里
  - G1收集器
  - 增加吞吐量需要怎么设置参数
- 优化分析
  - 接口变慢可能是什么原因？怎么优化?
  - dubbo的限流
- 分布式
  - 接口幂等的实现方式
  - 分布式锁

洋钱罐
---

- 简单的sql(join)
- mysql事务隔离级别
- redis怎么用的
#### 算法手写
二叉树根节点到叶子的数据和的最大值(广度优先,没一级存当前与上面的和，叶子节点就是总和)

vipkid
---

- 项目遇到的难点
- redis cluster写热点key问题
- 一个线程池，3个线程执行完成就继续执行下去
- 算法手写
    - 字符串反转
    - 和为n的连续正整数序列
    - 单链表反转

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
水平分表,满足订单id,卖家id和卖家id查询  
dubbo为什么用代理(方法的多态,消费组)  
线程池参数
#### 算法
- 用map,  list, array, stack 实现LRU cache
- 反转单链表
- 字符串转整型
- 足够量大的动物的动物园有一种动物是一只的，其他种类是两只的，找出这种动物

知乎
---

- spring　aop ioc, 动态代理中jdk和clib的区别  
- redis线程模型为什么快(NIO)  
- redis分布式锁,多线程，某个线程阻塞超时，重新执行时锁已被其他获取，自己删除别人的这个锁
- dubbo的执行过程, 代理监听和执行这中间做了什么
- HashMap为什么线程不安全(rehash时链表成环)
- ConcurrentHashMap怎么做到的分段锁，怎么得到的当前的大小
- 线程池参数
- AQS怎么做的线程放入队列，释放锁唤醒队列线程
- CAS
- mysql事务隔离级别
- 索引结构，　B+树数据全部存在叶子节点的好处
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