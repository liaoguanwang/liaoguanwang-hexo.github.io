---
title: Java——string类型与date类型之间的转化
date: 2019-08-29 23:50:39
tags:
---
## 1. String类型转化为Date类型

### 1.1 方法一
Date date=new Date("2019-01-25");

### 1.2 方法二
String =(new SimpleDateFormat("格式")).format(Date); 

### 1.3 方法三
SimpleDateFormat sdf=new SimpleDateFormat("yyyy-MM-dd");String dstr="2008-4-24";java.util.Date date=sdf.parse(dstr);

## 2. Date类型转化为String类型

### 2.1 方法一
StringDateFormat stringDateFormat = new StringDateFormat("yyyy-MM-dd");
java.util.Date date = new java.util.Date();
String string = stringDateFormat.format(date);

### 2.2 方法二
Date = (new SimpleDateFormat("格式").parse(String);

|字母 |日期或时间元素 |表示 |实例
-       |-                         |-       |-
G     |Era标识符          |Text  |AD

参考：
[java string类型怎么转化成date类型 精选](https://zhidao.baidu.com/question/534635378.html)

[GMT时间格式转换(Mon Feb 13 08:00:00 GMT+08:00 2012)](https://www.2cto.com/kf/201304/205998.html)

[Java时间处理(UTC时间和本地时间转换)](https://blog.csdn.net/u013412772/article/details/73610803)