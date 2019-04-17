---
title: Java8笔记
date: 2019-04-16 22:20:26
categories: Java
---
主要是Optional和Stream的基本用法
<!--more-->

### Optional
##### 逐层取值
```java
String version = "UNKNOWN";
if(computer != null)
    {
        Soundcard soundcard = computer.getSoundcard();
        if(soundcard != null){
             USB usb = soundcard.getUSB();
             if(usb != null){
                 version = usb.getVersion();
                }
            }
    }
```
改进: flatMap返回Optional对象, map提取属性值
```java
String name = computer.flatMap(Computer::getSoundcard)
                          .flatMap(Soundcard::getUSB)
                          .map(USB::getVersion)
                          .orElse("UNKNOWN");
```
##### 值处理
```java
Optional<Soundcard> soundcard = ...;
soundcard.ifPresent(System.out::println);
```
```java
if(soundcard.isPresent()){
  System.out.println(soundcard.get());
}
```
##### 默认值
```java
Soundcard soundcard = maybeSoundcard != null ? maybeSoundcard : new Soundcard("basic_sound_card");
```
改进:
```java
Soundcard soundcard = maybeSoundcard.orElse(new Soundcard("defaut"));
```
抛异常:
```java
Soundcard soundcard = 
  maybeSoundCard.orElseThrow(IllegalStateException::new);
```
##### 过滤
```java
USB usb = ...;
if(usb != null && "3.0".equals(usb.getVersion())){
  System.out.println("ok");
}
```
改进:
```java
Optional<USB> maybeUSB = ...;
maybeUSB.filter(usb -> "3.0".equals(usb.getVersion())
                    .ifPresent(() -> System.out.println("ok"));
```

### Stream
##### forEach
```java
Random random = new Random();
random.ints().limit(10).forEach(System.out::println);
```
##### map
```java
List<Integer> numbers = Arrays.asList(3, 2, 2, 3, 7, 3, 5);
//get list of unique squares
List<Integer> squaresList = numbers.stream().map( i -> i*i).distinct().collect(Collectors.toList());
```
##### filter
```java
List<String>strings = Arrays.asList("abc", "", "bc", "efg", "abcd","", "jkl");
//get count of empty string
int count = strings.stream().filter(string -> string.isEmpty()).count();
```
##### limit
```java
Random random = new Random();
random.ints().limit(10).forEach(System.out::println);
```
##### sorted
```java
Random random = new Random();
random.ints().limit(10).sorted().forEach(System.out::println);
```
##### Parallel Processing
```java
List<String> strings = Arrays.asList("abc", "", "bc", "efg", "abcd","", "jkl");

//get count of empty string
long count = strings.parallelStream().filter(string -> string.isEmpty()).count();
```
##### Collectors
```java
List<String> strings = Arrays.asList("abc", "", "bc", "efg", "abcd","", "jkl");
List<String> filtered = strings.stream().filter(string -> !string.isEmpty()).collect(Collectors.toList());

System.out.println("Filtered List: " + filtered);
String mergedString = strings.stream().filter(string -> !string.isEmpty()).collect(Collectors.joining(", "));
```
##### Statistics
```java
List numbers = Arrays.asList(3, 2, 2, 3, 7, 3, 5);

IntSummaryStatistics stats = numbers.stream().mapToInt((x) -> x).summaryStatistics();

System.out.println("Highest number in List : " + stats.getMax());
System.out.println("Lowest number in List : " + stats.getMin());
System.out.println("Sum of all numbers : " + stats.getSum());
System.out.println("Average of all numbers : " + stats.getAverage());
```