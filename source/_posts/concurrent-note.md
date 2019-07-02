---
title: '并发笔记'
date: 2019-06-30 22:02:10
tags:
categories:
---
并发笔记
<!--more-->
- 线程的状态: 新建、可运行(就绪状态，start后马上进入就绪状态、)、运行、阻塞(比如sleep方法后)、死亡
- [synchronized](https://juejin.im/post/5b4eec7df265da0fa00a118f)
		- 底层是通过monitor(监视器锁)对象完成的，通过软件在jvm实现
		- 同步代码块是 `monitorenter` 和 `monitorexit`完成，同步方法是通过检查`ACC_SYNCHRONIZED`标识符是否被设置
		- 实现方式是jvm在底层调用操作系统的互斥原语mutex实现，被阻塞的线程会被挂起、等待重新调度，会导致“用户态和内核态”两个态之间来回切换，对性能有较大影响。
		- 其实wait/notify等方法也依赖于monitor对象
- {% post_link threadlocal %}
- [volatile原理](https://www.cnblogs.com/paddix/p/5428507.html)
    	- 通过volatile修饰的变量可以保证线程之间的可见性，但并不能保证这多个指令的原子执行，在多线程并发执行下，无法做到线程安全，得到正确的结果
    	- 可见性:①写变量时，强制刷新到主内存中②修改变量后，强制让其他线程的工作内存中的值失效
    	- 顺序性:指令重排序，通过一组处理器指令，实现对内存操作的顺序限制(理解单例的懒加载的双重锁检查中的新建对象的三个过程)
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
- thread与线程池
  - [ThreadLocal 遇上线程池的问题及解决办法](https://www.cnblogs.com/qifenghao/p/8977378.html)
  - 线程池中的线程在任务执行完成后会被复用，所以在线程执行完成时，要对 ThreadLocal 进行清理（清除掉与本线程相关联的 value 对象）。不然，被复用的线程去执行新的任务时会使用被上一个线程操作过的 value 对象，从而产生不符合预期的结果。
- [线程池参数](https://www.jianshu.com/p/d8cdfd6bc7a4)
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