---
title: Spring注解
date: 2019-08-22 23:51:07
categories:
- 未分类
tags:
---

### spring不返回某个字段给前端
1.@JsonIngoreProperties(value={"xxx})
注解使用在类名，接口头上
```java
@JsonIngoreProperties(value={"username})
public interface User(){
}
```

2.@JsonIgnore
该注解用在字段的get方法上
```java
@JsonIngore
public String getUserName{
    return this.username;
}
```

### json与bean之间字段转换
@JsonProperty
json里的字段采用下划线格式，bean里的字段采用驼峰
```java
@JsonProperty("user_name")
private String userName;
```
