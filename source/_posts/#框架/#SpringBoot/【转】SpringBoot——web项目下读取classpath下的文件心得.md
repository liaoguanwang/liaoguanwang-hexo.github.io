---
title: SpringBoot——web项目下读取classpath下的文件心得
date: 2019-08-13 23:52:40
tags:
- 配置
categories: 
- SpringBoot
keywors: 
description: 
top_img: 
cover: /img/springboot.jpg
---

在读取springBoot+gradle构建的项目时，如果使用传统的FileInputStream读取文件流或者ResourceUtils工具类的方式，都会失败，下面解释原因：

## 一、读取文件的三种方式：
### 1. ResourceUtils工具类
```
import org.springframework.util.ResourceUtils;
//使用：
File file= ResourceUtils.getFile("classpath:test.txt");
```

### 2. FileInputStream文件流的方式读取
![](https://img2018.cnblogs.com/blog/1351916/201810/1351916-20181007134706666-1792888992.png)
（该方式为按行读取，若不是按行处理，需要像图中将每行数据存在一个buffer中，然后转成String处理）

### 3. ClassPathResource获取文件流的方式
```
ClassPathResource classPathResource = new ClassPathResource("test.txt");
 
获取文件：classPathResource .getFile();
 
获取文件流：classPathResource .getInputStream();
```

## 二、不同web容器读取文件的区别
有两种常见的web容器：
1. 第一种是普通的web项目，特点是jar/war压缩包会随着容器的启动解压缩成一个文件夹，当项目访问的时候，实际是访问文件夹，而非jar或者war包。该种方式下，用获取路径的方法：this.getClass().getResource("/")+fileName或者获取流的方法：this.getClass().getResourceAsStream(failName);都可以成功。

2. 第二种是内嵌web容器，Spring boot就是内嵌web容器，其特点是只有一个jar文件，在容器启动后不会解压缩，项目实际访问的就是jar/war包该种方式最容易遇坑！！最大的坑就是，this.getClass().getResource("/")+fileName在本地windows下能完美找到路径，可是在linux测试服务器下就失败，所以读取jar中的文件只能用流读取，不能用file，即只能用方式三读取。所以，用spring boot搭建的工程，只能用
```
classPathResource .getInputStream();获取文件流。
```

转自：https://blog.csdn.net/NNnora/article/details/80734326