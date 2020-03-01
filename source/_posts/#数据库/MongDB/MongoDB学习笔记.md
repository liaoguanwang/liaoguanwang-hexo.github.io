---
title: MongoDB学习笔记
date: 2019-09-16 23:51:14
tags:
- MongoDB
categories: 
- 数据库
---
## MongoDB简介

---
MongoDB是一个基于分布式文件存储的数据库。由C++语言编写。旨在为WEB应用提供可扩展的高性能数据存储解决方案。

MongoDB是一个介于关系数据库和非关系数据库之间的产品，是非关系数据库当中功能最丰富，最像关系数据库的。它支持的数据结构非常松散，是类似json的bson格式，因此可以存储比较复杂的数据类型。Mongo最大的特点是它支持的查询语言非常强大，其语法有点类似于面向对象的查询语言，几乎可以实现类似关系数据库单表查询的绝大部分功能，而且还支持对数据建立索引。

## MongoD安装与设置

---
### 1.MongoDB安装
官网下载：[https://www.mongodb.com/download-center/enterprise](https://www.mongodb.com/download-center/enterprise)

<img src="https://img2018.cnblogs.com/blog/1351916/201905/1351916-20190515000444100-1593660947.png"  height="375" width="713">

### 2.设置环境变量
<img src="https://img2018.cnblogs.com/blog/1351916/201905/1351916-20190515005313068-1730917808.png"  height="390" width="413">

### 3.启动配置
1. Windows下打开dos窗口，输入"mongod"，发现启动失败，报错:exception in initAndListen: NonExistentPath: Data directory C:\data\db\ not found., terminating。意思是没有在指定位置找到用来存放数据的目录。
<img src="https://img2018.cnblogs.com/blog/1351916/201905/1351916-20190515234223262-616990280.png"  height="1500" width="643">
解决方法：建立C:\data\db目录
--- 分割线 ---
参考：[windows下安装MongoDB](https://www.jianshu.com/p/7f3cb2c7806b)


2. 指定MongoDB数据库的目录 
参考：[MongoDB：更改数据库位置（Windows）](https://www.cnblogs.com/ilovewindy/p/3736072.html) 
--- 分割线 ---
如果不想数据库目录存在"C:\data\db"，也可以存放在其他地方，这里我的MongoDB的数据库安装在"D:\Program Files\mongodb-4.0.9\data"
首先dos窗口先切换到MongoDB安装的bin目录下
![](https://img2018.cnblogs.com/blog/1351916/201905/1351916-20190515235048086-89673912.png)
然后输入mongodb.exe --dbpath "D:\Program Files\mongodb-4.0.9\data" 
![](https://img2018.cnblogs.com/blog/1351916/201905/1351916-20190515235124201-2018153789.png)

3. 通过配置文件指定MongoDB数据库的目录
<img src="https://img2018.cnblogs.com/blog/1351916/201905/1351916-20190516001430489-1153028170.png"  height="1100" width="600">
在dos窗口输入命令mongod --config "D:\Program Files\mongodb-4.0.9\mongod.cfg" --install

4. 启动MongoDB服务端
命令：mongod
注意：启动端口默认为27017，建议更改端口和添加用户密码。
<img src="https://img2018.cnblogs.com/blog/1351916/201905/1351916-20190522234747328-1957429489.png"  height="270" width="1070">

5. 连接MongoDB
命令：mongo
<img src="https://img2018.cnblogs.com/blog/1351916/201905/1351916-20190523005225528-1762146626.png"  height="290" width="930">

## MongoDB命令

---
### 1.MongoDB启动
- 命令行（终端启动）启动命令
    - mongod:启动服务端
    - mongo:启动客户端

### 2.操作数据库命令
- 查看当前的数据：
db | db.getName()

- 查看所有的数据库： 
show dbs | show databases

- 切换数据库： 
use db_name

- 删除当前数据库：
db.dropDatabases()，如果没有选择任何数据库，那么它会删除默认的"test"数据库

- 显示数据库状态：
db.stats()

- 显示数据库版本：
db.version()

- 查看当前数据库的链接机器地址：
db.getMongo()

- 删除数据库：
db.dropDatabase()

### 3.操作集合的命令
- 查看集合：
show collections

- 删除集合：
db.collection_name.drop() 

### 4.操作用户命令
- 查看当前库下的账户：
show users 

- 查看全局所有账户：
db.system.users.find().pretty()

- 添加用户：

1.添加超级用户
切换到admin数据，use admin
执行添加语句：
```java
db.createUser(
    {
        user:"username",
        pwd:"password",
        roles:[
            {role:"root", db:"admin"}
        ]
    }
)
```
2.添加普通用户
```java
db.createUser(
    {
        user:"username",
        pwd:"password",
        roles:[
            {role:"read", db:"xxxx"},
            {role:"readWrite", db:"test"}
        ]
    }
)
```

```java
格式1
  db.createUser(
{
  user:"test",
  pwd:"123456",
  roles:[{role:"readWrite", db:"test"}]
})

格式2
db.createUser({user: "test", pwd: "123456", roles: [{ role: "readWrite", db: "test" }]})   
```

- 修改用户密码：
1.db.changeUserPassword("user_name","new_password");
2.db.updateUser("user_name",{pwd:"new_password"})；

- 修改用户权限
1.完全替换之前权限
db.updateUser("user_name",{roles:[ {role:"read",db:"testDB"} ]})
注：updateuser它是完全替换之前的值，如果要新增或添加roles而不是代替它，则使用方法： db.grantRolesToUser() 和 db.revokeRolesFromUser(）
---分割线---
2.添加权限
db.grantRolesToUser("user_name", [{role:"readWrite", db:"testDB"},{role:"read", db:"testDB"}])
---分割线---
3.删除权限
db.revokeRolesFromUser("user_name",[{role:"read", db:"testDB"}])

- 用户认证
切换到数据库
use db_name
认证
db.auth("user_name","password")

- 删除用户：
切换到用户授权的db
use db_name
执行删除操作    
db.removeUser("user_name") | db.dropUser("user_name")
---分割线---
参考：
[MongoDB给数据库创建用户](https://www.cnblogs.com/jacksoft/p/6916137.html)
[MongoDB 创建用户 修改权限 修改密码 删除用户](https://jingyan.baidu.com/article/d169e18609d989436611d82e.html)


### 5.数据库角色
- 数据库访问
  - read：允许读取指定数据库的角色
  - readWrite ：允许读写指定数据库的角色
- 数据库管理
  - dbAdmin：允许用户在指定数据库中执行管理函数，如索引创建、删除，查看统计或访问system.profile
  - userAdmin：允许管理当前数据库的用户，如创建用户、为用户授权
  - dbOwner：数据库拥有者(最高)，集合了dbAdmin/userAdmin/readWrite角色权限
- 集群管理
  - clusterAdmin：集群最高管理员，集合clusterManager/clusterMonitor/hostManager角色权限
  - clusterManager：集群管理角色，允许对分片和副本集集群执行管理操作，如addShard，resync等
  - clusterMonitor：集群监控角色，允许对分片和副本集集群进行监控，如查看serverStatus
  - hostManager： 节点管理角色，允许监控和管理节点，比如killOp、shutdown操作
- 备份恢复
  - backup：备份权限，允许执行mongodump操作
  - restore：恢复权限，允许执行mongoresotre操作
- 数据库通用角色
  - readAnyDatabase：允许读取所有数据库
  - readWriteAnyDatabase：允许读写所有数据库
  - userAdminAnyDatabase：允许管理所有数据库的用户
  - dbAdminAnyDatabase：允许管理所有数据库
- 特殊角色
  - root：超级管理员，拥有所有权限
  - __system：内部角色，用于集群间节点通讯

参考：
[mongodb 认证鉴权那点事](https://www.cnblogs.com/littleatp/p/7702539.html)
[mongodb用户权限管理配置](https://blog.csdn.net/u013066244/article/details/53874216)
[浅尝辄止MongoDB：管理（1）](https://blog.csdn.net/wzy0623/article/details/82999747)

---未完待续---