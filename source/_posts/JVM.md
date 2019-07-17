---
title: JVM
date: 2019-04-16 21:42:13
tags:
categories: Java
---
Java内存模型，垃圾收集器、类加载
<!--more-->

jdk包含jre，jre包含jvm

初试,分析解决
--
查看堆存储快照  
VM arguments
```
-XX:+HeapDumpOnOutOfMemoryError
```
设置堆内存大小
```
-Xms20m -Xmx20m
```

分析工具：eclipse memory analyzer

JVM监控工具
---
jdk/bin/jconsole  直接运行  
堆内存，分为新生代和老年代。垃圾回收会对新生代进行回收。新生代分为，Eden和两个Survivor。
垃圾回收会回收一部分Eden，存活的进入到Survivor生存区。存活久的进入到老年代。

Java虚拟机
---
### Sun Classic VM
- 第一款商用Java虚拟机
- 只能使用解释器方式执行java

### Exact VM
- 编译器和解释器混合执行，两级即时编译器
- 只在Solaris平台发布，就被取代了

### HotSpot VM
- 称霸武林
- 非sun开发，后收购的

### KVM
- 手机平台运行

### JRockit
- BEA公司
- 专注服务器端

### J9
-IBM

### Microsoft JVM


Java虚拟机内存结构
---
分为：  


线程独占区   
生命周期随着线程的创建而创建，随着线程的结束而死亡；所以不用关心这部分的垃圾回收。
- 程序计数器：当前线程执行字节码行号指示器。
- 虚拟机栈：一个个栈帧组成的，局部变量表、操作数栈、动态链接、方法出口信息；每个方法执行，创建一个栈帧，伴随方法的创建到结束，存储局部变量表等，方法执行完毕，出栈。
- 本地方法栈：与虚拟机栈类似，不过提供的是native方法运行。

线程共享区
- 堆：存储几乎所有的对象实例，虚拟机创建而创建。垃圾回收的主要区域。  
- 方法区：存储类信息，常量，静态变量。编译后的代码。


### 程序计数器
- 一块较小的内存空间，它可以看作是当前线程所执行的字节码的行号指示器。  
- 处于线程独占区，线程私有  
- 此区域是Java虚拟机规范中，唯一没有规定任何OutOfMemoryError情况的区域。
- 生命周期随着线程的创建而创建，随着线程的结束而死亡。 

### 虚拟机栈
- Java方法执行的动态内存模型
- 栈放的是对象的引用
- 栈帧：每个方法的执行，都创建一个栈帧，伴随方法的创建到执行完成，用于存储局部变量表，操作数栈，动态链接，方法出口。
- 当方法在运行过程中需要创建局部变量时，就将局部变量的值存入栈帧的局部变量表中。 
当这个方法执行完毕后，这个方法所对应的栈帧将会出栈，并释放内存空间。
- 栈内存溢出则，报错StackOverFlow（如递归）

注意：人们常说，Java的内存空间分为“栈”和“堆”，栈中存放局部变量，堆中存放对象。 
这句话不完全正确！这里的“堆”可以这么理解，但这里的“栈”只代表了Java虚拟机栈中的局部变量表部分。真正的Java虚拟机栈是由一个个栈帧组成，而每个栈帧中都拥有：局部变量表、操作数栈、动态链接、方法出口信息。

### 本地方法栈
- 虚拟机栈为虚拟机执行Java方法服务，本地方法栈为虚拟机执行native方法服务。
- 本地方法栈和Java虚拟机栈实现的功能类似，只不过本地方法区是本地方法运行的内存模型。

### 堆
- 存储对象实例，(几乎所有的对象都存储在堆中)
- 虚拟机启动时创建
- 垃圾收集器管理的主要区域
- 老年代，新生代；新生代又被分为Eden、From Survivor、To Survivor。不同区域生命周期不同，根据区域垃圾回收。
-  -Xms  -Xmx

### 方法区
- 存储虚拟机加载的类信息，常量，静态变量，即时编译器编译后的代码等数据。
- 方法区和永久代
- 垃圾回收在方法区的行为
- 异常的定义
- 运行时常量池，字符串会放到常量池里，类似hashset的table，如 String a = "aa". String 的intern是把对象搬到运行时常量。


### 直接内存(不属于虚拟机内存)
- 除Java虚拟机以外的内存，也有可能被Java使用。
- NIO， 在NIO中引入了一种基于通道和缓冲的IO方式。它可以通过调用本地方法直接分配Java虚拟机之外的内存，然后通过一个存储在Java堆中的DirectByteBuffer对象直接操作该内存，而无需先将外面内存中的数据复制到堆中再操作，从而提升了数据操作的效率。
- 直接内存大小不受Java虚拟机控制，不过，内存不足时就会抛出OOM异常。

