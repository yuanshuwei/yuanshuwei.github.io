---
title: logback kafka输出日志到ELK
date: 2019-02-23 16:14:35
categories: 工具框架
---
详细步骤，Spring日志通过logstash、kafka打印到ELK，方便查阅
<!--more-->

> 参考 [logback+kafka+elk搭建日志](https://www.jianshu.com/p/d1be3364f32d), 学习总结

> 日志流程: logback -> kafka -> logstash -> elasticsearch -> kibana

### kafka安装启动
- [官方下载](http://kafka.apache.org/downloads)， 选择Binary downloads下载
- 先启动zookeeper
`bin/zookeeper-server-start.sh config/zookeeper.properties &`
- 启动kafka
`bin/kafka-server-start.sh config/server.properties &`
### logback与kafka集成
kafka与logback使用的是 [logback-kafka-appender](https://github.com/danielwegener/logback-kafka-appender)
- 引入pom依赖
```xml
<!--kafka依赖-->
<dependency>
	<groupId>org.springframework.kafka</groupId>
    <artifactId>spring-kafka</artifactId>
    <version>2.1.6.RELEASE</version>
</dependency>
<!--logback-kafka-appender依赖-->
<dependency>
	<groupId>com.github.danielwegener</groupId>
    <artifactId>logback-kafka-appender</artifactId>
    <version>0.2.0-RC2</version>
</dependency>
```
- 配置`logback-spring.xml`:
>SpringBoot加载顺序: `logback-spring.xml> logback-spring.groovy> logback.xml> logback.groovy`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration  scan="true" scanPeriod="60 seconds" debug="false">
    <contextName>logback</contextName>
    <!--定义日志文件的存储地址 勿在 LogBack 的配置中使用相对路径-->
    <property name="LOG_HOME" value="/data/logs" />
    <!--输出到控制台-->
    <appender name="console" class="ch.qos.logback.core.ConsoleAppender">
        <!--格式化输出：%d表示日期，%thread表示线程名，%-5level：级别从左显示5个字符宽度%msg：日志消息，%n是换行符-->
        <encoder>
            <pattern>%d{HH:mm:ss.SSS} %contextName [%thread] %-5level %logger{36} - %msg%n</pattern>
        </encoder>
    </appender>

    <appender name="kafkaAppender" class="com.github.danielwegener.logback.kafka.KafkaAppender">
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <pattern>%d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n</pattern>
        </encoder>
        <topic>applog</topic>
        <!-- we don't care how the log messages will be partitioned  -->
        <keyingStrategy class="com.github.danielwegener.logback.kafka.keying.NoKeyKeyingStrategy" />

        <!-- use async delivery. the application threads are not blocked by logging -->
        <deliveryStrategy class="com.github.danielwegener.logback.kafka.delivery.AsynchronousDeliveryStrategy" />

        <!-- each <producerConfig> translates to regular kafka-client config (format: key=value) -->
        <!-- producer configs are documented here: https://kafka.apache.org/documentation.html#newproducerconfigs -->
        <!-- bootstrap.servers is the only mandatory producerConfig -->
        <producerConfig>bootstrap.servers=localhost:9092</producerConfig>
        <!-- don't wait for a broker to ack the reception of a batch.  -->
        <producerConfig>acks=0</producerConfig>
        <!-- wait up to 1000ms and collect log messages before sending them as a batch -->
        <producerConfig>linger.ms=1000</producerConfig>
        <!-- even if the producer buffer runs full, do not block the application but start to drop messages -->
        <producerConfig>max.block.ms=0</producerConfig>
        <!-- define a client-id that you use to identify yourself against the kafka broker -->
        <producerConfig>client.id=${HOSTNAME}-${CONTEXT_NAME}-logback-relaxed</producerConfig>
    </appender>

    <root level="info">
        <appender-ref ref="console" />
        <appender-ref ref="kafkaAppender" />
    </root>
</configuration>

```
### logstash配置启动
> ELK的安装使用可以参考 [ELK安装使用](https://blog.csdn.net/ysw1132/article/details/84941747)
- 配置kafka接收数据，输出到es, index是 test-kafka
```shell
input {
     kafka {
        topics => "applog"
        bootstrap_servers => "localhost:9092"
        group_id => "es"
    }
}
output {
  elasticsearch {
    hosts => "localhost:9200"
    index => "test-kafka"
  }
}
```
- 启动
`./bin/logstash -f test-kafka.conf`
### elasticsearch启动
`./bin/elasticsearch`

### kibana
`./bin/kibana`
### 验证
- 启动程序输出日志
```java
@Slf4j
@SpringBootApplication
public class LogKafkaApplication {

    public static void main(String[] args) throws InterruptedException {
        SpringApplication.run(LogKafkaApplication.class, args);

        while (true) {
            Thread.sleep(5000);
            log.info("log to kafka...");
        }
    }

}
```
访问 `http://127.0.0.1:5601`, 在`test-kafka`下出现了日志,如图
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190223161033739.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3lzdzExMzI=,size_16,color_FFFFFF,t_70)