---
title: MyBatis中${}和#{}的区别
date: 2019-08-30 00:07:09
tags:
- MyBatis
categories: 
- ORM框架
keywors: 
description: 
top_img: 
cover: /img/mybatis.jpg
---
## 1. 知识点
MyBatis中使用parameterType向SQL语句传参，parameterType后的类型可以是基本类型int,String,HashMap和java自定义类型。
在SQL中引用这些参数的时候，可以使用两种方式#{parameterName}或者${parameterName}。

## 2. 总结
1. \#将传入的数据都当成一个字符串，会对自动传入的数据加一个双引号。
例如：where username=#{username}，如果传入的值是zhangsan,那么解析成sql时的值为where username="zhangsan"。

2. \$ 将传入的数据直接显示生成在sql中。
例如：where username=\${username}，如果传入的值是zhangsan,那么解析成sql时的值为where username=zhangsan；

3. \#方式能够很大程度防止SQL注入，\$方式无法防止SQL注入。
如果传入的值为;drop table user;
\#{}方式的SQL语句为: select id, username, password from user where username = ";drop table user;"
${}方式的SQL语句为: select id, username, password from user where username = ;drop table user;

4. \$ 方式一般用于传入数据库对象，例如传入表名和列名。
例外：排序时使用order by动态参数时需要使用\$  ,ORDER BY ${columnName}

5. 一般能用#的就别用\$ ，若不得不使用"${xxx}"这样的参数，要手工地做好过滤工作，来防止sql注入攻击。

6. 在MyBatis中，"\$ {xxx}"这样格式的参数会直接参与SQL编译，从而不能避免注入攻击。但涉及到动态表名和列名时，只能使用"${xxx}"这样的参数格式。所以，这样的参数需要我们在代码中手工进行处理来防止注入。