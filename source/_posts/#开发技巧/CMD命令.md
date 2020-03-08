---
title: CMD命令
date: 2019-09-20 23:45:45
categories:
- 开发技巧
tags:
- 命令
keywors: 
description: 
top_img: 
cover: /img/hacker.jpg
---
### 1.代码补全
1. win+r打输入regedit 打开注册表
<img src="https://img2018.cnblogs.com/blog/1351916/201907/1351916-20190730230515541-1255840934.png" heigh="200" width="300">

2. 进入HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Command Processor\CompletionChar，双击CompletionChar， 将值改为9，选择十进制，点击确定
<img src="https://img2018.cnblogs.com/blog/1351916/201907/1351916-20190730232124698-280245012.png" heigh="490" width="630">

3. 以后运行CMD的时候 输入首字母按住 TAB键就能自动补全输入的命令了。

未完待续...