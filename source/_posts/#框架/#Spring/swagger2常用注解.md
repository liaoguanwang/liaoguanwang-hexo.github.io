---
title: Spring-Swagger2常用注解
date: 2019-08-24 23:48:28
categories:
- Spring
tags:
- Swagger
- API接口
keywors: 
description: 
top_img: 
cover: /img/swagger.jpg
---

## 常用注解：
- @Api()用于类；
表示标识这个类是swagger的资源

- @ApiOperation()用于方法；
表示一个http请求的操作

- @ApiParam()用于方法，参数，字段说明；
表示对参数的添加元数据（说明或是否必填等）

- @ApiResponse()用于方法
响应配置

- @ApiResponses()用于方法
响应集配置

- @ResponseHeader()用于方法
响应头设置，使用方法

- @ApiModel()用于类；
表示对类进行说明，用于参数用实体类接收

- @ApiModelProperty()用于方法，字段;
表示对model属性的说明或者数据操作更改

- @ApiIgnore()用于类，方法，方法参数 
表示这个方法或者类被忽略 

## 1. @Api()
用于类，表示标识这个类是swagger文档资源 ，使用方式：
```
@Api(value = "/url", description = "对api资源的描述")
```
与Controller注解并列使用。 

属性配置：

 属性名称     | 备注 
- | -
value           | url的路径值 |
tags             | 如果设置这个值，value的值会被覆盖 
description  | 对api资源的描述
basePath    | 基本路径，可以不配置 
position       | 如果配置多个Api，想改变显示的顺序位置 
produces     | For example，"application/json"，"application/xml"
consumes   | For example, "application/json, application/xml" 
protocols     | Possible values: http, https, ws, wss
hidden         | 配置为true 将在文档中隐藏
authorizations     | 高级特性认证时配置

## 2.@ApiOperation()
用于方法上，说明方法的作用，每一个url资源的定义，使用方式：
```java
@ApiOperation(
    value = "url的路径值",
    notes = "",
    response = order,
    tags = {'Pet Store'}
)
```
与Controller中的方法并列使用

属性配置：

属性名称 | 备注
- | -
value                 | url的路径值
tags                   | 如果设置这个值、value的值会被覆盖
description        | 对api资源的描述
basePath          | 基本路径可以不配置
position             | 如果配置多个Api 想改变显示的顺序位置
produces           | For example, "application/json, application/xml"
consumes         | For example, "application/json, application/xml"
protocols           | Possible values: http, https, ws, wss.
authorizations   | 高级特性认证时配置
hidden               | 配置为true 将在文档中隐藏
response           | 返回的对象
code                  | http的状态码 默认 200
extensions         | 扩展属性
responseContainer    | 这些对象是有效的 "List", "Set" or "Map".，其他无效
httpMethod                | "GET", "HEAD", "POST", "PUT", "DELETE", "OPTIONS" and "PATCH"

## 3. @ApiParam()
用于方法，参数，字段说明，表示对参数的添加元数据（说明或是否必填等)。
使用方式：
```java
public Result login(@RequestBody @ApiParam( value = "属性值", required = true) User user)

```
与Controller中的方法使用：

 属性名称 | 备注
- | -
name                  | 属性名称
value                  | 属性值
defaultValue       | 默认属性值
allowableValues | 可以不配置
required              | 是否属性必填
access                | 不过多描述
allowMultiple       | 默认为false
hidden                 | 隐藏该属性
example              | 举例子
 
## 4. @ApiResponses()
响应集配置，使用方式：
```java
@ApiResponses({ @ApiResponse(code = 400, message = "Invalid Order") })
```

与Controller中的方法并列使用。属性配置：

 属性名称 | 备注
- | -
value    | 多个ApiResponse配置


## 5. @ApiResponse()
响应配置，使用方式：
```java
@ApiResponse(code=400, message = "Invalid user supplied")
```
与Controller中的方法并列使用。属性配置：

属性名称 | 备注
- | -
code                           | http的状态码
message                    | 描述
response                    | 默认响应类 Void
reference                   | 参考ApiOperation中配置
responseHeaders      | 参考 ResponseHeader 属性配置说明
responseContainer    | 参考ApiOperation中配置

## 6. @ResponseHeader()
响应头设置，使用方法
```java
@ResponseHeader(name = "响应头名称", description = "头描述")
```

 属性名称 | 备注
- | -
name             | 响应头名称
description     | 头描述
response        | 默认响应类 Void
responseContainer | 参考ApiOperation中配置

## 7. @ApiModel()用于类
表示对类进行说明，用于参数用实体类接收 

## 8. @ApiModelProperty()用于方法，字段
表示对model属性的说明或者数据操作更改 

## 9.@ApiImplicitParams()
用于方法，包含一组参数说明，表示单独的请求参数 

## 10.@ApiImplicitParam()
用在@ApiImplicitParams注解中，指定一个请求参数的各个方面。即多个 @ApiImplicitParam

参考：
[SpringBoot集成springfox-swagger2构建restful API](https://blog.csdn.net/u014231523/article/details/54562695)

[SpringMVC集成springfox-swagger2构建restful API](https://blog.csdn.net/u014231523/article/details/54411026)

[swagger2常用注解说明](https://blog.csdn.net/u014231523/article/details/76522486)

[swagger常用注解说明](https://www.jianshu.com/p/12f4394462d5)