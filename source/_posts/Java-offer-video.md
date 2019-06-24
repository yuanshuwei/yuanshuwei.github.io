---
title: 剑指Java面试知识点笔记
date: 2019-06-20 20:16:50
tags:
categories: 面试
---
剑指面试视频，笔记
<!--more-->
一、Redis
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

`scan cursor [pattern] [count]`
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


二、Shell
---

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

三、类加载
---

### 自定义类加载
```
public class MyClassLoader extends ClassLoader {

    private String path;
    private String name;

    public MyClassLoader(String path, String name) {
        this.path = path;
        this.name = name;
    }

    @Override
    public Class findClass(String name) {

        byte[] b = loadClassData(name);
        return defineClass(name, b, 0, b.length);
    }

    // 加载类文件二进制字节码
    private byte[] loadClassData(String name) {
        name = path + name + ".class";
        InputStream in = null;
        ByteArrayOutputStream out = null;
        try {
            in = new FileInputStream(new File(name));
            out = new ByteArrayOutputStream();
            int i = 0;
            while ((i = in.read()) != -1) {
                out.write(i);
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            try {
                out.close();
                in.close();
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
        return out.toByteArray();
    }
}

public class ClassLoaderCheck {

    public static void main(String[] args) throws IllegalAccessException, InstantiationException {
        MyClassLoader myClassLoader = new MyClassLoader("/Users/yuan/Desktop/", "myClassLoader");
        Class clazz = myClassLoader.findClass("Bot");
        clazz.newInstance();
    }
}
```

#### 类加载器分类:
- BootStrapClassLoader: C++编写，加载核心库java
- ExtClassLoader: Java编写，加载扩展库javax.*
- AppClassLoader: Java编写，加载程序所在目录
- 自定义ClassLoader: Java编写，定制化加载


