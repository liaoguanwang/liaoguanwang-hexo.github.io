---
title: 【转】Springboot-Mybatis 读取配置文件
date: 2019-08-18 23:41:10
tags:
- 配置
- MyBatis
categories: 
- SpringBoot
keywors: 
description: 
top_img: 
cover: /img/springboot.jpg
---
Springboot-Mybatis配置整理
## 1. 加载mybatis的配置
### 1.1 手写配置，写死在代码里
```java
import java.io.IOException;
    import java.util.Properties;

    import javax.sql.DataSource;

    import org.apache.commons.logging.Log;
    import org.apache.commons.logging.LogFactory;
    import org.apache.ibatis.plugin.Interceptor;
    import org.apache.ibatis.session.SqlSessionFactory;
    import org.mybatis.spring.SqlSessionFactoryBean;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Configuration;
    import org.springframework.core.io.DefaultResourceLoader;
    import org.springframework.core.io.Resource;
    import org.springframework.core.io.support.PathMatchingResourcePatternResolver;
    import org.springframework.jdbc.datasource.DataSourceTransactionManager;
    import org.springframework.transaction.PlatformTransactionManager;
    import org.springframework.transaction.annotation.EnableTransactionManagement;
    import org.springframework.transaction.annotation.TransactionManagementConfigurer;

    import com.github.pagehelper.PageHelper;

    @Configuration
    @EnableTransactionManagement
    public class MybatisConfiguration implements TransactionManagementConfigurer{

        private static Log logger = LogFactory.getLog(MybatisConfiguration.class);

        @Autowired
        private DataSource dataSource;

        // 提供SqlSeesion
        @Bean(name = "sqlSessionFactory")
        public SqlSessionFactory sqlSessionFactoryBean() {
            try {
                SqlSessionFactoryBean sessionFactoryBean = new SqlSessionFactoryBean();
                sessionFactoryBean.setDataSource(dataSource);
                // 手写配置
                // 配置类型别名
                sessionFactoryBean.setTypeAliasesPackage("com.zsx.entity");

                // 配置mapper的扫描，找到所有的mapper.xml映射文件
                Resource[] resources = new PathMatchingResourcePatternResolver()
                        .getResources("classpath:mybatis/mapper/*.xml");
                sessionFactoryBean.setMapperLocations(resources);

                // 加载全局的配置文件
                sessionFactoryBean.setConfigLocation(
                        new DefaultResourceLoader().getResource("classpath:mybatis/mybatis-config.xml"));

                //添加插件 
                sessionFactoryBean.setPlugins(new Interceptor[]{pageHelper()});

                return sessionFactoryBean.getObject();
            } catch (IOException e) {
                logger.warn("mybatis resolver mapper*xml is error");
                return null;
            } catch (Exception e) {
                logger.warn("mybatis sqlSessionFactoryBean create error");
                return null;
            }
        }


        @Bean
        public PlatformTransactionManager annotationDrivenTransactionManager() {
            return new DataSourceTransactionManager(dataSource);
        }


        @Bean
        public PageHelper pageHelper(){
            logger.info("MyBatis分页插件PageHelper");
            //分页插件
            PageHelper pageHelper = new PageHelper();
            Properties properties = new Properties();
            properties.setProperty("offsetAsPageNum", "true");
            properties.setProperty("rowBoundsWithCount", "true");
            properties.setProperty("reasonable", "true");
            properties.setProperty("supportMethodsArguments", "true");
            properties.setProperty("returnPageInfo", "check");
            properties.setProperty("params", "count=countSql");
            pageHelper.setProperties(properties);
            return pageHelper;
        }


    }
```

### 1.2 读取配置文件方式

先在配置文件application.yml中添加
```yml
# MyBatis
mybatis:
    # 配置类型别名
    typeAliasesPackage: com.zsx.entity
    # 配置mapper的扫描，找到所有的mapper.xml映射文件
    mapperLocations: classpath:mybatis/mapper/*.xml
    # 加载全局的配置文件
    configLocation: classpath:mybatis/mybatis-config.xml
```

