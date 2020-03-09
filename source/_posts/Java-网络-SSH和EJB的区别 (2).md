---
title: POJO|JaveBean|VO|DTO
date: 2019-08-05 23:20:36
categories: 
- Java
tags: 
 - JavaWeb
 - 概念
keywors: 
description: 
top_img: 
cover: /img/java3.jpg
---

# POJO
## 概念定义
POJO（Plain Ordinary Java Object）简单的Java对象，实际就是普通JavaBeans，是为了避免和EJB混淆所创造的简称。
使用POJO名称是为了避免和EJB混淆起来, 而且简称比较直接. 其中有一些属性及其getter setter方法的类,没有业务逻辑，有时可以作为VO(value -object)或dto(Data Transform Object)来使用.当然,如果你有一个简单的运算属性也是可以的,但不允许有业务方法,也不能携带有connection之类的方法。

## 自身特点
POJO是Plain Ordinary Java Object的缩写不错，但是它通指没有使用Entity Beans的普通java对象，可以把POJO作为支持业务逻辑的协助类。
POJO实质上可以理解为简单的实体类，顾名思义POJO类的作用是方便程序员使用数据库中的数据表，对于广大的程序员，可以很方便的将POJO类当做对象来进行使用，当然也是可以方便的调用其get,set方法。POJO类也给我们在struts框架中的配置带来了很大的方便。

## 实例
POJO有一些private的参数作为对象的属性。然后针对每个参数定义了get和set方法作为访问的接口。
例子如下：
```java
public class User {
    private long id;
    private String name;

    public void setId(long id) {
        this.id = id;
    }

    public void setName(String name) {
        this.name = name;
    }

    public long getId() {
        return id;
    }

    public String getName() {
        return name;
    }
}
```

POJO对象有时也被称为Data对象，大量应用于表现现实中的对象。如果项目中使用了Hibernate框架，有一个关联的xml文件，使对象与数据库中的表对应，对象的属性与表中的字段相对应。

## POJO与JavaBean的区别
POJO 和JavaBean是我们常见的两个关键字，一般容易混淆，POJO全称是Plain Ordinary Java Object / Pure Old Java Object，中文可以翻译成：普通Java类，具有一部分getter/setter方法的那种类就可以称作POJO。
一般在web应用程序中建立一个数据库的映射对象时，我们只能称它为POJO。POJO(Plain Old Java Object)这个名字用来强调它是一个普通java对象，而不是一个特殊的对象，其主要用来指代那些没有遵从特定的Java对象模型、约定或框架（如EJB）的Java对象。理想地讲，一个POJO是一个不受任何限制的Java对象（除了Java语言规范）。

但是JavaBean则比 POJO复杂很多， Java Bean 是可复用的组件，对 Java Bean 并没有严格的规范，理论上讲，任何一个 Java 类都可以是一个 Bean 。
但通常情况下，由于 Java Bean 是被容器所创建（如 Tomcat) 的，所以 Java Bean 应具有一个无参的构造器。
另外，通常 Java Bean 还要实现 Serializable 接口用于实现 Bean 的持久性。 Java Bean 是不能被跨进程访问的。
JavaBean是一种组件技术，就好像你做了一个扳子，而这个扳子会在很多地方被拿去用，这个扳子也提供多种功能(你可以拿这个扳子扳、锤、撬等等)，而这个扳子就是一个组件。

## 错误的认识
POJO是这样的一种“纯粹的”JavaBean，在它里面除了JavaBean规范的方法和属性没有别的东西，即private属性以及对这个属性方法的public的get和set方法。我们会发现这样的JavaBean很“单纯”，它只能装载数据，作为数据存储的载体，而不具有业务逻辑处理的能力。

## 真正的意思
POJO = "Plain Old Java Object"，是MartinFowler等发明的一个术语，用来表示普通的Java对象，不是JavaBean, EntityBean 或者 SessionBean。POJO不担当任何特殊的角色，也不实现任何特殊的Java框架的接口如，EJB，JDBC等等。
即POJO是一个简单的普通的Java对象，它不包含业务逻辑或持久逻辑等，但不是JavaBean、EntityBean等，不具有任何特殊角色和不继承或不实现任何其它Java框架的类或接口。

