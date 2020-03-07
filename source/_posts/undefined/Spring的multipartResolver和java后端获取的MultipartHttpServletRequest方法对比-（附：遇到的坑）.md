---
title: 【转】spring的multipartResolver和java后端获取的MultipartHttpServletRequest方法对比
date: 2019-08-14 23:44:19
categories:
- 未分类
tags:
---

## 1. 问题描述
这两天在用spring进行上传上遇到问题，今天进行了问题的排查，这个过程也增加了我看spring源码的兴趣！还是很有收获的！

首先先给A组提供了上传接口，并没有在spring的配置文件进行multipartResolver的配置，后台Controller的java的获取为：

```java
MultipartResolver resolver = new CommonsMultipartResolver(request.getSession().getServletContext());
MultipartHttpServletRequest multipartRequest = resolver.resolveMultipart(request);
MultipartFile file = multipartRequest.getFile("file");
String a1 = multipartRequest.getParameter("a1");
```

将request请求的上下文转换为MultipartResolver，然后转换为MultipartHttpServletRequest请求，通过multi请求就可以获取对应的file文件信息，这样的方法没有问题，后台能获取到相应的参数；

稍后组里另一个同事也用到上传，基于网上查的资料，认为应该把配置文件给加上去了，于是问题出来了，也造成了我记录该博客的原因：
```xml
<bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver"> 
    <!-- set the max upload size100MB --> 
   <property name="maxUploadSize"> 
   <value>10485760000</value> 
   </property> 
   <property name="maxInMemorySize"> 
   <value>4096</value> 
   </property> 
</bean>
```

进行该配置后，我在后台的该方法获取file为空造成了上传错误，刚开始我也纳闷，按道理spring的上传应该需要这样的配置，同事也是觉得我大意了没有进行配置好心给我进行了修改，但是反而造成了bug，线下环境测试将该配置去除功能ok。好了，做了这么多年开发，不能因为fixed bug而不进行原理的问题查看，这样的修改连自己都过不去。

## 2. 解决方案

搜了资料，做了测试，有了以下的总结，有问题欢迎大家拍砖！

最后上传问题的方案：

### 2.1 解决方法1:
1、在spring-config配置了<bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver"> 之后

后台的获取有两种方法：

1、指定@RequestParam MultipartFile file 例如：public Map<String, Object> logsUpload(@RequestParam MultipartFile file,@RequestParam(value="key") String key)参数；

2、将request转化为MultipartHttpServletRequest multiRequest = (MultipartHttpServletRequest)(request);

原理是：使用spring的CommosMultipartResolver 配置MultipartResolver 用于文件上传，DispatcherServlet 将调用 MultipartResolver 的 isMultipart(request) 方法检查当前 Web 请求是否为 multipart类型。如果是，DispatcherServlet 将调用 MultipartResolver 的 resolveMultipart(request) 方法，对原始 request 进行装饰，并返回一个 MultipartHttpServletRequest 供后继处理流程使用(最初的 HttpServletRequest 被偷梁换柱成了 MultipartHttpServletRequest)，否则，直接返回最初的 HttpServletRequest。也就是说请求一旦被 MultipartResolver 接手，它就会解析请求中的文件，而不必等待后续 controller 主动从 MultipartRequest 中 getFile，所以在配置了MultipartResolver后，再通过这样的方法

MultipartResolver resolver = new CommonsMultipartResolver(request.getSession().getServletContext());
MultipartHttpServletRequest multipartRequest = resolver.resolveMultipart(request);

是获取不到file的，因为控制器已经帮我们进行了转换 直接获取即可。

如果你使用该方法发觉获取没有问题，你可以看看给这个方法是不是配置了servlet，如果配置了servlet是不走这个 MultipartResolver控制，是能获取成功的。

### 2.2 解决方法2
无需进行spring-config的配置，直接在后台获取进行转换即可，也就是我最初的写法。

MultipartResolver resolver = new CommonsMultipartResolver(request.getSession().getServletContext());
MultipartHttpServletRequest multipartRequest = resolver.resolveMultipart(request);
MultipartFile file = multipartRequest.getFile("file");
String key = multipartRequest.getParameter("key");

 

然后项目具体需要什么样的修改，自己结合业务斟酌即可。

自己讲的不深，看了很多文章资料，也是这篇给的启迪最大：
http://exceptioneye.iteye.com/blog/1314958

转载：https://www.cnblogs.com/yskcoder/p/4718198.html