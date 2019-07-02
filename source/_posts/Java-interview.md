---
title: Java面试
date: 2019-04-16 22:18:41
tags: 面试
categories: 面试
---
面试中高级的一些准备提纲, `持续更新`
<!--more-->
> [同学整理](http://nivelle.me/javaInterview/)
> {% post_link interview-experience-2019   %}

**整体参考**:
- [高并发、分布式等，适合面试](https://github.com/doocs/advanced-java)
- [基础知识+算法题解、源码分析](https://github.com/Snailclimb/JavaGuide)

## 消息中间件 
{% post_link message-middleware %}
[kafka为什么那么快](https://www.cnblogs.com/duanxz/p/4705164.html)
- 生产者: 顺序写入、内存映射文件(同步或异步更新到磁盘)
- 消费者: 全部以文件方式发送到消费者
……

## 源码
- 集合
  - [HashMap底层](https://github.com/Snailclimb/JavaGuide/blob/master/docs/java/collection/HashMap.md)
  - {% post_link Java-Collection %}
- 并发
  - {% post_link concurrent-note %}

## 数据库
#### MySql
- {% post_link mysql-note %}
- [索引详解](http://shanks.leanote.com/post/Mysql%E7%B4%A2%E5%BC%95)
- 主从复制原理、作用、实现。
- 水平切分与垂直切分。
- 分库分表，分库分表中间件？

#### redis

> [github参考](https://github.com/doocs/advanced-java)
- {% post_link redis-note %}

## Jvm
{% post_link JVM %} //todo 完善

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
- [spring扩展点](https://blog.csdn.net/qq_38182963/article/details/78795058)

## 分布式
- 分布式事务
- 分布式锁
  - redis
  `SET resource_name my_random_value NX PX 30000`
  NX：表示只有 key 不存在的时候才会设置成功。（如果此时 redis 中存在这个 key，那么设置失败，返回 nil）;
  PX 30000：意思是 30s 后锁自动释放。别人创建的时候如果发现已经有了就不能加锁了。
  优化: RedLock 算法; 对于redis cluster，尝试在大多数节点上建立锁就算成功，删除的时候依次删除
  - zookeeper
  尝试创建临时znode节点，创建成功就获取到了这个锁；那么其他机器再去尝试创建znode节点会失败，只能注册个监听器监听这个锁。释放锁就是删除这个znode，一旦释放就会通知客户端，然后一个等待的客户端可以再次重新加锁
  - redis需要不断尝试去获取锁，开销较大；zk获取不到锁时，注册个监听器即可
- zookeeper
  - 分布式协调
    Ａ系统发出请求以后可以在zookeeper上**对某个节点的值注册个监听器**,一旦Ｂ系统处理完成就修改zookeeper那个节点的值，Ａ系统立马可以收到通知
  - 分布式锁
  创建znode节点，那么其他机器再去尝试创建znode节点会失败
  - 元数据/配置信息管理
  - HＡ高可用
  一个重要的进程一般会做准备两个，主进程挂了立马通过zookeeper感知到切换刀备用进程
- 一致性hash

## 算法
- 几大排序：描述和代码实现 [参考](https://github.com/ZhongFuCheng3y/3y/blob/master/src/sort.md)
- [快排实现](https://juejin.im/post/5b55660ee51d4519202e2003): 分治
- [排序稳定性分析](https://www.jianshu.com/p/9e855ba2b079)
- [二分查找](https://juejin.im/post/5b15ff73e51d4506b113d956)
- 斐波那契
- [B树、B+树与红黑树](https://www.jianshu.com/p/86a1fd2d7406)
  
## 刷题
- 剑指offer {[参考](https://github.com/CyC2018/CS-Notes)}

## 其他
- [看看知识和岗位](https://github.com/CyC2018/)
- [简历模板](https://github.com/CyC2018/Markdown-Resume)
- [写简历](https://github.com/CyC2018/Backend-Interview-Guide/blob/master/doc/%E5%86%99%E5%A5%BD%E6%8A%80%E6%9C%AF%E7%AE%80%E5%8E%86.md)
- [扫码登录](https://github.com/CyC2018/Backend-Interview-Guide/blob/master/doc/%E6%89%AB%E4%BA%8C%E7%BB%B4%E7%A0%81%E7%99%BB%E5%BD%95%E8%BF%87%E7%A8%8B.md)