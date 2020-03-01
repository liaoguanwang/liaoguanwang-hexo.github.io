---
title: Druid连接池
date: 2019-08-14 23:41:41
tags:
---

## 1 简介
### 1.1 什么是连接池
数据库连接池负责分配、管理和释放数据库连接，它允许应用程序重复使用一个现有的数据库连接，而不是再重新建立一个。释放空闲时间超过最大空闲时间的数据库连接来避免因为没有释放数据库连接而引起的数据库连接遗漏。这项技术能明显提高对数据库操作的性能。

### 1.2 背景
- Druid是阿里巴巴开发的号称为监控而生的数据库连接池，Druid是目前最好的数据库连接池。
- Druid是目前Java社区使用广泛的的数据库连接池，在功能、性能、扩展性方面，都超过其他数据库连接池，包括DBCP、C3P0、BoneCP、Proxool、JBoss DataSource。Druid已经在阿里巴巴部署了超过600个应用，经过许多生产环境大规模部署的严苛考验。
- Druid加入日志监控，能够提供强大的监控和扩展功能，自带监控页面，实时监控应用的连接池情况

## 2 基本使用
### 2.1 获取
1.源码
Druid是一个开源项目，源码托管在github上：https://github.com/alibaba/druid

2.依赖    
```xml
<!-- https://mvnrepository.com/artifact/com.alibaba/druid -->
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.1.8</version>
</dependency>
```

### 2.2 替换
![](https://images2018.cnblogs.com/blog/1351916/201806/1351916-20180622011730996-1147810321.png)

## 3 扩展功能
### 3.1 性能监控
#### 3.1.1 开启配置
1.web.xml里增加如下配置
```xml
<!--  配置druid监控-->
<filter>
  <filter-name>DruidWebStatFilter</filter-name>
  <filter-class>com.alibaba.druid.support.http.WebStatFilter</filter-class>
  <init-param>
    <param-name>exclusions</param-name>
    <param-value>*.js,*.gif,*.jpg,*.png,*.css,*.ico,/druid/*</param-value>
  </init-param>
</filter>
<filter-mapping>
  <filter-name>DruidWebStatFilter</filter-name>
  <url-pattern>/*</url-pattern>
</filter-mapping>
```

2.增加html页面查看
```xml
<servlet>
  <servlet-name>DruidStatView</servlet-name>
  <servlet-class>com.alibaba.druid.support.http.StatViewServlet</servlet-class>
</servlet>
<servlet-mapping>
  <servlet-name>DruidStatView</servlet-name>
  <url-pattern>/druid/*</url-pattern>
</servlet-mapping>
```

3.Spring的连接池里增加如下配置
![](https://images2018.cnblogs.com/blog/1351916/201806/1351916-20180622011917066-1078747646.png)

#### 3.1.2 监控界面
- http://localhost/项目名/druid/index.html
![](https://images2018.cnblogs.com/blog/1351916/201806/1351916-20180622011945925-1623046085.png)

- 测试登录
从这个页面里我们能看到网站从启动开始执行的sql语句统计，每一条语句的执行次数、执行时间之和、最慢执行时间、执行错误数等等多个统计指标，通过这个统计数据我们可以找出sql语句的执行规律及sql语句的不足之处，也可以通过错误数来定位程序的不足并及时修改。
![](https://images2018.cnblogs.com/blog/1351916/201806/1351916-20180622012023045-1958011764.png)
![](https://images2018.cnblogs.com/blog/1351916/201806/1351916-20180622012028740-672619899.png)

### 3.2 防注入
#### 3.2.1 注入
- Mybatis可能存在注入攻击。
![](https://images2018.cnblogs.com/blog/1351916/201806/1351916-20180622012116102-1564543970.png)
参考：https://www.cnblogs.com/friends-wf/p/4227999.html

#### 3.2.2 防注入
- 注入演示:用户名和密码输入："1 or 1=1"
![](https://images2018.cnblogs.com/blog/1351916/201806/1351916-20180622012155931-1409272446.png)

- 报错：SQL注入异常
![](https://images2018.cnblogs.com/blog/1351916/201806/1351916-20180622012200881-2052651951.png)
![](https://images2018.cnblogs.com/blog/1351916/201806/1351916-20180622012206556-1875924082.png)

### 3.3 参考资料
官方: https://github.com/alibaba/druid/wiki/FAQ