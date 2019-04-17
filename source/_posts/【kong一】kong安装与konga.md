---
title: 【kong一】kong安装与konga
date: 2018-12-28 14:12:50
categories: 工具框架
---
kong的详细安装过程，以及通过konga管理台管理
<!--more-->
环境
>ubuntu

### 一、安装PostgreSql
kong需要用到数据库，目前支持
[psql安装过程参考](http://www.ruanyifeng.com/blog/2013/12/getting_started_with_postgresql.html)
安装完成创建一个用户:kong，密码:kong，数据库:kong
### 二、安装Kong
#### 1. [文件下载](https://getkong.org/install/ubuntu/)
#### 2. 安装
```shell
sudo apt-get update
sudo apt-get install openssl libpcre3 procps perl
sudo dpkg -i kong-community-edition-0.11.0.*.deb
```
#### 3. 修改数据库配置文件
```shell
cp /etc/kong/kong.conf.default /etc/kong/kong.conf
vim /etc/kong/kong.conf
```
示例：
```
pg_host = 127.0.0.1             # The PostgreSQL host to connect to.
pg_port = 5432                  # The port to connect to.
pg_user = kong                  # The username to authenticate if required.
pg_password = kong              # The password to authenticate if required.
pg_database = kong
```
#### 4. 启动
```shell
kong migrations up
kong start
```
#### 5. 验证
```
curl http://127.0.0.1:8001
```
输出大段json信息说明成功

#### 6. 端口说明
8000端口：外部调用api端口，比如设置Urlis=/test, 则访问地址是:  http://[IP]:8000/test
8001端口: api 管理端口，可通过restful接口管理kong
#### 7. 文档
https://docs.konghq.com/
### 三、安装Konga
说明：
>- 官网推荐: [kong-dashboard](https://github.com/PGBI/kong-dashboard)，但对比界面高端程度和友好度，更推荐konga.
>- [一个坑]kong版本问题：我在安装时目前kong最新版本已经到1.0.0, 对于konga和kong-dashboard还不支持，建议安装低版本0.15以下; 具体表现查询apis，旧版本:[ip]:8001/apis,新版:[ip]:8001/services

#### npm安装方式
#### 1. 安装依赖
```shell
sudo apt-get install nodejs npm
sudo npm install -g gulp
sudo npm install -g bower
sudo npm install -g sails
```
#### 2. 安装
```shell
git clone https://github.com/pantsel/konga.git
cd konga
npm install konga
```
#### 3. 配置数据库信息
目前支持数据库: mysql, mongo, sqlserver, postgres
```shell
cd config/
cp local_example.js local.js
vi local.js

models: {
    connection: process.env.DB_ADAPTER || 'localDiskDb',
}
# 改成
models: {
    connection: process.env.DB_ADAPTER || 'mysql', // 这里可以用‘mysql’，‘mongo’，‘sqlserver’，‘postgres’
}
```
```shell
# 修改数据库默认配置
vi konga/config/connections.js
mysql: {
    adapter: 'sails-mysql',
    host: process.env.DB_HOST || 'localhost',
    port: process.env.DB_PORT || 3306,
    user: process.env.DB_USER || 'root',
    password: process.env.DB_PASSWORD || null,
    database: process.env.DB_DATABASE || 'konga_database'
}
# 改成
mysql: {
    adapter: 'sails-mysql',
    host: process.env.DB_HOST || 'localhost',
    port: process.env.DB_PORT || 3306,
    user: process.env.DB_USER || 'root',
    password: process.env.DB_PASSWORD || 'root',
    database: process.env.DB_DATABASE || 'konga_database'
}
```
安装依赖
```shell
npm run bower-deps
npm install dotenv-extended
npm install angular
```
启动
```shell
#konga根目录
npm start
```
访问: http://127.0.0.1:1338
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181228114218994.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3lzdzExMzI=,size_16,color_FFFFFF,t_70)