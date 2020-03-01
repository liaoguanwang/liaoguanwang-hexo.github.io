---
title: SpringJPA主键生成采用自定义ID，自定义ID采用年月日时间格式
date: 2019-09-10 23:45:25
tags:
categories: 
- Java
---

### 自定义主键生成策略


1. 在entity类上添加注解
```java
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO, generator = "custom-id")     @GenericGenerator(name = "custom-id", strategy = "com.h5page-mapdiy.utils.CustomIDGenerator")
    @Column(name = "id")
    public Long getId() {
        return id;
    }

```
注意：GeneratedValue中的generator要与GenericGenerator中的name相等 上面代码中是"custom-id"

2. 在utils包下添加自定义ID生成类
```java
package com.h5page-mapdiy.utils

import org.hibernate.MappingException;
import org.hibernate.engine.spi.SharedSessionContractImplementor;
import org.hibernate.id.IdentityGenerator;
import java.io.Serializable;

/**
 * 自定义ID生成器
 * @author liaoguanwang
 * @date 2019-03-16 10:33
 */
public class CustomIDGenerator extends UUIDGenerator {
    @Override
    public Serializable generate(SharedSessionContractImplementor session, Object object) throws MappingException {

        SimpleDateFormat simpleDateFormat;  
        simpleDateFormat = new SimpleDateFormat("yyyyMMddHHmmss");  
        Date date = new Date();  
        String str = simpleDateFormat.format(date);  
        Random random = new Random();  
        int rannum = (int) (random.nextDouble() * (99999 - 10000 + 1)) + 10000;// 获取5位随机数  
        return rannum + str;// 当前时间  
    }
}
```

注意：
1. 测试的ID是Long类型所以这里继承的是IdentityGenerator类
2. 如果ID为String类型的话应该继承 UUIDGenerator 或者 UUIDGenerator

参考：
[Spring boot JPA 用自定义主键策略 生成自定义主键ID](https://www.cnblogs.com/DevMuYuer/p/10088425.html)

[AVA随机生成文件名：当前年月日时分秒+五位随机数](https://sxdtzhaoxinguo.iteye.com/blog/2274635)