**下面是摘自Martin Fowler个人网站的一句话：**
"We wondered why people were so against using regular objects in their systems and concluded that it was because simple objects lacked a fancy name. So we gave them one, and it's caught on very nicely."－－Martin Fowler
我们疑惑为什么人们不喜欢在他们的系统中使用普通的对象，我们得到的结论是——普通的对象缺少一个响亮的名字，因此我们给它们起了一个，并且取得了很好的效果。——Martin Fowler

参考：https://blog.csdn.net/qq_27093465/article/details/52527270

# DTO

## 百度百科的解读

DTO是Data Transfer Object 的简写，既数据传输对象。
是一种设计模式之间传输数据的软件应用系统。数据传输目标往往是数据访问对象从数据库中检索的数据。数据传输对象与数据交互对象或数据访问对象之间是一个不具备有任何行为除了存储和检索的数据。（访问和存取器）
 
## 维基百科的解读

A data transfer object(DTO) is an  object that carries data between two process.
The difference between data transfer and business objects or data access objects is that a DTO does not have any behavior except for storage and retrieval of its own data(mutators and accesssors).DTOs are simple objects that should not contain any business logic that would require testing.
 
和百度百科不同的是，DTO和MO（Model Object）与BO（Business Object）的不同之处在于DTO没有任何业务行为（贫血模式）只作为数据的存储。
 
原链接：https://en.wikipedia.org/wiki/Data_transfer_object

References 下有许多有料的参考文章。

## 经典文章的解读
 
- ### 2.3.1 博客园dax.net的观点：
    表现层于应用层之间是通过DTO来进行交互的，数据传输对象是没有行为的POCO对象，他的目的是为了对领域对象进行数据封装，实现层与层之间的数据传递。为何不直接将领域对象进行数据传递？因为领域对象更注重领域，DTO更注重数据。由于“富领域模型”的特点，这样会直接将领域对象的行为暴露给表现层。
    DTO本身不是业务对象，他是根据UI需求进行设计的。简单来说Model面向业务，我们是通过业务来定义Model的。而DTO是面向UI，通过UI的需求来定义的，通过DTO我们实现了表现层与Model层之间的解耦，表现层不引用Model。如果开发过程中我们的模型变了，而界面没变，我们只需改Model而不需要去改动表现层。
原文链接：http://www.cnblogs.com/daxnet/archive/2010/07/07/1772584.html
 