### 综上
- Java虚拟机的内存模型中一共有两个“栈”，分别是：Java虚拟机栈和本地方法栈。 
两个“栈”的功能类似，都是方法运行过程的内存模型。并且两个“栈”内部构造相同，都是线程私有。 
只不过Java虚拟机栈描述的是Java方法运行过程的内存模型，而本地方法栈是描述Java本地方法运行过程的内存模型。
- Java虚拟机的内存模型中一共有两个“堆”，一个是原本的堆，一个是方法区。方法区本质上是属于堆的一个逻辑部分。堆中存放对象，方法区中存放类信息、常量、静态变量、即时编译器编译的代码。


对象的创建
---
1、new 类名  
2、根据new的参数在常量池中定义一个类的引用符号。  
3、如果没有找到这个符号引用，说明类还没有被加载，进行类的加载，解析和初始化。  
4、虚拟机为对象分配内存，（堆）  
5、将分配的内存初始化为零值（不包括对象头）   
6、调用对象的init方法。（代码块，构造方法）

### 给对象分配内存
- 指针碰撞(指针移动)，连续的空间。
- 空闲列表：一张表，记录空闲内存。在空闲的部分分配内存。
- 线程安全
    - 加锁
    - 每个线程单独分配自己的区域，叫做本地线程分配缓冲

### 对象的结构
- Header
    - 自身运行时数据(Mark Word)
        - 哈希值
        - GC分代年龄
        - 锁状态标志
        - 线程持有的锁
        - 偏向线程ID
        - 偏向时间戳
    - 类型指针
- InstanceData
- Padding

### 对象的访问定位
栈的对象引用指向堆的对象实例。
- 使用句柄：指向堆中的句柄池，句柄池指向对象的地址
- 直接指针：直接指向对象的地址(hotspot)

具体哪一种方式，跟虚拟机有关。

垃圾回收
---
### 如何判定对象为垃圾
- 引用计数法：在对象中添加一个引用计数器，当有地方引用这个对象的时候，引用计数+1，当引用失效引用计数-1.引用计数为0时回收。
- 可达性分析:根节点(GC Roots)不能到达的回收。GC Roots包括：
    - 虚拟机栈
    - 方法区的类属性所引用的对象
    - 方法区中常量所引用的对象
    - 本地方法栈中所引用的对象
### 如何回收
#### 回收策略
新生代通常复制算法，老年代标记-整理，标记清除。老年代对象一般较大不适合复制算法，浪费空间。标记-整理在可用对象多的情况下很适合。
- 标记-清除算法
    - 效率问题
    - 空间问题
- 复制算法:(针对新生代)解决标记清除的效率问题；两块相同的内存，每次只用一块区域，回收时把不需要回收的部分复制到另一块内存，并连续排列，原来的内存区全部清空，下一次在这块区域继续划分，循环这种操作。
> 内存浪费的解决方案。新生代分为Eden，From Survivor，To Survivor，比例是8：1：1。我们认为垃圾回收后剩余大约10%的有用对象。在Eden的对象垃圾收集以后剩余的放在Survivor，From与To之间是复制算法的关系。当Survivor内存不够时就需要一个内存担保，Tenured Gen。放到老年代。

- 标记-整理算法:针对老年代，整理以后清除。
- 分代收集算法：根据垃圾需要收集的多少决定用复制还是标记-整理算法

#### 常见回收器(Java虚拟机没有规范，下面的不同收集器适用不同的场景)
- Serial(-XX:+UseSerialGC,新生代, 复制算法)
    - 最基本，发展最悠久的
    - 单线程(多线程任务执行，垃圾收集时全部线程暂停等待单线程的收集，收集完继续多个线程的执行)
    - 桌面应用
- ParNew(-XX:+UseParNewGC, 新生代, 复制算法)
    - 多线程
- Parallel Scavenge(-XX:+UserParellelGC)
    - 复制算法(新生代收集器)
    - 多线程收集器
    - 吞吐量:(执行用户代码的时间) / 总时间
    - 达到可控制的吞吐量
    -  -XX：MaxGCPauseMillis 垃圾收集器最大停顿时间
    -  -XX:GCTimeRatio 吞吐量大小， 取值(0, 100)
- Serial Old(-XX:+UseSerialOldGC, 标记-整理)
  - 老年代收集器, Serial的老年代版本
- Parallel Old
  - 老年代
- CMS(Concurrent Mark Sweep)
    - 针对老年代
    - 停顿时间很短
- G1(-XX:+UseG1GC, 复制+标记+整理)
    - 面向服务端
    - 综合前面的优势
    - 并行和并发
    - 分代收集
    - 空间整合
    - 可预测的停顿
    - 标记整理

### 何时回收

内存分配策略
---
### 1. 优先分配到eden
打印垃圾收集信息： -verbose:gc -XX:+PrintGCDetails

