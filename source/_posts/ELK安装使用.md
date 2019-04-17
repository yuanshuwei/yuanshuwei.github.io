---
title: ELK安装使用
date: 2018-12-10 16:11:35
categories: 工具框架
---
ELK安装详细步骤，欢迎参考
<!--more-->
## logstash
>需要java环境

[官方下载地址](https://www.elastic.co/downloads/logstash)
cd [logstash主目录]
vim test.conf
```conf
input {
 stdin { }
}
output {
 stdout {
 codec => rubydebug {}
 }
}
```
运行: bin/logstash -f test.conf
测试结果
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181210155547275.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3lzdzExMzI=,size_16,color_FFFFFF,t_70)
上面结果表示成功

## elasticsearch
[官方下载地址](https://www.elastic.co/downloads/elasticsearch)
cd [elasticsearch主目录]
vim config/elasticsearch.yml
```conf
path.data: /data/es #数据路径
path.logs: /data/logs/es #日志路径
network.host: 本机地址 #服务器地址
http.port: 9200 #端口
```
启动: bin/elasticsearch
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181210155946530.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3lzdzExMzI=,size_16,color_FFFFFF,t_70)

## 集成logstash和elasticsearch
修改logstash配置文件
```conf
input {
 stdin { } 
}
output {
 elasticsearch {
 hosts => "elasticsearchIP:9200"
 index => "logstash-test"
 } 
 stdout {
 codec => rubydebug {}
 } 
}
```
再次启动logstash, 输入"hello elasticsearch"

![在这里插入图片描述](https://img-blog.csdnimg.cn/2018121016052219.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3lzdzExMzI=,size_16,color_FFFFFF,t_70)
## kibana
[官方下载地址](https://www.elastic.co/downloads/kibana)
cd [kibana主目录]
vim config/kibana.yml
```conf
server.port: 5601 #kibana端口
server.host: “本机ip” #kibana的ip
elasticsearch.url: “http://elasticsearchIP:9200” #elasticsearch地址
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181210160927279.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3lzdzExMzI=,size_16,color_FFFFFF,t_70)

SpringBoot-ELK demo: [推荐](https://howtodoinjava.com/microservices/elk-stack-tutorial-example/)