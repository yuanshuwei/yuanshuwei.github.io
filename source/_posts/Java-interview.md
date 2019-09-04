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
- [《Java编程的逻辑》系列博客](https://www.cnblogs.com/swiftma/)

## 消息中间件 
- {% post_link message-middleware %}
- [kafka架构和基本原理](https://www.cnblogs.com/ITtangtang/p/8026771.html)
- [kafka系统设计-面试](https://github.com/Snailclimb/JavaGuide/blob/master/docs/system-design/data-communication/Kafka%E7%B3%BB%E7%BB%9F%E8%AE%BE%E8%AE%A1%E5%BC%80%E7%AF%87-%E9%9D%A2%E8%AF%95%E7%9C%8B%E8%BF%99%E7%AF%87%E5%B0%B1%E5%A4%9F%E4%BA%86.md)

## 集合
- {% post_link Java-Collection %}
- [HashMap底层](https://github.com/Snailclimb/JavaGuide/blob/master/docs/java/collection/HashMap.md)
- [HashMap源码](https://www.cnblogs.com/cutter-point/p/11396240.html)
- [ConcurrentHashMap源码](https://www.cnblogs.com/luozhiyun/p/11406557.html)
- [LinkedHashMap](https://www.cnblogs.com/xiaoxi/p/6170590.html)
- [TreeMap](https://www.cnblogs.com/swiftma/p/5975943.html)
- [HashSet](https://www.cnblogs.com/whgk/p/6114842.html)
- [TreeSet](https://www.cnblogs.com/pony1223/p/7907173.html)
- [ArrayList](https://www.cnblogs.com/chenssy/p/3498468.html)
- [LinkedList](https://www.cnblogs.com/chenssy/p/3514524.html)


## 并发
- [线程的生命周期](https://www.cnblogs.com/sunddenly/p/4106562.html)
- [synchronized](https://juejin.im/post/5b4eec7df265da0fa00a118f)
  - 底层是通过monitor(监视器锁)对象完成的，通过软件在jvm实现
  - 同步代码块是 `monitorenter` 和 `monitorexit`完成，同步方法是通过检查`ACC_SYNCHRONIZED`标识符是否被设置
  - 实现方式是jvm在底层调用操作系统的互斥原语mutex实现，被阻塞的线程会被挂起、等待重新调度，会导致“用户态和内核态”两个态之间来回切换，对性能有较大影响。
  - 其实wait/notify等方法也依赖于monitor对象
- {% post_link threadlocal %} (重写)
- [volatile原理](https://www.cnblogs.com/paddix/p/5428507.html)
  - 通过volatile修饰的变量可以保证线程之间的可见性，但并不能保证这多个指令的原子执行，在多线程并发执行下，无法做到线程安全，得到正确的结果
  - 可见性:①写变量时，强制刷新到主内存中②修改变量后，强制让其他线程的工作内存中的值失效
  - 顺序性:指令重排序，通过一组处理器指令，实现对内存操作的顺序限制(理解单例的懒加载的双重锁检查中的新建对象的三个过程)
- [CAS](https://juejin.im/post/5a73cbbff265da4e807783f5) / [参考2](https://www.jianshu.com/p/fb6e91b013cc)
  - 比较，如果原始值没有被修改过，则更新成新的目标值；如果这一步的CAS没有成功，那就采用自旋的方式继续进行CAS操作，取出乍一看这也是两个步骤了啊，其实在 `JNI` 里是借助于一个 `CPU指令` 完成的。所以还是原子操作。
  - `synchronized` 是悲观锁，假设一定会发生冲突，直接加锁操作，比较重； `CAS` 是 乐观锁，假设不会发生冲突，当大声冲突时，进行重试操作； ReenterLock内部的AQS，还是各种Atomic开头的原子类，内部都应用到了CAS
  - 缺点：ABA问题， 用`AtomicStampedReference`, 它可以通过控制变量值的版本来保证CAS的正确性。/ `循环时间长开销大`
- [AQS](https://www.cnblogs.com/waterystone/p/4920797.html)  
即队列同步器。它是构建锁或者其他同步组件的基础框架(如ReentrantLock、ReentrantReadWriteLock、Semaphore等)
- [并发的几种实现方式](https://www.jianshu.com/p/bdac4e8a6839)
  - 任务类实现Runnable接口，在方法Run()里定义任务。
  - 任务类继承Thread，重写run()方法。
  - 实现接口Callable并在call()方法里得到线程执行结果。
- [线程池](https://www.cnblogs.com/dolphin0520/p/3932921.html)  
比如coresize 1 maxsize 5　有界队列: 
  先判断当前空闲线程数和coresize的关系，如果超过coresize扔队列，队列满，判断maxsize满没满，没满创建thread,如果满了执行拒绝策略逻辑(jdk自带４种拒绝策略，也可以重写，只要实现RejectExecutionHandler类) 
- [线程池注意事项](https://www.jianshu.com/p/340f23001a65)
- [有返回值的线程](https://www.cnblogs.com/yysbolg/p/9239384.html)
- [多线程和并发基础面试题](https://www.cnblogs.com/dolphin0520/p/3932934.html)
- [java concurrent包常用类小结](https://www.cnblogs.com/juniorMa/p/5848705.html)
- [CountDownLatch原理和示例](https://www.cnblogs.com/skywang12345/p/3533887.html)

## 数据库
#### MySql
- {% post_link mysql-note %}
- [索引详解](http://shanks.leanote.com/post/Mysql%E7%B4%A2%E5%BC%95)

#### redis

> [github参考](https://github.com/doocs/advanced-java)

- {% post_link redis-note %}

## Jvm
- {% post_link JVM %}
- [jvm调优1](https://zhuanlan.zhihu.com/p/57014847)
- [jvm调优2](https://cs.xieyonghui.com/java/java-jvm-performance_16.html)
- [jvm调优3](https://www.jianshu.com/p/2c4b091deaa3)
## 框架
##### dubbo
- [与springcloud的区别](https://www.cnblogs.com/cyl048/p/10416705.html)
- 灰度
  当一个接口出现不兼容升级时，可以用版本号过渡，版本号不同的服务相互不调用，实现灰度发布,步骤如下:
  1. 接口旧的实现定义version="1.0.0"，接口新的实现version="2.0.0"
  2. Consumer端定义version="*"
  
  这样定义Provider和Consumer后，新旧接口实现各承担`50%`的流量；
利用dubbo该特性，还能完成不兼容版本迁移：
  在低压力时间段，先升级一半Provider为新版本；
  1. 再将所有消费者升级为新版本；
  2. 然后将剩下的一半提供者升级为新版本。
- [dubbo是如何控制并发数和限流的？](dubbo是如何控制并发数和限流的？)
- [Dubbo令牌桶限流代码分析-StatItem类](https://blog.csdn.net/cbhyk/article/details/86064725)
- [dubbo服务降级](https://www.cnblogs.com/libin6505/p/11270147.html)
- [dubbo sentinel](http://dubbo.apache.org/zh-cn/blog/sentinel-introduction-for-dubbo.html)
- [dubbo spi](http://dubbo.apache.org/zh-cn/blog/introduction-to-dubbo-spi.html)
- 源码
- [掘金-肥朝](https://juejin.im/search?query=%E8%82%A5%E6%9C%9D&type=all)

##### [微服务架构基础之service mesh](https://www.cnblogs.com/tianyamoon/p/10106587.html)

##### spring
- {% post_link spring-ioc %}
- [spring扩展点](https://blog.csdn.net/qq_38182963/article/details/78795058)

## 分布式
- [分布式事务](https://www.cnblogs.com/cjsblog/p/9837062.html)
- [分布式锁](https://www.cnblogs.com/garfieldcgf/p/6380816.html)
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
- [zookeeper选举](https://www.cnblogs.com/ASPNET2008/p/6421571.html)

## 算法
- 几大排序：描述和代码实现 [参考](https://github.com/ZhongFuCheng3y/3y/blob/master/src/sort.md)
- [面试中的算法总结](https://www.cnblogs.com/wxisme/p/5243631.html)
- [快排实现](https://juejin.im/post/5b55660ee51d4519202e2003): 分治
- [排序稳定性分析](https://www.jianshu.com/p/9e855ba2b079)
- [二分查找](https://juejin.im/post/5b15ff73e51d4506b113d956)
- [斐波那契](https://www.jianshu.com/p/0755888b946b)
- [B树、B+树与红黑树](https://www.jianshu.com/p/86a1fd2d7406)
- [深度和广度优先](https://dyygusi.iteye.com/blog/2162617)
- [最长回文子串](https://www.jianshu.com/p/138ff21d9ede)
- [懒加载单例](https://blog.csdn.net/xjy9266/article/details/75371179)
- [手写工厂模式](https://www.cnblogs.com/fengchuipipiliangandy/articles/7645944.html)
- [手写生产者消费者](https://www.cnblogs.com/chentingk/p/6497107.html)
- [动态代理](https://www.cnblogs.com/gdwkong/p/8035120.html)
- [对称和非对称加密](https://juejin.im/post/5abb6c8651882555784e051d)
  
## 刷题
- [剑指offer](https://doocs.gitee.io/coding-interview/#/docs/coding-interview)
- [leetcode](https://github.com/doocs/leetcode)

## 其他
- [看看知识和岗位](https://github.com/CyC2018/)
- [简历模板](https://github.com/CyC2018/Markdown-Resume)
- [写简历](https://github.com/CyC2018/Backend-Interview-Guide/blob/master/doc/%E5%86%99%E5%A5%BD%E6%8A%80%E6%9C%AF%E7%AE%80%E5%8E%86.md)
- [扫码登录](https://github.com/CyC2018/Backend-Interview-Guide/blob/master/doc/%E6%89%AB%E4%BA%8C%E7%BB%B4%E7%A0%81%E7%99%BB%E5%BD%95%E8%BF%87%E7%A8%8B.md)