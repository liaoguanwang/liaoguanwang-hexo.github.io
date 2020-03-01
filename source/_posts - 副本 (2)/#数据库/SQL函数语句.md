---
title: SQL函数语句
date: 2019-08-25 23:22:06
tags:
categories: 
- 数据库
---
### 1.MyBatis实现模糊查询

1.${…}代替#{…}

2.把’%#{name}%’改为”%”#{name}”%”

3.使用sql中的字符串拼接函数

4.使用标签bind


```xml
<select id="selectPersons" resultType-"person" parameterType="person">
    <bind name="pattern" value=" '%' + _parameter.username + '%' "/>
    select id, sex, age, username, password from person where username LIKE #{pattern}
</select>
```
参考：
[MyBatis实现模糊查询的几种方式](https://blog.csdn.net/lonely_dog/article/details/74171314)
[SQL Count（*）函数，GROUP_By,Having的联合使用](https://www.cnblogs.com/gongchengshiwhl/p/7994761.html)


### 2. group_concat函数
用法：group_concat( [distinct] 要连接的字段 [order by 排序字段 asc/desc ] [separator ‘分隔符’] )

#### 2.1 group_concat长度限制
mysql中group_concat函数对此函数的处理结果字符串长度是有限制的，默认为1024；所以如果结果字符串太长的话，就会在数据库里被截取，因此没有显示全部数据。

解决方法：
1、SET GLOBAL group_concat_max_len = 102400;  //其中数字大小可修改
注意：该方法缺点是重启服务后设置失效

2、在MySQL配置文件(my.ini)中加： 
group_concat_max_len = -1 # -1为最大值或填入你要的最大长度 
并重启mysql