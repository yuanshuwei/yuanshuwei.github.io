---
title: Java编程的逻辑-泛型
date: 2018-08-28 11:27:54
categories: Java
---
> 泛型是通过类型擦除实现的，类型参数会在编译时被替换为Object，运行时Java虚拟机不知道泛型这回事；
<!--more-->
类泛型，方法泛型，接口泛型  
#### 泛型类
```java
public class Pair<U,V> {
    U first;
    v second;
    
    public Pair(U first, V second) {
        this.first = first;
        this.second = second;
    }
    public U getFirst() {
        return first;
    }
    public V getSecond() {
        return second;
    }
```
#### 泛型方法
```java
//下面都是泛型方法
public <T> int indexOf(T[] arr, T elm) {
    for(int i=0; i < arr.length; i++) {
        if(arr[i].equals(elm)) {
            return i;
        }
    }
    return -1;
}

public <T,K> K showKeyName(Generic<T> container){
...
}

//这不是一个泛型方法，这是一个普通的方法，只不过使用了泛型通配符?
    //同时这也印证了泛型通配符章节所描述的，?是一种类型实参，可以看做为Number等所有类的父类
    public void showKeyValue2(Generic<?> obj){
        Log.d("泛型测试","key value is " + obj.getKey());
    }
    
class GenerateTest<T>{
        public void show_1(T t){
            System.out.println(t.toString());
        }

        //在泛型类中声明了一个泛型方法，使用泛型E，这种泛型E可以为任意类型。可以类型与T相同，也可以不同。
        //由于泛型方法在声明的时候会声明泛型<E>，因此即使在泛型类中并未声明泛型，编译器也能够正确识别泛型方法中识别的泛型。
        public <E> void show_3(E t){
            System.out.println(t.toString());
        }

        //在泛型类中声明了一个泛型方法，使用泛型T，注意这个T是一种全新的类型，可以与泛型类中声明的T不是同一种类型。
        public <T> void show_2(T t){
            System.out.println(t.toString());
        }
    }

```
> 1. <T>表明该方法将使用泛型类型T，此时才可以在方法中使用泛型类型T; 
> 2. 只有声明了<T>的方法才是泛型方法，泛型类中的使用了泛型的成员方法并不是泛型方法。

##### 泛型方法与可变参数
```java
public <T> void printMsg( T... args){
    for(T t : args){
        Log.d("泛型测试","t is " + t);
    }
}
```
```java
printMsg("111",222,"aaaa","2323.4",55.55);
```
##### 泛型方法静态方法
> 静态方法无法访问类上定义的泛型；如果静态方法操作的引用数据类型不确定的时候，必须要将泛型定义在方法上。

#### 泛型接口
```java
//定义一个泛型接口
public interface Generator<T> {
    public T next();
}
```
```java
/**
 * 未传入泛型实参时，与泛型类的定义相同，在声明类的时候，需将泛型的声明也一起加到类中
 * 即：class FruitGenerator<T> implements Generator<T>{
 * 如果不声明泛型，如：class FruitGenerator implements Generator<T>，编译器会报错："Unknown class"
 */
class FruitGenerator<T> implements Generator<T>{
    @Override
    public T next() {
        return null;
    }
}
```
```java
//实现时传入具体类型
public class FruitGenerator implements Generator<String> {

    private String[] fruits = new String[]{"Apple", "Banana", "Pear"};

    @Override
    public String next() {
        Random rand = new Random();
        return fruits[rand.nextInt(3)];
    }
}
```

#### 泛型通配符
举例， 虽然Integer是Number的子类，但是Generic<Number>和Generic<Ingeter>并不能构成子类关系；可以使用下面的形式
```java
public void showKeyValue1(Generic<?> obj){
    Log.d("泛型测试","key value is " + obj.getKey());
}
```
此处的 '?' 是实参，可以把 '?' 看成所有类型的父类。是一种真实的类型;   当操作类型时，不需要使用类型的具体功能时，只使用Object类中的功能。那么可以用 ? 通配符来表未知类型。

两种写法:
```java
public void addAll(DynamicArray<? extends E> c)

public <T extends E> void addAll(DynamicArray<T> c)
```
通配符，允许读不允许写，比如下面，?是未知的类型，只知道是Number的子类，如果允许写，Java无法保证类型安全性所以干脆禁止写
```
```

#### 上界
```java
public class NumberPair<U extends Number, V extends Number> extends Pair<U, V> {
    public NumberPair(U first, V second) {
        super(first, second);
    }
}

public static <T extends Comparable<T>> T max(T[] arr) {
    //
}
```
#### 局限性
- 因为类型会被替换为Object，所以泛型不能使用基本的数据类型; 下面写法不合法，需使用对应的包装类
```java
Pair<int> minmax = new Pair<ing>(1,100)
```
- instanceof是运行时判断，与泛型无关
- 下面的例子重载不允许，因为类型擦除后它们的声明是一样的
```java
public static void test(DynamicArray<Integer> intArr)
public static void test(DynamicArray<String> intArr)
```
- 不能用类型参数创建对象；如果允许用户以为创建的是对应类的对象，类型擦除后实际创建的是Object的对象，所以干脆禁止。 想要这么做可以用反射；
```java
//都是非法的
T elm = new T();
T[] arr = new T[10]; 
```
- 不能用于静态变量
- 不支持创建泛型数据， Object[] -> T[] 问题； 需要使用反射。Array.newInstance(Class, size)