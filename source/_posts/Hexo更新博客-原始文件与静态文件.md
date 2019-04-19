---
title: Hexo更新博客-原始文件与静态文件
date: 2018-12-11 17:03:21
tags: hexo
categories: 博客搭建
---
前言
---
通过Hexo发布的博客，通过本地原始文件 "hexo g -d" 将生成的静态文件发布到github上，原始文件留在了本地。这样无法在其他电脑或文件丢失情况下更新发布博客。  
<!--more-->
解决
---
利用github的分支，创建两个分支master和hexo；  hexo分支存储原始文件，master存储博客静态页面，原始文件push到hexo分支，而静态文件直接发布到了master分支。  
_config.yml中的deploy参数，分支应为master。

日常博客修改
---
包括新博文和样式的修改  

1.提交原始文件到分支hexo
```
git add .
git commit -am ""
git commit origin hexo
```
2.发布网站到master分支上  
```
hexo g -d
```
>注意顺序

在没有原始文件的电脑上修改
---
1. 拉hexo分支的原始代码到本地

```
git clone ...
```
2. 下载npm包(需要有node环境)

```
npm install hexo
npm install
npm install hexo-deployer-git
```
> 不需要 hexo init
