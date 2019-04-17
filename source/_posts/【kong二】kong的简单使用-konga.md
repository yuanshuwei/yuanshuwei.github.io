---
title: 【kong二】kong的简单使用-konga
date: 2018-12-28 14:29:07
categories: 工具框架
---
通过konga简单的配置kong，的使用过程
<!--more-->
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181228142514513.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3lzdzExMzI=,size_16,color_FFFFFF,t_70)
如图，
- Upstream Url: 重定向的地址是实际的接口地址，或者upstreams地址(此时upstream配合多台机器接口，可做负载均衡)
- Hosts:kong主机ip
- Uris：访问地址
- 此时我们访问http://{hosts}:8000/Uris，则指向了Upstream URL

### 负载
- 1.配置UPSTREAMS
新建一个UPSTREAMS，在其下增加多个Targets：Target配置为ip:port,修改Weight权重


-  2.配置APIS
新建APIS，配置填写如下
```
{
    "name":"test",
    "host":"${kong所在主机ip}",
    "Uris":"/api/test",
    "Upstream URL":"http://${UPSTREAMS.name}/api/test",
}

```
kong访问地址: 
> http://${kong主机}:8000/${Uris}