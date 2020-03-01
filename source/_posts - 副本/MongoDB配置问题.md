---
title: MongoDB配置问题
date: 2019-09-10 23:46:29
tags:
---

### 1. mongodb启动配置
参考：[Windows下mongodb的启动](https://www.jianshu.com/p/6f4549037cc1)


### 2. Invalid command: files\mongoDB
报错信息：
![](https://img2018.cnblogs.com/blog/1351916/201905/1351916-20190506235357505-310315469.png)

正确方法：
![](https://img2018.cnblogs.com/blog/1351916/201905/1351916-20190506235618294-1343038263.png)

原因：路径之中有空格会当参数来处理，需要将整个路径加上引号

### 3. MongoDB连接认证失败
SpringBoot配置MongoDB地址时，如果不设置连接方式默认是MONGODB-CR,如果安装的时候设置的是SCRAM-SHA-1。

需要在配置mongodb地址的时候加上连接方式：
spring.data.mongodb.uri=mongodb://root(userName):root(password)@localhost(ip地址):27017(端口号)/gis(collections/数据库)?authSource=admin&authMechanism=SCRAM-SHA-1

### 4. Unauthorized: not authorized on test to execute command { dbstats: 1.0, scale: undefined, $db: "test" } 
当前版本3.6.2
问题原因出在读写的权限上：stackoverflow上的解决方法

You should have started the mongod instance with access control, i.e., the --auth command line option, such as:
```
$ mongod --auth
```

Let's start the mongo shell, and create an administrator in the admin database:
```
$ mongo
> use admin
> db.createUser(
  {
    user: "myUserAdmin",
    pwd: "abc123",
    roles: [ { role: "userAdminAnyDatabase", db: "admin" } ]
  }
)
```

Now if you run command "db.stats()", or "show users", you will get error "not authorized on admin to execute command..."
```
> db.stats()
{
        "ok" : 0,
        "errmsg" : "not authorized on admin to execute command { dbstats: 1.0, scale: undefined }",
        "code" : 13,
        "codeName" : "Unauthorized"
}
```
下面这两句最重要，如果是开始新建 ，则要从头开始看:
The reason is that you still have not granted role "read" or "readWrite" to user myUserAdmin. You can do it as below:
```
> db.auth("myUserAdmin", "abc123")
> db.grantRolesToUser("myUserAdmin", [ { role: "read", db: "admin" } ])
```

Now You can verify it (Command "show users" now works):
```
> show users
{
        "_id" : "admin.myUserAdmin",
        "user" : "myUserAdmin",
        "db" : "admin",
        "roles" : [
                {
                        "role" : "read",
                        "db" : "admin"
                },
                {
                        "role" : "userAdminAnyDatabase",
                        "db" : "admin"
                }
        ]
}

Now if you run "db.stats()", you'll also be OK:
```
> db.stats()
{
        "db" : "admin",
        "collections" : 2,
        "views" : 0,
        "objects" : 3,
        "avgObjSize" : 151,
        "dataSize" : 453,
        "storageSize" : 65536,
        "numExtents" : 0,
        "indexes" : 3,
        "indexSize" : 81920,
        "ok" : 1
}
```

This user and role mechanism can be applied to any other databases in MongoDB as well, in addition to the admin database.
```
参考：[Unauthorized: not authorized on test to execute command { dbstats: 1.0, scale: undefined, $db: "test" } 错误的解决](https://www.cnblogs.com/herosoft/p/8399974.html)