#### 双亲委派机制
自底向上查找，自上向下加载;代码的loadclass中，加载类时，循环判断父加载器不为空就先尝试加载父加载器，直到加载C++编写的BootStrapClassLoader。 `CustomClassLoader->AppClassLoader->ExtClassLoader->BooStrapClassLoader`
类本身+类加载器唯一确定在jvm  的唯一性。所以自定义加载同类名的类，无法加载
[参考](https://www.jianshu.com/p/353c26c744df)

#### 为什么用双亲委派模型?
每个类加载器是一个类加载空间，隔开的各个类的加载优先级；
- 避免多份同样的字节码的加载，逐层去查找

#### loadClass, forName的区别
- 加载class文件字节码，生成Class对象
- 校验(正确和安全)、准备(为类变量分配空间并设置变量初始值)、解析(JVM常量池的符号引用转为直接引用)
- 初始化(执行类变量赋值和静态代码块)

forName是加载类并已完成了初始化(比如执行静态代码块); loadClass没有链接的

#### 三大性能调优参数 -Xms -Xmx -Xss的含义
- -Xss: 规定每个线程虚拟机栈的大小，影响并发线程数的大小
- -Xms: 堆的初始值
- -Xmx: 堆能达到的最大值
> 一般-Xms与-Xmx设置成一样大小，防止扩容时的抖动

#### Java内存模型中堆和栈的区别-内存分配策略
- 静态存储: 编译时确定每个数据目标在运行时的存储空间需求
- 栈式存储: 数据区需求在编译时未知，运行时模块入口前确定
- 堆式存储: 编译时或运行时模块入口都无法确定，动态分配
- 管理方式: 栈自动释放，堆需要GC
- 空间大小: 栈比堆小
- 碎片相关: 栈产生的碎片远小于堆
- 分配方式: 栈支持静态和动态分配，堆只支持动态分配
- 效率: 栈效率比堆高

#### intern方法
如果常量池中不存在，则把字符串放到常量池中，存在则直接返回其引用；
JDK6以上:除了检查常量池是否存在，还检查堆中是否存在，如果存在则将对象的引用放在常量池并返回

#### Java内存结构
线程私有:
  - 程序计数器
  - 虚拟机栈
  - 本地方法栈

线程共享:
  - 堆(JDK6以上: 常量池放在堆中，之前在永久代，频繁创建放到常量池容易OOM: PermGen spcae)
  - MetaSpace: 类加载信息

#### GC Root
- 虚拟机栈中引用的对象(栈帧中的本地变量表)
- 方法区中的常量引用的对象
- 方法区中的类静态属性引用的对象
- 本地方法栈中JNI(Native方法)的引用对象
- 活跃线程的引用对象

#### 回收算法
- 标记-清除
- 复制: 解决碎片化问题
- 标记-整理
- 分代收集
> JDK8以后取消了永代，可以减少Full GC的频率

#### 常见的垃圾收集器
...
#### 强引用、软引用、弱引用、虚引用
- 强引用(Strong Reference)
  - Object obj = new Object()
  - 抛出OOM也不会回收这个内存
- 软引用(Soft Reference)
  - 对象处于有用但非必须的状态
  - 只有空间不足时，才会回收这个对象内存
  - 可以实现高速缓存
  ```
  String str= new String("a")
  SoftReference<String> s = new SoftReference<String>(str) //软引用
  ```
- 弱引用(Weak Reference)
  - 非必须对象，比软引用更弱
  - GC时被回收
  - 被回收的概率不大，因为回收的优先级比较低
  - 适用于偶尔会适用，不影响GC垃圾收集的对象
- 虚引用(Phntom Reference)
  - 不会决定对象的生命周期
  - 任何时候都能被回收
  - 跟踪垃圾回收期回收活动，起到哨兵作用
  - 必须和引用队列ReferenceQueue联合使用

#### 进程与线程的区别
进程独占程序内存，线程是对进程拆分的多个任务的并发执行；进程是资源分配的最小单位，线程是CPU调度的最小单位。

#### Thread的start和run
start()方法会创建一个新的子线程并启动；run()方法只是一个普通方法的调用

#### 线程返回值
通过Callable接口实现，通过FutureTask或线程池获取

```
public class MyCallable implements Callable<String> {

    public String call() throws Exception {
        String value = "test";
        System.out.println("Ready to work");
        Thread.sleep(5000);
        System.out.println("work done");
        return value;
    }
}

public class CallableDemo {

    public static void main(String[] args) throws ExecutionException, InterruptedException {
        FutureTask task = new FutureTask(new MyCallable());
        new Thread(task).start();
        if (!task.isDone()) {
            System.out.println("task has not finished, pleaser wait!");
        }
        System.out.println("task return :" + task.get());
    }
}

public class ThreadPoolDemo {

    public static void main(String[] args) {
        ExecutorService executorService = Executors.newCachedThreadPool();
        Future<String> future = executorService.submit(new MyCallable());
        if (!future.isDone()) {
            System.out.println("task has not finished, pleaser wait!");
        }
        try {
            System.out.println(future.get());
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        } finally {
            executorService.shutdown();
        }
    }
}
```

#### 线程状态
新建、运行(running和ready)、无限期等待、限期等待、阻塞、结束

#### sleep和wait
sleep只让出cpu，不会导致锁行为的变化；wait不仅让出cpu还释放已占有的同步资源锁

#### synchronized
- 每个Java对象头存储的锁信息，代码块monitorenter,monitorexit,锁方法，ACC_SYNCHRONIZED标志；
- 早期属于重量级锁，原来mutex lock实现，线程切换需要用户态转到核心态，开销很大

#### ReentrantLock
AQS实现，可以对获取锁的等待时间进行设置，避免死锁；可以获取各种锁信息；可以灵活实现多路通知；Sync操作Mark Word，lock调用Unsafe类的park()方法

#### HashMap put方法逻辑
- 如果未初始化过，则初始化
- 对key求hash，然后计算下标
- 如果没有碰撞，直接放入桶中
- 如果碰撞了，已链表方式链接到后面
- 如果链表长度超过阈值，链表转为红黑树
- 如果节点已存在就替换旧值
- 如果桶满了就resize(扩容2倍后重排)

#### ConccurentHashMap
CAS+synchronized使锁更细化； 数组+链表+红黑树

#### JUC梳理
- collections: Queue,ConcurrentMap,
- executor: Furure,Callable,Executor
- tools: CountDownLatch,CyclicBarrier,Semaphore,Executors,Exchanger
- locks:ReentantLock, Condition,ReadWriteLock,LockSypport
- atomic: AtomicInteger...

#### BlockingQueue
- ArrayBlockingQueue: 数组结构组成的有界阻塞队列
- LinkedBlockingQueue: 链表结构组成的有界/无界阻塞队列
- PriorityBlockingQueue: 支持优先级排序的无界阻塞队列
- DelayQueue: 优先级实现的无界阻塞队列
- SynchronousQueue: 不存储元素的阻塞队列
- LinkedTransferQueue: 链表结构组成的无界阻塞队列
- LinkedBlockingDeque: 链表结构组成的双向阻塞队列