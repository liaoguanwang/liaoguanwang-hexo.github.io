---
title: >-
  【BUG】Spring Mvc使用Jackson进行json转对象时，遇到的字符串转日期的异常处理（JSON parse error: Can not
  deserialize value of type java.util.Date from String[]）
date: 2019-08-21 23:23:04
tags:
- SpringMVC
categories: 
- BUG
keywors: 
description: 
top_img: 
cover: /img/bug.jpg
---

### 1.问题排查
**项目配置**
- springboot 2.1
- Mave配置Jackson依赖

**出现的场景**
- 服务端通过springmvc写了一个对外的接口，查询数据中的表，表中有一个字段属性是时间戳，返回一个json字符串，其中该json带有日期，格式为yyyy-MM-dd HH:mm:ss
- 客户端调用该http接口，指定返回值为一个Vo，Vo中日期的字段为Date类型
- 客户端调用该接口后抛异常了。报错信息如下：
```java
feign.codec.DecodeException: JSON parse error: Can not deserialize value of type java.util.Date from String "2018-03-07 16:18:35": not a valid representation (error: Failed to parse Date value '2018-03-07 16:18:35': Can not parse date "2018-03-07 16:18:35Z": while it seems to fit format 'yyyy-MM-dd'T'HH:mm:ss.SSS'Z'', parsing fails (leniency? null)); nested exception is com.fasterxml.jackson.databind.exc.InvalidFormatException: Can not deserialize value of type java.util.Date from String "2018-03-07 16:18:35": not a valid representation (error: Failed to parse Date value '2018-03-07 16:18:35': Can not parse date "2018-03-07 16:18:35Z": while it seems to fit format 'yyyy-MM-dd'T'HH:mm:ss.SSS'Z'', parsing fails (leniency? null))
```

### 2.解决方法
因为数据库中存在一个create_time字段，属性是timestamp，服务器端查询数据库并把字段映射到定义的Vo中出现了格式问题，所以在Vo中把createTime添加@JsonFormat注释就可以完成时间字段映射的格式问题。
```
@JsonFormat( pattern="yyyy-MM-dd HH:mm:ss")  
  private Date createTime;  
```

### 3.异常的原因
这篇博客可以了解底层报错的原因。
[https://blog.csdn.net/qq906627950/article/details/79503801](https://blog.csdn.net/qq906627950/article/details/79503801)