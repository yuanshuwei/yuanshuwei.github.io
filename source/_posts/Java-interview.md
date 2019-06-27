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

## 源码
- 集合
  - [HashMap底层](https://github.com/Snailclimb/JavaGuide/blob/master/docs/java/collection/HashMap.md)
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
  	- ExecutorService的execute和submit:submit有返回值，而execute没有; submit用的Future,call的方式得到返回值 [链接](http://www.jcodecraeer.com/a/chengxusheji/java/2014/0903/1672.html)
- 并发的几种实现方式 [111](https://www.jianshu.com/p/bdac4e8a6839)
  - 任务类实现Runnable接口，在方法Run()里定义任务。
  - 任务类继承Thread，重写run()方法。
  - 实现接口Callable并在call()方法里得到线程执行结果。
- 线程池参数
  比如coresize 1 maxsize 5　有界队列: 
  先判断当前空闲线程数和coresize的关系，如果超过coresize扔队列，队列满，判断maxsize满没满，没满创建thread,如果满了执行拒绝策略逻辑(jdk自带４种拒绝策略，也可以重写，只要实现RejectExecutionHandler类)
  - 阻塞队列
    1. ArrayBlockingQueue：是一个基于数组结构的有界阻塞队列，此队列按 FIFO（先进先出）原则对元素进行排序。
    2. LinkedBlockingQueue：一个基于链表结构的阻塞队列，此队列按FIFO （先进先出） 排序元素，吞吐量通常要高于ArrayBlockingQueue。静态工厂方法Executors.newFixedThreadPool()使用了这个队列。
    3. SynchronousQueue：一个不存储元素的阻塞队列。每个插入操作必须等到另一个线程调用移除操作，否则插入操作一直处于阻塞状态，吞吐量通常要高于LinkedBlockingQueue，静态工厂方法Executors.newCachedThreadPool使用了这个队列。
    4. PriorityBlockingQueue：一个具有优先级得无限阻塞队列。
  - 线程池饱和时，线程数大于maxsize,的拒绝策略
    - hreadPoolExecutor.AbortPolicy:丢弃任务并抛出RejectedExecutionException异常。
    - ThreadPoolExecutor.DiscardPolicy：也是丢弃任务，但是不抛出异常。
    - ThreadPoolExecutor.DiscardOldestPolicy：丢弃队列最前面的任务，然后重新尝试执行任务（重复此过程）
    - ThreadPoolExecutor.CallerRunsPolicy：由调用线程处理该任务

## 数据库
#### MySql
- {% post_link mysql-note %}
- 主从复制原理、作用、实现。
- 水平切分与垂直切分。
- 分库分表，分库分表中间件？

#### redis

> [github参考](https://github.com/doocs/advanced-java)

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
  - **主从切换步骤**: 每个哨兵ping master,超过指定时间就认为宕机，此时是主观宕机；当哨兵集群你中超过一半都认为master宕机了，此时到达了客观宕机，可以进程主从切换　
  - **选举算法**: ①跟master断开时长②slave优先级③复制offset(复制数据更多的那个)④run id(大小排序)
- 集群: 
  - redis cluster基于槽的概念，一共分成16384个槽，将请求分发生到任意节点，接受到的节点会把请求发送刀正确的节点，类似重定向。如果某个节点故障，整个集群不能工作，因此推荐一个主跟多个从+哨兵，保证集群的高可用

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
- 斐波那契
  
## 刷题
- 剑指offer {[参考](https://github.com/CyC2018/CS-Notes)}

## 其他
- [看看知识和岗位](https://github.com/CyC2018/)
- [简历模板](https://github.com/CyC2018/Markdown-Resume)
- [写简历](https://github.com/CyC2018/Backend-Interview-Guide/blob/master/doc/%E5%86%99%E5%A5%BD%E6%8A%80%E6%9C%AF%E7%AE%80%E5%8E%86.md)
- [扫码登录](https://github.com/CyC2018/Backend-Interview-Guide/blob/master/doc/%E6%89%AB%E4%BA%8C%E7%BB%B4%E7%A0%81%E7%99%BB%E5%BD%95%E8%BF%87%E7%A8%8B.md)