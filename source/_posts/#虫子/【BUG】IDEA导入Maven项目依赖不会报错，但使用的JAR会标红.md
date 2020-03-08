---
title: 【BUG】IDEA导入Maven项目依赖不会报错，但使用的JAR会标红
date: 2019-08-12 00:21:36
tags:
- Maven
categories:
- BUG
keywors: 
description: 
top_img: 
cover: /img/bug.jpg
---

### 方法1：
1.通过编译找到报错的jar; 
2.在 repository找到此jar,一般未下载完大小为1k我的是这样(); 
3.删除未下载完全的jar,在项目上执行maven reimport会重新下载jar;

### 方法2
Idea的File -> Invalidate Caches/Restart...

### 方法3
Idea右边Maven框，点击clean再重新Reimport