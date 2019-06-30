---
title: ThreadLocal源码分析
date: 2019-04-18 14:35:33
tags:
- 源码
categories: Java
---
### 前言
以前接触过ThreadlLocal，不过只是一点点并且没有及时记录下来，最近看了一些博客并结合源码，了解了一番，很有必要记录一下。  
欢迎指正~
<!--more-->
### 一、简单介绍
ThreadLocal可以存储当前线程下的数据，并保持各线程的数据互不干扰。方便使用，并减少传输。  
使用起来类似HashMap的key，value形式，set和get，不过同一个ThreadLocal对象只能存储一个值
### 二、一般使用
```java
public class Test {

    // 一般以private static方式定义
    private static ThreadLocal<String> threadLocal;
    private static ThreadLocal<String> threadLocal2;

    public static void main(String[] args) {
        threadLocal = new ThreadLocal<>();
        threadLocal2 = new ThreadLocal<>();


        threadLocal.set("abc");
        threadLocal2.set("bcd");

        System.out.println(threadLocal.get());
        System.out.println(threadLocal2.get());
    }
}
```
> 整体结构: Thread下存ThreadLocalMap->Entry数组->数组的下标通过key(当前ThreadLocal对象)的hash函数确定，Entry对象存在key和value属性

### 三、源码分析
> 来自: JDK1.8

```java
public void set(T value) {
    Thread t = Thread.currentThread();
    ThreadLocalMap map = getMap(t);
    if (map != null)
        map.set(this, value);
    else
        createMap(t, value);
}

ThreadLocalMap getMap(Thread t) {
    return t.threadLocals;
}

// ThreadLocalMap.set()
private void set(ThreadLocal<?> key, Object value) {

      Entry[] tab = table;
      int len = tab.length;
      int i = key.threadLocalHashCode & (len-1);

      for (Entry e = tab[i];
              e != null;
              e = tab[i = nextIndex(i, len)]) {
          ThreadLocal<?> k = e.get();

          if (k == key) {
              e.value = value;
              return;
          }

          if (k == null) {
              replaceStaleEntry(key, value, i);
              return;
          }
      }

      tab[i] = new Entry(key, value);
      int sz = ++size;
      if (!cleanSomeSlots(i, sz) && sz >= threshold)
          rehash();
  }
```
通过上面代码发现，set的过程是:
1. 首先获取当前线程Thread对象的ThreadLocalMap引用  `getMap(t)` 的 `t.threadLocals`
2. 然后对ThreadLocalMap引用进行 `map.set()`
3. ThreadLocal对象作为key将值存到ThreadLocalMap下的Entry数组中(类似HashMap，它是通过hash与数组长度减1与，离散的存到数组的不同索引位置，不同的是，由于不是链表的结构，需要判断即将插入的位置和当前元素的关系，来调整位置)

注:ThreadLocalMap是ThreadLocal的内部类，如下
```java
static class ThreadLocalMap {
        static class Entry extends WeakReference<ThreadLocal<?>> {
      /** The value associated with this ThreadLocal. */
      Object value;

      Entry(ThreadLocal<?> k, Object v) {
          super(k);
          value = v;
      }
  }
        ...
}
```

- 从上面又可以看出，**ThreadLocalMap是在ThreadLocal中使用内部类来编写的，但对象的引用是在Thread中！**
- 于是可以总结出：**Thread为每个线程维护了ThreadLocalMap这么一个Map，而ThreadLocalMap的key是LocalThread对象本身，value则是要存储的对象**

下面再来看get()就不难了
```java
public T get() {
    Thread t = Thread.currentThread();
    ThreadLocalMap map = getMap(t);
    if (map != null) {
        ThreadLocalMap.Entry e = map.getEntry(this);
        if (e != null) {
            @SuppressWarnings("unchecked")
            T result = (T)e.value;
            return result;
        }
    }
    return setInitialValue();
}
```

### 四、内存泄露

```java
static class Entry extends WeakReference<ThreadLocal<?>> {
    /** The value associated with this ThreadLocal. */
    Object value;

    Entry(ThreadLocal<?> k, Object v) {
        super(k);
        value = v;
    }
}
```

1. Entry的key保存在外部的 `WeakReference`，弱引用，ThreadLocal在没有外部强引用时，可以被GC回收
2. 但如果线程一直在运行，这个Entry的value一直不能被回收，强引用，会发生内存泄露;Entry是key的弱引用，不是实例对象value的弱引用，无法避免内存泄露
3. 为了尽量避免泄露，在不需要的时候，执行 `remove()` 方法

> 引用: 
> [Java WeakReference的理解与使用](https://www.jianshu.com/p/282a00c9c583)
> [理解Java的强引用、软引用、弱引用和虚引用](https://juejin.im/post/5b82c02df265da436152f5ad)

**remove做了什么呢?**
```java
 /**
  * Remove the entry for key.
  */
  private void remove(ThreadLocal<?> key) {
      Entry[] tab = table;
      int len = tab.length;
      int i = key.threadLocalHashCode & (len-1);
      for (Entry e = tab[i];
          e != null;
          e = tab[i = nextIndex(i, len)]) {
          if (e.get() == key) {
              e.clear();
              expungeStaleEntry(i);
              return;
          }
      }
  }

// Reference.clear()
public void clear() {
    this.referent = null;
}
```
`clear()` 将key置null, 然后 `expungeStaleEntry(i)` 将Entry数组重新组合，遍历数组，key或value为空的对象置空
### 五、总结

1. 存储当前线程可访问的数据，减少数据传输 `private static ThreadLocal<T> local`
2. 因为存储的值在ThreadLocalMap中，与每个线程绑定，所以各个线程间的访问互不影响
3. 数据通过它的内部类ThreadLocalMap，存储在`ThreadLocalMap.Entry`中，最终是存储在每个线程Thread中的ThreadLocalMap引用
4. 调用ThreadLocal的set()方法时，实际上就是往ThreadLocalMap设置值，key是ThreadLocal对象，值是传递进来的对象
5. 调用ThreadLocal的get()方法时，实际上就是往ThreadLocalMap获取值，key是ThreadLocal对象
6. ThreadLocal本身并不存储值，它只是作为一个key来让线程从ThreadLocalMap获取value。

**ThreadLocal设计的目的就是为了能够在当前线程中有属于自己的变量，并不是为了解决并发或者共享变量的问题**