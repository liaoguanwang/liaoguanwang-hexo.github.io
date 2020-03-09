---
title: IntelliJ IDEA控制台输出中文乱码问题
date: 2019-08-03 23:46:24
tags:
- 乱码
categories: 
- BUG
keywors: 
description: 
top_img: 
cover: /img/bug.jpg
---
### 解决方法1
1. 打开tomcat配置页面，Edit Configurations。
2. 选择项目部署的tomcat，在配置项VM options文本框中输入-Dfile.encoding=UTF-8,点击Apply或OK即可。

### 解决方法2
1. 打开IntelliJ IDEA本地安装目录中bin文件夹下的idea.exe.vmoptions和idea64.exe.vmoptions这两个文件。
![](https://img2018.cnblogs.com/blog/1351916/201908/1351916-20190814230040408-1249569017.png)

2. 分别在这两个文件内容的末尾添加-Dfile.encoding=UTF-8
![](https://img2018.cnblogs.com/blog/1351916/201908/1351916-20190814230500139-1390703469.png)

![](https://img2018.cnblogs.com/blog/1351916/201908/1351916-20190814230538725-503134338.png)

3. 打开IntelliJ IDEA>File>Setting>Editor>File Encodings，将Global Encoding、Project Encoding、Default encodeing for properties files这三项都设置成UTF-8，点击OK或者Apply。
![](https://img2018.cnblogs.com/blog/1351916/201908/1351916-20190814230719415-1359649962.png)