---
title: Java面试
date: 2019-04-16 22:18:41
tags: 面试
categories: Java
---
面试中高级的一些复习提纲, `持续更新`
<!--more-->
> [其他参考](http://nivelle.me/javaInterview/)
## 消息中间件
- kafka
- activemq
- rabbitmq
- rocketmq
## 源码
- 集合
- 并发(synchronized ThreadLocal...)、线程池
	- [synchronized](https://juejin.im/post/5b4eec7df265da0fa00a118f)
		- 底层是通过monitor(监视器锁)对象完成的，通过软件在jvm实现
		- 同步代码块是 `monitorenter` 和 `monitorexit`完成，同步方法是通过检查`ACC_SYNCHRONIZED`标识符是否被设置
		- 实现方式是jvm在底层调用操作系统的互斥原语mutex实现，被阻塞的线程会被挂起、等待重新调度，会导致“用户态和内核态”两个态之间来回切换，对性能有较大影响。
		- 其实wait/notify等方法也依赖于monitor对象
- 并发的几种实现方式

## 数据库
#### MySql
- 索引 
	- mysql中a、b的联合索引。 对于单个a或b字段有效吗？
- 引擎
- B+ Tree 原理，与其它查找树的比较
- 主从复制原理、作用、实现。
- 水平切分与垂直切分。
#### redis
- 五种数据类型 [简书](https://www.jianshu.com/p/d645cebff386) 总结[我的](https://blog.csdn.net/ysw1132/article/details/88807203)
- 分布式锁
- 与 Memchached 的比较。

#### Jvm
[我的笔记](https://blog.csdn.net/ysw1132/article/details/89342995) //todo 完善

#### 设计模式
- 手写单例
- 手写工厂模式
- 手写生产者消费者
- 动态代理

#### 框架
##### dubbo
- 与springcloud的区别
- 灰度、限流、降级
##### spring
- MVC
- AOP

#### 分布式
- 分布式事务
- 分布式锁
- zookeeper
#### 算法
- 几大排序：可以参考这个， 描述和代码实现
- 斐波那契

#### 其他
- [看看知识和岗位](https://github.com/CyC2018/)
- [简历模板](https://github.com/CyC2018/Markdown-Resume)
- [写简历](https://github.com/CyC2018/Backend-Interview-Guide/blob/master/doc/%E5%86%99%E5%A5%BD%E6%8A%80%E6%9C%AF%E7%AE%80%E5%8E%86.md)
- [扫码登录](https://github.com/CyC2018/Backend-Interview-Guide/blob/master/doc/%E6%89%AB%E4%BA%8C%E7%BB%B4%E7%A0%81%E7%99%BB%E5%BD%95%E8%BF%87%E7%A8%8B.md)