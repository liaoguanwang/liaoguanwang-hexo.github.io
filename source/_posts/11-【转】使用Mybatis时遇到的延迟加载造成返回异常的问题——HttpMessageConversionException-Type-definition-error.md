---
title: >-
  【转】使用Mybatis时遇到的延迟加载造成返回异常的问题——HttpMessageConversionException: Type definition
  error
date: 2019-08-18 23:27:44
tags:
---

在使用Mybatis的过程中，使用了resultMap延迟加载。

延迟加载：association联表查询的过程中，查询另外两个表的对象。而延迟加载是指只有在使用这两个对象的时候才会进行查询。

问题的产生，在我这里，我是直接通过路由url直接查询 StudentDto_2 对象。延迟加载的对象为 TClass 和 Assistant.

```java
public class StudentDto_2 {
    private Integer stuId;
    private String stuNum;  //学生编号,unique
    private String studentName;
    private Long birthDate;
    private TClass tClass;
    private Assistant assistant;
}
```

在序列化的过程中，会抛出以下异常。
```java
2018-08-27 11:14:29.339 ERROR 34755 --- [nio-8089-exec-1] o.a.c.c.C.[.[.[/].[dispatcherServlet]    : Servlet.service() for servlet [dispatcherServlet] in context with path [] threw exception [Request processing failed; nested exception is org.springframework.http.converter.HttpMessageConversionException: Type definition error: [simple type, class org.apache.ibatis.executor.loader.javassist.JavassistProxyFactory$EnhancedResultObjectProxyImpl]; nested exception is com.fasterxml.jackson.databind.exc.InvalidDefinitionException: No serializer found for class org.apache.ibatis.executor.loader.javassist.JavassistProxyFactory$EnhancedResultObjectProxyImpl and no properties discovered to create BeanSerializer (to avoid exception, disable SerializationFeature.FAIL_ON_EMPTY_BEANS) (through reference chain: com.zimo.mybaties.util.Result["data"]->com.zimo.mybaties.dto.StudentDto_2_$$_jvstfe4_0["handler"])] with root cause
```
问题：Springmvc+mybatis,mybatis配置延迟加载时，json序列化异常

我目前的解决办法是关闭延迟加载，关闭后一切正常。

寻找另外一种方法

参考链接：https://blog.csdn.net/justinytsoft/article/details/53575236

在SpringBoot中，默认使用Jackson来实现java对象到json格式的序列化与反序列化。Jackson的转化是通过ObjectMapper来实现的。SpringBoot内置了一个ObjectMapper。我们通过下面的配置来

实现序列化Mybatis的延迟加载。
```java
package com.zimo.mybaties.config;

import com.fasterxml.jackson.annotation.JsonInclude;
import com.fasterxml.jackson.databind.ObjectMapper;
import com.fasterxml.jackson.databind.SerializationFeature;
import org.springframework.context.annotation.Configuration;

@Configuration
public class CustomerMapper extends ObjectMapper {
    public CustomerMapper() {
        this.setSerializationInclusion(JsonInclude.Include.NON_NULL);　　//返回为null的值则去除，
        this.configure(SerializationFeature.FAIL_ON_EMPTY_BEANS,false);　　//解决延迟加载的对象
    }
}
```

返回参数中了多了一个代理对象handle
```json
{
"code": 200,
"message": "获取学生信息",
-"data": {
    "stuId": 22,
    "stuNum": "12310",
    "studentName": "天涯10",
    "birthDate": 777524356000,
    -"tClass": {
        "classId": 3,
        "classNum": "1921083145",
       "className": "数学"
        },
    -"assistant": {
       "assistantId": 1,
        "assistantNum": "1315151521",
        "name": "还在于是"
        },
    "handler": { }
    }
}
```
转自：https://www.cnblogs.com/wbq1113/p/9541114.html