- ### 博客园loveis715的观点：
DTO用于在服务器与客户端之间或服务器与服务器之间进行数据传递，文章从问题出发，然后深入浅出的讨论了DTO以及相关的概念，值得一读。
原文链接：[DTO – 服务实现中的核心数据](http://www.cnblogs.com/loveis715/p/4379656.html)

 
- ### 微软MSDN的观点：
Create a data transfer object(DTO) that holds all data that is required for the remote call.Modify the remote signature to accept the DTO as the single parameter and to return a single DTO to the client.After the calling application receives the DTO and and stores it as a local object , the application can make a series of individual procedure calls to the DTO without incurring the overhead of remote calls.
 大致的观点是DTO可以有效的减少请求数量，文章中包含示例，讨论了问题的由来（为什么需要DTO），以及DTO使用过程中的一些注意事项。不过在文章的开头说到这个设计模式已经过时了，从此来看技术的更新迭代真的是很快，只是不知道DTO为什么过时了？以及哪些新的技术替代了DTO？
 原文链接：https://msdn.microsoft.com/en-us/library/ms978717.aspx
 
- ### Pattern of Enterprise  Application Architecture 作者观点：
    An object carries data between two processes in order to reduce the number of method calls.
    
    When you are working with a remote interface,such as Remote Facade,each call to it is expensive.As a result you should reduce the number of calls, and that means that you need to transfer more data with each call.One way to do this is to use  lots of parameters.However, this is often awkward  to program - indeed,it's often impossible with languages such as Java that return only a single value.
 
    The solution is to create a Data Transfer Object that can hold all the data for the call.It needs to be serializable to go across the connection.Usually an assembler is used on the server side to transfer data between the DTO and any domain objects.    
 
    Although the main reason for using a Data Transfer Object is to batch up what would be mutiple remote calls into single call,it's worth mentioning that another advantage is to encapsulate the serialization mechanism for transferring data over write.By encapsulating the serialization like this ,the DTOs keep this logic out of the rest of the code and also provide a clear point to change  serialization should you wish.
    原文链接：https://martinfowler.com/eaaCatalog/dataTransferObject.html

    观点和loveis715有一致的地方，简而言之DTO的存在就是为了帮助我们减少客户端请求而降低服务器压力，提升效率。作者还有一个观点大概是指在使用DTO后我们可以灵活定义数据模型，同时将数据模型和逻辑剥离开了。
    参考：https://www.cnblogs.com/simple-blog/p/7071740.html

# PO&VO
## PO
PO:persistant object 持久对象,可以看成是与数据库中的表相映射的java对象。使用Hibernate来生成PO是不错的选择。

## VO
VO:value object值对象。通常用于业务层之间的数据传递，和PO一样也是仅仅包含数据而已。但应是抽象出的业务对象,可以和表对应,也可以不,这根据业务的需要.

## 总结
有一种观点就是：PO只能用在数据层，VO用在商业逻辑层和表示层。各层操作属于该层自己的数据对象，这样就可以降低各层之间的耦合，便于以后系统的维护和扩展。如果将PO用在各个层中就相当于我们使用全局变量，我们知道在OO设计非常不赞成使用全局变量。

但是每次都得进行VO-PO的转换，也确实很烦。我觉得有时候也可以在某个商业逻辑或者表示层使用PO，此时在这个商业逻辑的过程中PO的状态是不发生变化的，比如显示一条商品详细信息的商业逻辑。

在开发过的项目中，规模都很小，我一直都把PO当VO用，因为PO确实很方便，结合Hibernate的DAO，我使用JAVA的集合对象作为值传递的载体，当然Struts也是我的不二之选。

我认为：在一些直观的，简单的，不易发生变化的，不需要涉及多个PO时，传递值还是使用PO好，这样可以减少大量的工作量（也就意味着减少bug，减少风险），也不需要担心未来的维护工作!

参考：https://zhidao.baidu.com/question/568202447.html

# 总结

- JavaBean 是一种JAVA语言写成的可重用组件。为写成JavaBean，类必须是具体的和公共的，并且具有无参数的构造器。JavaBean 通过提供符合一致性设计模式的公共方法将内部域暴露成员属性。众所周知，属性名称符合这种模式，其他Java 类可以通过自身机制发现和操作这些JavaBean 的属性。

- VO即value object值对象
主要体现在视图的对象，对于一个WEB页面将整个页面的属性封装成一个对象。然后用一个VO对象在控制层与视图层进行传输交换。

- DTO (经过处理后的PO，可能增加或者减少PO的属性)：
Data Transfer Object数据传输对象，主要用于远程调用等需要大量传输对象的地方。
比如我们一张表有100个字段，那么对应的PO就有100个属性。但是我们界面上只要显示10个字段，客户端用WEB service来获取数据，没有必要把整个PO对象传递到客户端，这时我们就可以用只有这10个属性的DTO来传递结果到客户端，这样也不会暴露服务端表结构.到达客户端以后，如果用这个对象来对应界面显示，那此时它的身份就转为VO。

- POJO（POJO是一种概念或者接口，身份及作用随环境变化而变化） ：
POJO有一些private的参数作为对象的属性。然后针对每个参数定义了get和set方法作为访问的接口
plain ordinary java object 简单java对象.
即POJO是一个简单的普通的Java对象，它不包含业务逻辑或持久逻辑等，但不是JavaBean、EntityBean等，不具有任何特殊角色和不继承或不实现任何其它Java框架的类或接口。
POJO对象有时也被称为Data对象，大量应用于表现现实中的对象。
一个POJO持久化以后就是PO。
直接用它传递、传递过程中就是DTO
直接用来对应表示层就是VO

    参考：https://zhidao.baidu.com/question/135819256365307645.html