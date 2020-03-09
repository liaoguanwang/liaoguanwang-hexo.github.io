---
title: linux命令总结
date: 2019-09-18 23:58:51
categories:
- Linux
tags: 
- 命令
keywors: 
description: 
top_img: 
cover: /img/linux.jpg
---
- lfconfig -a
查看虚拟机的IP地址、子网掩码、MAC地址

- netstat -rnv
查看当前路由配置

- su -root
进入root用户目录

- ps -ef|grep processname
查找名字processname的进程（等价于windows任务管理器）
grep表示筛选，grep processname 就是查看进程

- lsof -i | egrep port
查看端口port的监听情况

- vim file 打开文件
i 插入
Esc 退出插入模式
:q 退出
:q! 强制退出
:wq! 强制保存退出  
/word 从上至下搜索word
?word 从下到上搜索Word
  
- kill -9 PID 
结束进程
假设要结束xx进程，可通过ps -ef|grep xx查看它的PID，再使用kill -9 PID。

- ./xxx.sh 
启动服务（等价于windows启动bat）

- df -h 
查看硬盘使用情况

- talif 
实时取日志 talif xxx.log
talif -n 20 filename : 显示filename最后20行

- history 
linux历史命令
history -c - : 历史命令的清楚

- cat 查看文件
cat xxx.log | grep xxx关键字
cat -n 文件名 : 根据行号输出

- cp
cp 原来目标路径/文件名 目标路径名
例子: cp /home/test.txt /home/test/test.txt
cp -R 源目录/* 目的目录
使用-R实现递归，即所有子目录中的文件与目录均拷贝
注意：如果dir2目录不存在，则可以直接使用
cp -r dir1 dir2
如果dir2目录已存在，则需要cp -r dir1 dir2

- nohup
用法：nohup Command [ Arg ... ] [　& ]  
nohup 是 no hang up 的缩写，就是不挂断的意思。  
nohup 命令运行由 Command参数和任何相关的 Arg参数指定的命令，忽略所有挂断（SIGHUP）信号。在注销后使用 nohup 命令运行后台中的程序。要运行后台中的 nohup 命令，添加 & （ 表示“and”的符号）到命令的尾部。  
详细：https://www.cnblogs.com/jinxiao-pu/p/9131057.html

## find命令
- 列出当前目录及子目录所有文件和文件夹
find .

- 在指定目录（及其子目录）下查找指定格式文件名
find 指定目录 -name "*.txt"

- 在指定目录（及其子目录）下查找指定格式文件名，忽略大小写
find 指定目录 -inname "*.txt"

- 当前目录及子目录下查找所有以.txt和.pdf结尾的文件
find . -name ".txt" -o -name "*.pdf"

- 匹配文件路径或者文件
find /usr/ -path "*local*"

- 基于正则表达式匹配文件路径
find . -regex ".*\(\.txt\|\.pdf\)$"

- 基于正则表达式匹配文件路径，忽略大小写
find . -iregex ".*\(\.txt\|\.pdf\)$"

- 反向查找。找到当前目录不以.txt结尾的文件
find . !-name "*.txt"

- 根据文件类型搜索
find . -type 类型参数
  - 类型参数
  - f 普通文件
  - l 符号链接
  - d 目录
  - c 字符设备
  - b 块设备
  - s 套接字
  - p FIFO  

- 基于目录深度搜索
  - 向下最大深度限制3
  find . -maxdepth3 type f
  - 搜索距离当前目录至少2个子目录的所有文件
  find . -mindepth2 -type f

- 根据文件时间戳搜索
find . -type f 时间戳
  - 访问时间（-atime/天，-amin/分钟）
  用户最近一次访问时间
  - 修改时间（-mtime/天，-mmin/分钟）
  文件最后一次修改时间
  - 变化时间（-ctime/天，-cmin/分钟）
  文件数据元（例如权限等）最后一次修改时间
  - 搜索7天内被访问过的所有文件
  find . -type f -atime-7
  - 搜索超过7天被访问过的所有文件
  find . -type f -atime+7

- 根据文件大小匹配
find . type f-size 文件大小单元
  - 文件大小单元
  - b 块（512字节）
  - c 字节
  - w 字（2字节）
  - k 千字节
  - M 兆字节
  - G 吉字节

  - 搜索大于10KB的文件
  find . -type f -size+10k

- 删除匹配文件
  - 删除当前目录下所有.txt文件
  find . -type f -name ".txt" -delete

- 根据文件权限/所有权匹配
  - 当前目录下搜索出权限为777的文件
  find . -type f -perm 777
  - 找出当前目录用户tom拥有的所有文件
  find . -type f -user tom
  - 找出当前目录用户组sunk拥有的所有文件
  find . -type f -group sunk

- 借助-exec选项与其他命令结合使用
  - 查找当前目录下所有.txt文件并把他们拼接起来写入all.txt文件中
  find . -type f -name "*.txt" -exec cat {}\;> all.txt
  - 将30天前log文件移动到old目录中
  find .type f -mtime +30 -name "*.log" -exec cp {} old\;
  - 找出当前目录下所有.txt并以"File:文件名"的形式打印出来
  find . type f -name "*.txt" -exec printf "File:%s" {}\;

## grep命令
- 从单个文件中查找指定字符串
grep "this" demp_file

- 从多个文件中查找指定的字符串
grep "this" demo_*

- 忽略大小写使用grep -i
grep -i "the" demo_file

- 在文件中匹配正则表达式
grep -w "is" demo_file

- 用grep -A，-B，-C来查看after/before/aroud行
grep -A 3 -i "example" demo_file
grep -B 2"single WORD" demo_file
grep -C 2"Example" demo_file

- 用grep -r来搜索所有的文件及子目录
grep -r "this" *

- 用grep -v来显示不匹配的项
grep -v "go" demo_file

- 用grep -c来计算匹配的数量
grep -c "go" demo_file

- 计算匹配的模式数
grep -c this demo_file

- 计算不匹配的模式数
grep -v -c this demo_file

- 使用grep -l显示匹配的文件名
grep -l this demo_*

- 只显示匹配的字符串
grep -o "is.*line" demo_file

- 显示匹配的字节位置
grep -o -b "b" test_file

- 用grep -n显示行数
显示关键字"this"在文件中的所在行数
grep -n "this" demo_file

- grep -E 匹配多个

## ps命令
- ps -A
显示所有进程

- ps -u root
显示指定用户信息

- ps -ef
显示所有进程信息，连同命令行

- ps -ef | grep ssh
ps与grep常用组合，查找特定进程

- ps -l
将目前属于自己注册登录的PID与相关信息列展示出来

- ps aux
列出目前所有的正在内存中的程序

- ps -axjf
列出类似程序树的程序显示

- ps aux | egrep '(cron|syslog)'
找出与cron和syslog这两个服务有关的PID号码

- ps -aux> ps001.txt
把所有进程显示出来，并输出到ps001.txt

- ps -opid,ppid,pgrp,session,tpgid,comm
输出指定字段

未完待续...