---
title: 【BUG】Write operations are not allowed in read-only mode (FlushMode.MANUAL):Turn
  your Session into FlushMode.COMMIT/AUTO or remove 'readOnly' marker from
  transaction definition.
date: 2019-08-14 23:51:05
tags:
---

## 1. 问题描述
Struts Problem Report
Struts has detected an unhandled exception:

```java
Messages:	
Write operations are not allowed in read-only mode (FlushMode.MANUAL): Turn your Session into FlushMode.COMMIT/AUTO or remove 'readOnly' marker from transaction definition.
File:	org/springframework/orm/hibernate5/HibernateTemplate.java
Line number:	1,093
Stacktraces
org.springframework.dao.InvalidDataAccessApiUsageException: Write operations are not allowed in read-only mode (FlushMode.MANUAL): Turn your Session into FlushMode.COMMIT/AUTO or remove 'readOnly' marker from transaction definition.
```
## 2. 解决方法

### 2.1 解决方法1
涉及到数据库的读写都要添加 @Transactional(readOnly = false, propagation = Propagation.REQUIRES_NEW )

```java
    @Transactional(readOnly = false, propagation = Propagation.REQUIRES_NEW )
    @Override
    public void save(User user) {
        userDao.save(user);
    }

    @Transactional(readOnly = false, propagation = Propagation.REQUIRES_NEW )
    @Override
    public void update(User user) {
        userDao.update(user);
    }

    @Transactional(readOnly = false, propagation = Propagation.REQUIRES_NEW )
    @Override
    public void remove(User user) {
        userDao.remove(user);
    }
```

### 2.2 解决方法2 
在应用程序上下文中使用bean下面的HibernateTemplate。
添加&lt;property name="checkWriteOperations" value="false"></property>
```xml
    <!-- 配置HibernateTemplate -->
    <bean id="hibernateTemplate" class="org.springframework.orm.hibernate5.HibernateTemplate">
        <!-- 注入sessionFactory -->
        <property name="sessionFactory" ref="sessionFactory"></property>
        <property name="checkWriteOperations" value="false"></property>
    </bean>
```

同时在类上要添加@Transactional
```java
@Transactional
@Service
public class UserServiceImpl implements UserService 
{
    ...
}
```

## 3. 错误原因
OpenSessionInViewFilter在getSession的时候,会把获取回来的session的flush mode 设为FlushMode.NEVER。然后把该sessionFactory绑定到TransactionSynchronizationManager，使request的整个过程都使用同一个session，在请求过后再接除该sessionFactory的绑定，最后closeSessionIfNecessary根据该session是否已和transaction绑定来决定是否关闭session。
在这个过程中，若HibernateTemplate 发现自当前session有不是readOnly的transaction，就会获取到FlushMode.AUTO Session，使方法拥有写权限。也即是，如果有不是readOnly的transaction就可以由Flush.NEVER转为Flush.AUTO,拥有insert,update,delete操作权限，如果没有transaction，并且没有另外人为地设flush model的话，则doFilter的整个过程都是Flush.NEVER。所以受transaction(声明式的事务)保护的方法有写权限，没受保护的则没有。

## 4. 原理分析
当使用Spring OpenSessionInViewFilter并尝试在Spring管理的事务之外执行持久性操作时，通常会看到该错误消息。
过滤器将会话设置为FlushMode.NEVER / MANUAL（取决于您使用的Spring和Hibernate的版本 - 它们大致相当）。
当Spring事务机制开始一个事务时，它将刷新模式更改为“COMMIT”。事务完成后，它会根据需要将其设置为NEVER / MANUAL。
如果你完全确定如果没有发生这种情况，那么下一个最可能的罪魁祸首就是非线程安全地使用Session。Hibernate Session必须只在一个线程中使用。如果它穿过线程之间，就会发生各种混乱。请注意，从Hibernate加载的实体可以保存对加载它的Session的引用，因此跨线程处理实体也可以导致从另一个线程访问Session。

参考资料：https://stackoverflow.com/questions/6810158/java-hibernate-write-operations-are-not-allowed-in-read-only-mode