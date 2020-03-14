---
title: >-
  【BUG】The remote endpoint was in state [TEXT_PARTIAL_WRITING] which is an
  invalid stat
categories:
- BUG
tags:
- 多线程
- WebSocket
- Session
date: 2020-02-10 23:59:05
keywors:
description:
top_img:
cover: /img/multithread.jpg
---

## 报错信息
Caused by: java.lang.IllegalStateException: The remote endpoint was in state [TEXT_PARTIAL_WRITING] which is an invalid state for called method

## 解决方法
加锁，保证一个session在某个时刻不会被调用多次

参考：https://blog.csdn.net/qq_20641565/article/details/80857408