然后配置文件为：
```java
   import java.io.IOException;

    import javax.sql.DataSource;

    import org.apache.commons.logging.Log;
    import org.apache.commons.logging.LogFactory;
    import org.apache.ibatis.session.SqlSessionFactory;
    import org.mybatis.spring.SqlSessionFactoryBean;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.beans.factory.annotation.Value;
    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Configuration;
    import org.springframework.core.io.DefaultResourceLoader;
    import org.springframework.core.io.Resource;
    import org.springframework.core.io.support.PathMatchingResourcePatternResolver;
    import org.springframework.jdbc.datasource.DataSourceTransactionManager;
    import org.springframework.transaction.PlatformTransactionManager;
    import org.springframework.transaction.annotation.EnableTransactionManagement;
    import org.springframework.transaction.annotation.TransactionManagementConfigurer;

    @Configuration
    @EnableTransactionManagement
    public class MybatisConfiguration implements TransactionManagementConfigurer{

        private static Log logger = LogFactory.getLog(MybatisConfiguration.class);

    //  配置类型别名
        @Value("${mybatis.typeAliasesPackage}")
        private String typeAliasesPackage;

    //  配置mapper的扫描，找到所有的mapper.xml映射文件
        @Value("${mybatis.mapperLocations}")
        private String mapperLocations;

    //  加载全局的配置文件
        @Value("${mybatis.configLocation}")
        private String configLocation;

        @Autowired
        private DataSource dataSource;
        // DataSource配置
    //  @Bean
    //  @ConfigurationProperties(prefix = "spring.datasource")
    //  public DruidDataSource dataSource() {
    //      return new com.alibaba.druid.pool.DruidDataSource();
    //  }

        // 提供SqlSeesion
        @Bean(name = "sqlSessionFactory")
        public SqlSessionFactory sqlSessionFactoryBean() {
            try {
                SqlSessionFactoryBean sessionFactoryBean = new SqlSessionFactoryBean();
                sessionFactoryBean.setDataSource(dataSource);

                // 读取配置 
                sessionFactoryBean.setTypeAliasesPackage(typeAliasesPackage);

                // 
                Resource[] resources = new PathMatchingResourcePatternResolver()
                        .getResources(mapperLocations);
                sessionFactoryBean.setMapperLocations(resources);
    //      //
                sessionFactoryBean.setConfigLocation(
                        new DefaultResourceLoader().getResource(configLocation));

                //添加插件  （改为使用配置文件加载了）
    //          sqlSessionFactoryBean.setPlugins(new Interceptor[]{pageHelper()});

                return sessionFactoryBean.getObject();
            } catch (IOException e) {
                logger.warn("mybatis resolver mapper*xml is error");
                return null;
            } catch (Exception e) {
                logger.warn("mybatis sqlSessionFactoryBean create error");
                return null;
            }
        }


    //  @Bean
    //    public SqlSessionTemplate sqlSessionTemplate(SqlSessionFactory sqlSessionFactory) {
    //        return new SqlSessionTemplate(sqlSessionFactory);
    //    }

    //  @Bean
    //  public PlatformTransactionManager transactionManager(){
    //      return new DataSourceTransactionManager(dataSource);
    //  }


        @Bean
        public PlatformTransactionManager annotationDrivenTransactionManager() {
            return new DataSourceTransactionManager(dataSource);
        }


    }

```

最后还有配置一个扫描mapper的类：
```java
import org.mybatis.spring.mapper.MapperScannerConfigurer;
import org.springframework.boot.autoconfigure.AutoConfigureAfter;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
//TODO 注意，由于MapperScannerConfigurer执行的比较早，所以必须有下面的注解
@AutoConfigureAfter({MybatisConfiguration.class})
public class MyBatisMapperScannerConfig {

    @Bean
    public MapperScannerConfigurer mapperScannerConfigurer(){
        MapperScannerConfigurer mapperScannerConfigurer = new MapperScannerConfigurer();

        mapperScannerConfigurer.setSqlSessionFactoryBeanName("sqlSessionFactory");
        mapperScannerConfigurer.setBasePackage("com.zsx.dao");


        return mapperScannerConfigurer;
    }

}

```

还有一个 mybatis-config.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>
    <properties>
        <property name="dialect" value="mysql" />
    </properties>
    <settings>
        <!-- 开启驼峰匹配 -->
        <setting name="mapUnderscoreToCamelCase" value="true"/>
        <!-- 这个配置使全局的映射器启用或禁用缓存。系统默认值是true，设置只是为了展示出来 -->
        <setting name="cacheEnabled" value="true" />
        <!-- 全局启用或禁用延迟加载。当禁用时，所有关联对象都会即时加载。 系统默认值是true，设置只是为了展示出来 -->
        <setting name="lazyLoadingEnabled" value="true" />
        <!-- 允许或不允许多种结果集从一个单独的语句中返回（需要适合的驱动）。 系统默认值是true，设置只是为了展示出来 -->
        <setting name="multipleResultSetsEnabled" value="true" />
        <!--使用列标签代替列名。不同的驱动在这方便表现不同。参考驱动文档或充分测试两种方法来决定所使用的驱动。 系统默认值是true，设置只是为了展示出来 -->
        <setting name="useColumnLabel" value="true" />
        <!--允许 JDBC 支持生成的键。需要适合的驱动。如果设置为 true 则这个设置强制生成的键被使用，尽管一些驱动拒绝兼容但仍然有效（比如 
            Derby）。 系统默认值是false，设置只是为了展示出来 -->
        <setting name="useGeneratedKeys" value="false" />
        <!--配置默认的执行器。SIMPLE 执行器没有什么特别之处。REUSE 执行器重用预处理语句。BATCH 执行器重用语句和批量更新 系统默认值是SIMPLE，设置只是为了展示出来 -->
        <setting name="defaultExecutorType" value="SIMPLE" />
        <!--设置超时时间，它决定驱动等待一个数据库响应的时间。 系统默认值是null，设置只是为了展示出来 -->
        <setting name="defaultStatementTimeout" value="25000" />
    </settings>

    <!-- 分页助手 -->
    <plugins>
        <plugin interceptor="com.github.pagehelper.PageHelper">
            <!-- 数据库方言 -->
            <property name="dialect" value="mysql" />
            <property name="offsetAsPageNum" value="true" />
            <!-- 设置为true时，使用RowBounds分页会进行count查询 会去查询出总数 -->
            <property name="rowBoundsWithCount" value="true" />
            <property name="pageSizeZero" value="true" />
            <property name="reasonable" value="true" />
        </plugin>
    </plugins>
</configuration>    

```
参考：https://blog.csdn.net/javahighness/article/details/53044655