---
title: SpringBoot实现跨域
date: 2019-08-24 23:49:46
tags:
- 跨域
categories: 
- SpringBoot
keywors: 
description: 
top_img: 
cover: /img/springboot.jpg
---

### 1. 什么是跨域请求？
跨域请求，就是说浏览器在执行脚本文件的ajax请求时，脚本文件所在的服务地址和请求的服务地址不一样。说白了就是ip、网络协议、端口都一样的时候，就是同一个域，否则就是跨域。这是由于Netscape提出一个著名的安全策略——同源策略造成的，这是浏览器对JavaScript施加的安全限制。是防止外网的脚本恶意攻击服务器的一种措施。

### 2. SpringBoot实现跨域的方法

#### 2.1 @Configuration配置跨域
```
/**
 * 设置跨域请求
 */
@Configuration
public class CorsConfig {
    private CorsConfiguration buildConfig() {
        CorsConfiguration corsConfiguration = new CorsConfiguration();
        corsConfiguration.addAllowedOrigin("*"); // 1 设置访问源地址
        corsConfiguration.addAllowedHeader("*"); // 2 设置访问源请求头
        corsConfiguration.addAllowedMethod("*"); // 3 设置访问源请求方法
        return corsConfiguration;
    }

    @Bean
    public CorsFilter corsFilter() {
        UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
        source.registerCorsConfiguration("/**", buildConfig()); // 4 对接口配置跨域设置
        return new CorsFilter(source);
    }
}
```

“*”代表全部。”**”代表适配所有接口。 
其中addAllowedOrigin(String origin)方法是追加访问源地址。如果不使用”*”（即允许全部访问源），则可以配置多条访问源来做控制。 
例如：
```
corsConfiguration.addAllowedOrigin("http://www.aimaonline.cn/"); 
corsConfiguration.addAllowedOrigin("http://test.aimaonline.cn/"); 
```



#### 2.2 启动类配置跨域

```
@SpringBootApplication
@ComponentScan
@EnableDiscoveryClient
public class ManagementApplication {

    public static void main(String[] args) {
        SpringApplication.run(ManagementApplication.class, args);
    }

    private CorsConfiguration buildConfig() {
        CorsConfiguration corsConfiguration = new CorsConfiguration();
        corsConfiguration.addAllowedOrigin("*");
        corsConfiguration.addAllowedHeader("*");
        corsConfiguration.addAllowedMethod("*");
        corsConfiguration.addExposedHeader(HttpHeaderConStant.X_TOTAL_COUNT);
        return corsConfiguration;
    }

    /**
     * 跨域过滤器
     *
     * @return
     */
    @Bean
    public CorsFilter corsFilter() {
        UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
        source.registerCorsConfiguration("/**", buildConfig()); // 4
        return new CorsFilter(source);
    }
}
```

#### 2.3 编写Filter过滤器

```
package com.cci.market.common.filter;
import java.io.IOException;
import javax.servlet.Filter;
import javax.servlet.FilterChain;
import javax.servlet.FilterConfig;
import javax.servlet.ServletException;
import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;
import javax.servlet.http.HttpServletResponse;
import org.springframework.stereotype.Component;

/**
 * 处理跨域问题
 */
@Component
public class OriginFilter implements Filter {

    @Override
    public void init(FilterConfig filterConfig) throws ServletException {

    }

    @Override
    public void doFilter(ServletRequest req, ServletResponse res,
        FilterChain chain) throws IOException, ServletException {
        HttpServletResponse response = (HttpServletResponse) res;
        response.setHeader("Access-Control-Allow-Origin", "*");
        response.setHeader("Access-Control-Allow-Methods", "POST, GET, OPTIONS, DELETE,PUT");
        response.setHeader("Access-Control-Max-Age", "3600");
        response.setHeader("Access-Control-Allow-Headers", "x-requested-with");
        chain.doFilter(req, res);
    }

    @Override
    public void destroy() {
        // TODO Auto-generated method stub
    }
}

```

#### 2.4 @CrossOrgin注解

##### 2.4.1 接口上使用
```
@RestController
@RequestMapping("/account")
public class AccountController {

    @CrossOrigin
    @GetMapping("/{id}")
    public Account retrieve(@PathVariable Long id) {
        // ...
    }

    @DeleteMapping("/{id}")
    public void remove(@PathVariable Long id) {
        // ...
    }
}

```
对于上述代码，官方给出如下一段说明：
You can add to your @RequestMapping annotated handler method a @CrossOrigin annotation in order to enable CORS on it (by default @CrossOrigin allows all origins and the HTTP methods specified in the @RequestMapping annotation).

意思就是可以直接在@RequestMapping接口上使用@CrossOrigin实现跨域。@CrossOrigin默认允许所有访问源和访问方法。



##### 2.4.2 整个Controller上使用
```
@CrossOrigin(origins = "http://domain2.com", maxAge = 3600)
@RestController
@RequestMapping("/account")
public class AccountController {

    @GetMapping("/{id}")
    public Account retrieve(@PathVariable Long id) {
        // ...
    }

    @DeleteMapping("/{id}")
    public void remove(@PathVariable Long id) {
        // ...
    }
}

```
这里，可以对@CrossOrigin设置特定的访问源，而不是使用默认配置。


### 3. 公司项目中实现跨域的代码

```java
package com.lgw.mavenparent.config;

import org.apache.commons.logging.Log;
import org.apache.commons.logging.LogFactory;
import javax.servlet.*;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

public class CorsFilter implements Filter {

    private final Log log = LogFactory.getLog(CorsFilter.class);

    public CorsFilter(){
        log.info("SimpleCORSFilter init");
    }


    @Override
    public void init(FilterConfig filterConfig) throws ServletException {

    }

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {

        HttpServletRequest request = (HttpServletRequest) servletRequest;
        HttpServletResponse response = (HttpServletResponse) servletResponse;

        String clientOrigin = request.getHeader("origin");
        response.addHeader("Access-Control-Allow-Origin", clientOrigin);
        if(request.getRequestURI().contains("/findMarGrpPage")
                ||request.getRequestURI().contains("/itemquery/list")
                ||request.getRequestURI().contains("/getPersonalizedSetting")
                ||request.getRequestURI().contains("/manage/single")
                ||request.getRequestURI().contains("findAllExpInfo")){

        }else {
            response.setHeader("Access-Control-Allow-Methods", "POST, GET, DELETE, PUT");
            response.setHeader("Access-Control-Allow-Credentials", "true");
            response.setHeader("Access-Control-Max-Age", "3600");
            response.setHeader("Access-Control-Allow-Headers", "Accept, Content-Type, Origin, Authorization, X-Auth-Tkoen");
            response.setHeader("Access-Control-Expose-Headers", "X-Auth-Token");
        }

        if(request.getMethod().equals("OPTIONS")){
            response.setStatus(HttpServletResponse.SC_OK);
        }else{
            filterChain.doFilter(request, response);
        }
        
    }

    @Override
    public void destroy() {

    }
}

```




参考：
[https://www.jb51.net/article/110848.htm](https://www.jb51.net/article/110848.htm)

[http://www.cnblogs.com/m4tech/p/7871715.html](http://www.cnblogs.com/m4tech/p/7871715.html)

[SpringBoot配置Cors解决跨域请求问题](https://www.cnblogs.com/yuansc/p/9076604.html)

[SpringBoot解决ajax跨域问题](https://www.cnblogs.com/smiler/p/8509062.html)