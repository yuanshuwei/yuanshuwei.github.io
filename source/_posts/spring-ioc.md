---
title: spring ioc aop
date: 2019-07-16 18:51:26
tags:
categories:
---

spring ioc aop

<!-- more -->

spring ioc
---

依赖注入:底层类作为参数传递给上层类，实现下层对上层的控制。  
依赖注入方式:
- Setter
- Interface
- Constructor
- Annotation

![](/images/spring-ioc.png)

通过 `BeanDefinition` 来描述bean的定义，`BeanDefinitionRegistry` 的`registerBeanDefinition` 来注册。  
Bean最终存在 `DefaultListableBeanFactory` 中, 用ConcurrentHashMap存储的Bean名称和BeanDefinition

```
Map<String, BeanDefinition> beanDefinitionMap = new ConcurrentHashMap(256)
List<String> beanDefinitionNames = new ArrayList(256)
```

#### BeanFactory
- 提供ioc的配置机制
- 包含Bean的各种定义，便于实例化Bean
- 建立Bean之间的依赖关系
- Bean的生命周期的控制

#### BeanFactory与ApplicationContext
BeanFactory是spring的基础设施，面向spring  
ApplicationContext面向使用spring的开发者  

#### ApplicationContext

继承如下接口：
```
EnvironmentCapable, ListableBeanFactory, HierarchicalBeanFactory, MessageSource, ApplicationEventPublisher, ResourcePatternResolver
```
- BeanFactory: 能够管理，装配Bean
- ResourcePatternResolver: 能够加载资源文件
- MessageSource: 能够实现国际化等功能
- ApplicationEventPublisher： 能够注册监听器，实现监听机制

#### getBean方法的代码逻辑
- 转换beanName
- 从缓存种加载实例
- 实例化Bean
- 检测parentBeanFactory
- 初始化依赖的Bean
- 创建Bean

#### spring bean的作用域
- singleton: spring的默认作用域，容器里拥有唯一的bean实例
- prototype: 针对每个getBean请求，容器
- request: 会为每个Http请求创建一个Bean实例
- session: 会为每个session创建一个session实例
- globalSession: 会为每个全局Http Session创建一个Bean实例，该作用域仅对Portlet有效

#### spring bean的生命周期

##### 创建
1. 实例化bean
2. Aware(注入Bean ID，BeanFactory和AppCtx)
3. BeanPostProcessor(s) `postProcessBeforeInitialization`
4. InitializingBean(s). `afterPropertiesSet`
5. 定制的Bean init 方法
6. BeanPostProcessor(s). `postProcessAfterInitialzation`
7. Bean初始化完毕

##### 销毁
1. 若实现了 `DisposableBean` 接口，则会调用destroy方法
2. 若配置了 `destroy-method` 属性, 则会调用其配置的销毁方法

AOP
---

实现: `JdkProxy` 和 `Cglib`
- 由AopProxyFactory根据AdvisedSupport对象的配置来决定
- 默认策略如果目标类是接口，则用JdkProxy来实现，否则用后者
- JdkProxy的核心: InvocationHandler接口和Proxy类
- Cglib： 以继承方式动态生成目标类的代理
- JdkPeoxy是Java内部的反射机制实现的， Cglib结果ASM实现
- 反射机制在生成类的过程比较高效；ASM在生成类之后的执行过程比较高效

代理模式: 接口 + 真是实现类 + 代理类

#### spring代理模式的实现
- 真实实现类的逻辑包含在getBean方法里
- getBean方法返回的实际是Proxy的实例
- Proxy实例是spring通过JDK Proxy或CGLIB动态生成的

ACID/隔离级别/事务传播
> [参考这个吧](https://www.cnblogs.com/ooo0/p/11029658.html)