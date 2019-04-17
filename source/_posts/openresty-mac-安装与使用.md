---
title: openresty mac 安装与使用
date: 2018-12-24 11:52:25
categories: openresty
---
openresty详细安装步骤
<!--more-->

### 安装
1. 依赖安装	
```shell
brew update
brew install pcre openssl
```
 2. 安装
```shell
brew install homebrew/nginx/openresty
```
2. 设置环境变量
```shell
PATH=/usr/local/opt/openresty/nginx/sbin:$PATH
export PATH
```
3.启动查看
查看版本信息
```shell
nginx -V
```
启动
```shell
nginx
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181224114653561.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3lzdzExMzI=,size_16,color_FFFFFF,t_70)

常用命令
```shell
启动:nginx
停止:nginx -s stop   停止nginx也停止了openresty
重启:nginx -s reload
检验nginx配置是否正确: nginx -t
```
### Hello World
- 创建测试目录
```
mkdir ~/openresty-test ~/openresty-test/logs/ ~/openresty-test/conf/
```
- 在conf文件下创建nginx.conf文件
```shell
worker_processes  1;
error_log logs/error.log;
events {
    worker_connections 1024;
}
http {
    server {
        listen 8088;
        location / {
            default_type text/html;
            content_by_lua '
                ngx.say("hello lua")
            ';
        }
    }
}
```
- nginx -p ${pwd} -c conf/nginx.conf 
- 访问 http://localhost:8888