---
title: 我的 Intelij Idea 配置
date: 2019-02-22 14:36:26
categories: 工具
---
我常用的Idea字体，插件等配置，欢迎查阅
<!--more-->

### 代码模板
> 在 Preference/Editor/File and Code Templates
- Class
```
#if (${PACKAGE_NAME} && ${PACKAGE_NAME} != "")package ${PACKAGE_NAME};#end
#set($myName = 'xxx')

/**
 * ${DESCRIPTION}
 * @author ${myName}
 * @date ${YEAR}-${MONTH}-${DAY} ${TIME}
 */
public class ${NAME} {
}
```
- Interface
```
#if (${PACKAGE_NAME} && ${PACKAGE_NAME} != "")package ${PACKAGE_NAME};#end
#set($myName = 'xxx')

/**
 * ${DESCRIPTION}
 * @author ${myName}
 * @date ${YEAR}-${MONTH}-${DAY} ${TIME}
 */
#parse("File Header.java")
public interface ${NAME} {
}

```
- Enum
```
#if (${PACKAGE_NAME} && ${PACKAGE_NAME} != "")package ${PACKAGE_NAME};#end
#set($myName = 'xxx')

/**
 * ${DESCRIPTION}
 * @author ${myName}
 * @date ${YEAR}-${MONTH}-${DAY} ${TIME}
 */
#parse("File Header.java")
public enum ${NAME} {
}

```
### 字体
> 在 Preference/Editor/Font

很喜欢mac字体Menlo，windows可自行下载字体安装，逐个点击字体文件安装即可

插件
---
- [Lombok](https://plugins.jetbrains.com/plugin/6317-lombok-plugin) : 简化Java代码，自动生产Setter、Getter等，详情见[官网](https://projectlombok.org/)
- [Free Mybatis](https://plugins.jetbrains.com/plugin/8321-free-mybatis-plugin) :可以将Mapper.java与xml文件关联起来，点击可以跳转到对应的方法中
- [Maven Helper](https://plugins.jetbrains.com/plugin/7179-maven-helper) :maven解决冲突，查看maven引用tree
- [Alibaba Java Coding Guidelines](https://plugins.jetbrains.com/plugin/10046-alibaba-java-coding-guidelines) : 阿里代码规约，根据阿里代码规约自动提示