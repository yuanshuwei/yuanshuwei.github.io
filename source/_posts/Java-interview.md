---
title: Java面试
date: 2019-04-16 22:18:41
tags: 面试
categories: 面试
---
面试中高级的一些准备提纲, `持续更新`
<!--more-->
> [其他参考](http://nivelle.me/javaInterview/)
## 消息中间件 
{% post_link message-middleware %}

## 源码
- 集合
  - HashMap底层
  - {% post_link Java-Collection %}
- 并发(synchronized ThreadLocal...)、线程池
	- [synchronized](https://juejin.im/post/5b4eec7df265da0fa00a118f)
		- 底层是通过monitor(监视器锁)对象完成的，通过软件在jvm实现
		- 同步代码块是 `monitorenter` 和 `monitorexit`完成，同步方法是通过检查`ACC_SYNCHRONIZED`标识符是否被设置
		- 实现方式是jvm在底层调用操作系统的互斥原语mutex实现，被阻塞的线程会被挂起、等待重新调度，会导致“用户态和内核态”两个态之间来回切换，对性能有较大影响。
		- 其实wait/notify等方法也依赖于monitor对象
	- {% post_link threadlocal %}
    - volatile
    	- 通过volatile修饰的变量可以保证线程之间的可见性，但并不能保证这多个指令的原子执行，在多线程并发执行下，无法做到线程安全，得到正确的结果
	- [CAS](https://juejin.im/post/5a73cbbff265da4e807783f5) / [参考2](https://www.jianshu.com/p/fb6e91b013cc)
    	- 比较，如果原始值没有被修改过，则更新成新的目标值；如果这一步的CAS没有成功，那就采用自旋的方式继续进行CAS操作，取出乍一看这也是两个步骤了啊，其实在 `JNI` 里是借助于一个 `CPU指令` 完成的。所以还是原子操作。
    	- `synchronized` 是悲观锁，假设一定会发生冲突，直接加锁操作，比较重； `CAS` 是 乐观锁，假设不会发生冲突，当大声冲突时，进行重试操作； ReenterLock内部的AQS，还是各种Atomic开头的原子类，内部都应用到了CAS
    	- 缺点：ABA问题， 用`AtomicStampedReference`, 它可以通过控制变量值的版本来保证CAS的正确性。/ `循环时间长开销大`
	- [AQS](https://juejin.im/entry/5ae02a7c6fb9a07ac76e7b70)
    	- 即队列同步器。它是构建锁或者其他同步组件的基础框架（如ReentrantLock、ReentrantReadWriteLock、Semaphore等） 
    - 偏向锁:加锁偏向经常分配到的那个线程
    - ReentrantLock（公平锁\非公平锁两种实现)
	- 线程池分类，关键参数设置
- 并发的几种实现方式

## 数据库
#### MySql
- 索引 
	- mysql中a、b的联合索引。 对于单个a或b字段有效吗？
- 引擎
- 索引数据结构
- B+ Tree 原理，与其它查找树的比较。 B+ Tree 和 B Tree 的区别？
- 主从复制原理、作用、实现。
- 水平切分与垂直切分。
- 分库分表，分库分表中间件？

#### redis
- 五种数据类型 [简书](https://www.jianshu.com/p/d645cebff386) 总结[我的](https://blog.csdn.net/ysw1132/article/details/88807203)
- 分布式锁
- 与 Memchached 的比较。
- redis(socket 网络编程)
- [github总结](https://github.com/doocs/advanced-java)

## Jvm
{% post_link jvm %} //todo 完善

## 设计模式
- 手写单例
- 手写工厂模式
- 手写生产者消费者
- 动态代理

## 框架
##### dubbo
- 与springcloud的区别-服务调用方式和关注点不同
  - dubbo服务间调用采用RPC，长连接，性能会更好
  - 支持多种序列化协议
  - 不支持跨语言，自己再包装一层REST服务，相对更麻烦；服务提供和消费者强依赖
  - 分布式配置、服务追踪等，需要借助其他框架，风险大
  - 而对于springcloud，社区很活跃，提供了整套的微服务解决方案，部署快，支持REST，跨语言，提供了docker支持
  - 选择的关注点：是否存在异构系统的集成问题；功能特性是否满足；http协议的通信是否会成为瓶颈点；springcloud也不是必须http+json也可配置；dubbo需要内部有一个维护的团队
- 灰度
  当一个接口出现不兼容升级时，可以用版本号过渡，版本号不同的服务相互不调用，实现灰度发布,步骤如下:
  1. 接口旧的实现定义version="1.0.0"，接口新的实现version="2.0.0"
  2. Consumer端定义version="*"
  
  这样定义Provider和Consumer后，新旧接口实现各承担`50%`的流量；
利用dubbo该特性，还能完成不兼容版本迁移：
  在低压力时间段，先升级一半Provider为新版本；
  1. 再将所有消费者升级为新版本；
  2. 然后将剩下的一半提供者升级为新版本。
- 限流、降级
- 源码
- [掘金-肥朝](https://juejin.im/search?query=%E8%82%A5%E6%9C%9D&type=all)

##### service mesh 是什么

##### spring
- MVC
- AOP
  - 实现方式
  - 应用场景

## 分布式
- 分布式事务
- 分布式锁
- zookeeper
- 一致性hash

## 算法
- 几大排序：描述和代码实现
- 斐波那契
  
## 刷题
- 剑指offer {[参考](https://github.com/CyC2018/CS-Notes)}

## 其他
- [看看知识和岗位](https://github.com/CyC2018/)
- [简历模板](https://github.com/CyC2018/Markdown-Resume)
- [写简历](https://github.com/CyC2018/Backend-Interview-Guide/blob/master/doc/%E5%86%99%E5%A5%BD%E6%8A%80%E6%9C%AF%E7%AE%80%E5%8E%86.md)
- [扫码登录](https://github.com/CyC2018/Backend-Interview-Guide/blob/master/doc/%E6%89%AB%E4%BA%8C%E7%BB%B4%E7%A0%81%E7%99%BB%E5%BD%95%E8%BF%87%E7%A8%8B.md)


# 加油~~
- 消息中间件(1)
- 源码(1)
- mysql(2)
- redis(2)
- jvm(1)
- 设计模式(1)
- dubbo(2)
- spring(1)
- 分布式(2)
- 算法(1)
- xxx(1)

> 每天保证进度，9点撤，回去刷一题