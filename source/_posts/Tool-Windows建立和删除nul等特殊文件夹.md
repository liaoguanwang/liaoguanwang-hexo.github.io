---
title: Windows建立和删除nul等特殊文件夹
categories:
- Tool
tags:
- CMD
- Windows
date: 2020-01-10 00:09:52
keywors:
description:
top_img:
cover: /img/windows.jpg
---

## Windows设备名称
"aux","com1","com2","prn","con","nul"等，因为这些名字都属于设备名称，等价于一个 DOS 设备，如果我们把文件命名为这些名字，Windows 就会误以为发生重名，所以会提示“不能创建同名的文件”等等。

## 建立设备名文件夹
命令提示符下执行"md C:\nul\\"，就在C盘建立一个名叫nul的文件夹。
此文件夹虽然可以访问，也可以建立子文件夹，但无法删除，因为Windows不允许以这种方式删除。在系统看来，nul文件夹就是个设备

## 删除设备名文件夹
按照完整的UNC路径格式，就是网上邻居的路径格式，正确输入文件路径和文件名即可。
比如删除C盘下的nul文件夹，在命令提示符下执行：
- rd /s \\.\C:\nul  
rd 是命令提示符删除文件夹的命令，/s 参数表示从所有子目录删除指定文件
- del \\.\C:\nul
