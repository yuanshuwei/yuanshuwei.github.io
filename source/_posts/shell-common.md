---
title: 常见shell
date: 2019-07-18 00:08:46
tags:
categories:
---

常见shell

<!-- more -->

- 文件检索: find
```
find ~ -name "build.sh"　#　精确查找


find ~ -name "build.*"　# 通配符

find ~ -iname "build.sh"  # 忽略大小写

man find
```

- 文件内容检索: grep

- grep [options] pattern file

    ```
    grep "moo" target* # 查询包含内容"moo"并且文件名以target开头的文件和目标字段串所在的行的内容 
    ```
  - `grep "ss" ss.log`  查找包含你指定内容的行，并打印你呢
  - `grep -o` 只显示想要的数据部分
  - `grep -v` 过滤掉目标数据
- 管道操作符 |  输出作为后面的输入    
  ```
  find ~ | grep "target"
  ```
- 文件呢内容的统计: awk
```
awk [options] 'cmd' file ＃ 特别适合表格类数据，统计等
awk '{print $1,$4}' netstat.txt #打印第一行和第四行, awk默认以空格分割
```
- 批量替换文本内容: sed
```
sed -i 's/^Str/String/' xx.java #Str开头的Str替换为String
sed -i '/\.$/\;/' xx.java #.结尾的替换为;
sed -i '/Jack/me/g' xx.java ＃Jack替换成me，g是整行替换
```