### 2. 大对象直接分配到老年代
指定大对象的大小： -XX:PretenureSizeThreshold=8M  
新生代通常是复制算法，新生代垃圾回收的频率很高，所以放在老年代更好。

### 3. 长期存活的对象分配到老年代(jdk7以后不严格)
指定年龄多少算长期 -XX:MaxTenuringThreshold 15  
年龄计数器，每次垃圾回收年龄+1

### 4. 空间分配担保
内存不够时借用老年代内存  
需要检查老年代是否有足够的空间容纳全部的新生代  
开启空间分配担保  -XX:+HandlePromotionFailure  
禁用空间分配担保  -XX:-HandlePromotionFailure

### 5. 动态对象的年龄判断

### 6. 逃逸分析和栈上分配
逃逸分析：分析对象的作用域。  
栈上分配是java虚拟机提供的一种优化技术

只在方法体内部有效，不发生逃逸。反之。  
为成员属性赋值，发生逃逸；  
对象的作用域仅在当前方法有效，没有发生逃逸。  
引用成员变量的值，发生逃逸。

没有发生逃逸的对象放在栈内存中。  
```java
public class PartionOnStack {
   static class User{
    private int id;
    private String name;
    public User(){}
       }
    private static  User user;
    public static void foo() {
    user=new User();
    user.id=1;
    user.name="sixtrees";
    }
    public static void main(String[] args) {
    foo();
    }
}
```
因为上面的代码中的User的作用域是整个Main Class，所以user对象是可以逃逸出函数体的。下面的代码展示的则是一个不能逃逸的代码段。
```java
public class PartionOnStack {
    class User{
    private int id;
    private String name;
    public User(){}
       }
    public  void foo() {
    User user=new User();
    user.id=1;
    user.name="sixtrees";
    }
    public static void main(String[] args) {
    PartionOnStack pos=new PartionOnStack();
    pos.foo();
    }

}
```
虚拟机工具
---
- Jps
    - Java process status
    - 控制台 打 "jps". 显示Java进程id
    - jps -l 运行时主类全名或jar包名
    - jps -m 运行时主类接收的参数(在args中)
    - jps -v 接收的VM的参数
    - 本地虚拟机唯一Id  lvmid local virtual machine id

- Jstat
    - 依赖jps， 需要知道进程的id才能使用
    - 官网可查看文档
    - jstat -gcutil {id}  垃圾回收概要信息

- Jinfo  
    - 实时查看和调整虚拟机的各项参数
    - jinfo -flag UseSerialGC {id}  查看该进程是否使用SerialGC。是(+),否(-)

- Jmap  
    - jmap -dump:format=b,file=/Users/yuan/a.bin {进程id}  转储快照
    - jmap -histo {id} 类和实例信息

- Jhat
    - 分析Jmap快照
    - jhat {文件路径}
    - 启动http server

- Jstack
    - 线程快照
    - Jstack {id}

- JConsole 
    - 内存监控
    - 线程监控
    - 死锁检测

- VisualVM 需下载


性能调优
---
### 案例1：绩效考核系统  
#### 环境：
64G， 2个intel E5 CPU  
tomcat7， jdk7  
堆内存设置了50G  
#### 问题：经常卡顿  
#### 处理思路：
- ~~优化sql(不是每个功能慢了，是某个时间段慢了)~~
- ~~监控CPU~~
- 监控内存
    - Full GC 20-30s

#### 解决：  
部署多个web容器，每个web容器堆内存设置为4G.用nginx负载。 这样解决了堆内存多大垃圾收集时间长。又不浪费内存。

#### 总结：  
没有大对象，不经常Full GC，部署多个容器更好。否则单个容器分配大的内存更好。毕竟容器启动会有额外内存，硬盘开销。

### 案例2：数据抓取系统
#### 环境
jdk5， 2G内存， intel core i3， win server

#### 问题
不定期内存溢出，堆内存加大也无济于事，导出堆快照，没人信息。内存监控，正常

#### 处理思路
捕获到了bytebuffer。系统用了很多NIO。direct memory改大一些。

### 案例3：物联网应用
JVM崩溃。 Connect Reset  

任务挤压，大量未处理任务导致  
解决：加消息队列





---------------

Class文件
---

编译器编译产生的。字节码文件  
.class文件需要二进制编辑器打开。比如binary viewer

### 文件结构
Class文件是一组以8位字节为基础单位的二进制流，各个数据项目严格按照顺序紧凑的排列。中间没有任何分隔符。  
Class文件中有两种数据类型：无符号数和表。 

第一行前8位是魔数，后8位是版本号。  
魔数: class文件的标识，CA FE BA BE， 魔数后面8位为版本号：JDK1.8 = 52， JDK1.7 = 51  

常量池  
访问标志  
类索引，弗雷索引，接口索引集合  
字段表集合  
方法表集合  
属性表集合   

### 类加载

#### 自定义类加载
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
