---
title: 通过nginx自建文件共享服务，用手机播放电脑的视频
date: 2019-05-05 16:49:28
tags: 
categories: 好玩
---
### 背景
> 最近想把一些学习视频共享给小伙伴，结果发现网盘生成链接很容易失效，下载限速严重；加上手机内存有限观看下载好的视频很不方便；所以觉得搞一下本地文件服务器，来分享文件；同时手机也可以连接在线观看视频，因为是内网，速度拉满
<!--more-->
### 环境
mac(windows有乱码问题)  
安装好nginx
### 配置

`nginx.conf`
```
http {
    server {
        charset      utf-8,gbk; # windows 服务器下设置后，依然乱码，暂时无解
        listen       80 default_server;
        listen       [::]:80 default_server;
        server_name  _;
        location / {
            root         /Users/yuan/sp; #共享目录，Windows目录这样写 d://www//
            autoindex on; ##显示索引
            autoindex_exact_size on; ##显示大小
            autoindex_localtime on;   ##显示时间
        }
    }
}
```
注:
查看默认nginx配置文件位置执行: `nginx -h`, 如下可知，-c对应的/usr/local/etc/nginx/nginx.conf，即是当前默认配置文件
```
yuandeMacBook-Pro-2:nginx yuan$ nginx -h
nginx version: nginx/1.15.3
Usage: nginx [-?hvVtTq] [-s signal] [-c filename] [-p prefix] [-g directives]

Options:
  -?,-h         : this help
  -v            : show version and exit
  -V            : show version and configure options then exit
  -t            : test configuration and exit
  -T            : test configuration, dump it and exit
  -q            : suppress non-error messages during configuration testing
  -s signal     : send signal to a master process: stop, quit, reopen, reload
  -p prefix     : set prefix path (default: /usr/local/Cellar/nginx/1.15.3/)
  -c filename   : set configuration file (default: /usr/local/etc/nginx/nginx.conf)
  -g directives : set global directives out of configuration file
```

### 使用
启动nginx  
访问`http://127.0.0.1`即可，手机访问更换为电脑ip

### 参考
nginx命令及教程推荐参考 [Nginx极简教程](https://github.com/dunwu/nginx-tutorial)

[自建视频网站](https://www.tiezi.xyz/archives/647.html)