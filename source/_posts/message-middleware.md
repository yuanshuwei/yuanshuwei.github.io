---
title: 消息中间件面试常见问题
date: 2019-05-14 20:28:22
tags: 消息中间件
categories: 面试
---
消息中间件面试常见问题, [整理自github](https://github.com/doocs/advanced-java)
<!-- more -->

### 一、为什么用消息队列?  
- **解耦**(发布订阅模型)、**异步**(速度快)、**削峰**(从mq拉的速度控制一下)
- **可用性降低**(mq挂)、**系统复杂度高**(消息重复、消息丢失、消息顺序)、**一致性问题**(多个系统消费可能存在失败的)
- 结合项目体现
  
### 二、ActiveMQ、RabbitMQ、RocketMQ、Kafka优缺点，如何选型
- ActiveMQ社区不活跃，没经过大规模吞吐场景验证，不推荐
- RabbitMQ，erlang开发，阻止了大多人深入底层，中小公司推荐；社区活跃不会黄
- RocketMQ，适合大公司，自己有实力
- Kafka，大数据实时计算，日志采集的业界标准

### 三、如何保证消息队列的高可用？
- RabbitMQ, 基于主从
  - 普通集群模式 (无高可用，数据只存在一台机器，消费时需要mq实例去实际存数据的mq拉取数据,一台那个实例机器挂了系统就挂了)
  - 镜像集群模式 (数据会在多个mq实例之间同步可实现高可用；但是queue的数量无法扩展，如果queue的数量大到机器无法承受，再加机器也是没用的)
- Kafka,
  - 架构认识：由多个Broker组成，创建一个topic可以划分为多个partition，每个partition存在不同的broker里。就是说每个topic的数据存在不同的机器中，3个partition就是3个机器中
  - 0.8以前，如果一个实例挂，就相当于topic丢了一部分数据。之后加入了replica副本，partition会存在副本；自动选举leader和follower；
  - 读写全部从leader节点；写数据的时候，leader存本地磁盘，follower主动拉leader拉，全部follower拉完会发ack给leader，再返回给生产者；读数据时，只有所有follower都返回ack同步成功的才会被消费

### 四、 消息队列的重复消费、幂等性？
- 都可能出现重复消费，这是开发来保证的不是MQ
- 对于kafka，有一个offset概念，代表消息的序号，每隔一段时间消费者会把消费过的offset提交，表示已经消费过了，但异常情况来不及提交，会重复消费，所以需要我们开发自己来保证幂等性

### 五、如何保证可靠性、消息丢失？
- RabbitMQ
  - 生产者丢失数据: `confirm`模式,生产者设置开启confirm模式后，每次写消息会被分配一个全局唯一id，如果写入成功会返回ack，失败则回调`nack接口告诉你接收失败可以重试；过程是异步的
  - MQ丢失数据: 数据持久化到本地，启动后数据读取之前存储的数据，极少数存在还未持久化MQ就已经挂了; 也可以与生产者ack关联，为持久化的消息重新发送
  - 消费者丢失数据: 刚消费到还没处理，进程就挂了；可以用ack，每次消费完成发送ack，否则MQ认为没有被消费
- Kafka
  - 消费端丢失: 消费了未处理，就自动提交了offset；此情况可以关闭自动提交，处理完成后才手动提交
  - Kafka丢失: 某个Broker中leader挂了，还未来得及同步partition，进行了选举；所以配置一些参数:
    - **保证每个partition至少有两个副本**: topic的 `replication.factor`  大于1
    - **leader感知至少有一个follower存在**: kafka设置`min.insync.replicas`大于1
    - **写入所有的replica,才认为是成功**: producer设置 `acks=all` 
    - **一旦写入失败无限重试**: producer设置 `retries=MAX` 
  - 生产者不会丢失，因为所有到接收到才算成功

### 六、消息消费的顺序性?
- 保证一个queue或topic，或同一类的消息由同一个消费者消费
- [参见](https://github.com/doocs/advanced-java/blob/master/docs/high-concurrency/how-to-ensure-the-order-of-messages.md)

### 七、延时过期失效、队列满了、消息积压几个小时，怎么解决？
[这里](https://github.com/doocs/advanced-java/blob/master/docs/high-concurrency/mq-time-delay-and-expired-failure.md)
- 就是消费端出现了问题

### 八、设计消息队列的思路
[这里](https://github.com/doocs/advanced-java/blob/master/docs/high-concurrency/mq-design.md)

[RocketMQ 实战之快速入门](https://www.jianshu.com/p/824066d70da8)