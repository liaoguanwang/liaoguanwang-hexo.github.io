---
title: Java日志框架
date: 2019-08-26 23:49:20
categories:
- 未分类
tags:
---

### 1.Logback
 默认情况下springboot是不将日志输出到日志文件中，这里对日志框架的支持有两种配置方式：
*  application.properties 或  application.yml （系统层面）
* logback-spring.xml                               （自定义文件方式）

第一种方式比较简单，可做的事情也比较简单，比如：只能配置日志文件的路径、日志文件的格式等
第二种方式比较复杂，比如有如下需求的话:
1、区分 info 和 error 日志
2、每天产生一个日志文件。

以下是这两种方式的配置方式
#### 1、采用application.properties 或 application.yml配置方式
```yml
logging:
  pattern:
    console: "%d - %msg%n"
  path: /Users/yg/Desktop/sell222/                
  file: demo.log
  level:
    com.imooc.LoggerTest: trace
```
注意：
1. 若只配置logging.path，那么将会在/Users/yg/Desktop/sell222/文件夹生成一个日志文件为spring.log（ps：该文件名是固定的，不能更改）。如果path路径不存在，会自动创建该文件夹
2. 若只配置logging.file，那将会在项目的当前路径下生成一个demo.log日志文件。这里可以使用绝对路径如，会自动在e盘下创建文件夹和相应的日志文件。
3. logging.path和logging.file同时配置时，只有logging.file生效哦。
4. 都可以是相对路径或者绝对路径。

#### 2、采用 logback-spring.xml  配置方式
```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration debug="true">

    <!-- 彩色日志 -->
    <!-- 彩色日志依赖的渲染类 -->
    <conversionRule conversionWord="clr" converterClass="org.springframework.boot.logging.logback.ColorConverter" />
    <conversionRule conversionWord="wex" converterClass="org.springframework.boot.logging.logback.WhitespaceThrowableProxyConverter" />
    <!-- 彩色日志格式 -->
    <property name="CONSOLE_LOG_PATTERN" value="%clr(%d{yyyy-MM-dd HH:mm:ss.SSS}){faint} %clr(%5p) %clr(${PID:- }) [%t]{magenta} %clr(---){faint} %clr([%15.15t]){faint} %clr(%-40.40logger{39}){cyan} %clr(:){faint} %m%n%wex"/>
    <property name="FILE_LOG_PATTERN" value="%d{yyyy-MM-dd HH:mm:ss.SSS} %5p ${PID:- } --- [%t] %-40.40logger{39} : %m%n%wex"/>
     
    <!--多环境的日志输出-->
    <!--根据不同环境（prod:生产环境，test:测试环境，dev:开发环境）来定义不同的日志输出，-->
    <!--在 logback-spring.xml中使用 springProfile 节点来定义，方法如下：-->
    <springProfile name="prod">
        <property name="LOG_HOME" value="/data/dubbo/platform-action/log/" />
    </springProfile>
    <springProfile name="stg">
        <property name="LOG_HOME" value="/data/dubbo/platform-action/log/" />
    </springProfile>
    <springProfile name="dev">
        <property name="LOG_HOME" value="/data/dubbo/platform-action/log/" />
    </springProfile>
    <springProfile name="pre">
        <property name="LOG_HOME" value="/data/dubbo/platform-action/log/" />
    </springProfile>

    <!--输出到控制台-->
    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>${CONSOLE_LOG_PATTERN}</pattern>
            <charset>utf8</charset>
        </encoder>
    </appender>

    <!--info 级别的日志-->
    <!-- 按照每天生成日志文件 -->
    <appender name="INFO" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <level>INFO</level>
            <onMatch>ACCEPT</onMatch>
            <onMismatch>DENY</onMismatch>
        </filter>
        <encoder>
            <pattern>${FILE_LOG_PATTERN}</pattern>
        </encoder>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">

            <!--日志文件输出的文件名-->
            <fileNamePattern>${LOG_HOME}/info.%d{yyyy-MM-dd}.log</fileNamePattern>

            <!--日志文件保留天数-->
            <MaxHistory>30</MaxHistory>
        </rollingPolicy>

        <!--日志文件最大的大小-->
        <triggeringPolicy class="ch.qos.logback.core.rolling.SizeBasedTriggeringPolicy">
          <MaxFileSize>10MB</MaxFileSize>
        </triggeringPolicy>
    </appender>

    
    <!--WARN 级别的日志-->
    <appender name="WARN" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <level>WARN</level>
            <onMatch>ACCEPT</onMatch>
            <onMismatch>DENY</onMismatch>
        </filter>
        <encoder>
            <pattern>${FILE_LOG_PATTERN}</pattern>
        </encoder>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>${LOG_HOME}/warn.%d{yyyy-MM-dd}.log</fileNamePattern>
            <MaxHistory>30</MaxHistory>
        </rollingPolicy>
    </appender>

    <!--ERROR 级别的日志-->
    <appender name="ERROR" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <level>ERROR</level>
            <onMatch>ACCEPT</onMatch>
            <onMismatch>DENY</onMismatch>
        </filter>
        <encoder>
            <pattern>${FILE_LOG_PATTERN}</pattern>
        </encoder>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>${LOG_HOME}/error.%d{yyyy-MM-dd}.log</fileNamePattern>
            <MaxHistory>30</MaxHistory>
        </rollingPolicy>
    </appender>
    
    <!-- 日志输出级别 -->
    <root level="INFO">
        <appender-ref ref="CONSOLE" />
        <appender-ref ref="INFO"/>
        <appender-ref ref="WARN"/>
        <appender-ref ref="ERROR"/>
    </root>
</configuration>
```

注意：
1. 有些人配置文件里面有看到 layout（如下如），比如不要慌，因为encoder 和 layout 在作用上没有本质区别，但是自0.9.19版本之后，极力推荐使用encoder。
```xml
<appender name="consoleLog" class="ch.qos.logback.core.ConsoleAppender">
    <layout class="ch.qos.logback.classic.PatternLayout">
        <pattern>
            %d - %m%n
        </pattern>
    </layout>
</appender>
```
2. 什么Spring Boot推荐使用logback-spring.xml来替代logback.xml来配置logback日志的问题分析？
![](https://static.oschina.net/uploads/img/201805/11153235_m6Yg.png)

即，logback.xml加载早于application.properties，所以如果你在logback.xml使用了变量时，而恰好这个变量是写在application.properties时，那么就会获取不到，只要改成logback-spring.xml就可以解决。

3. idea中可以通过 command+o 查看某个类名，比如本例中我要查找系统的日志级别，
![](https://static.oschina.net/uploads/space/2018/0511/145249_QOnl_914271.png)

4. 上面用到了过滤器 Filter，执行一个过滤器会有返回个枚举值，即DENY，NEUTRAL，ACCEPT其中之一。具体用法可以见![
logback logback.xml常用配置详解（三）](https://blog.csdn.net/haidage/article/details/6794540)

LevelFilter： 级别过滤器，根据日志级别进行过滤。如果日志级别等于配置级别，过滤器会根据onMath 和 onMismatch接收或拒绝日志。

ThresholdFilter： 临界值过滤器，过滤掉低于指定临界值的日志。当日志级别等于或高于临界值时，过滤器返回NEUTRAL；当日志级别低于临界值时，日志会被拒绝。

参考：[Spring boot 日志框架Logback的使用以及配置详解](https://my.oschina.net/360yg/blog/1810625)