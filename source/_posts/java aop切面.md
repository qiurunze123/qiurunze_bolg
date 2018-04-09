---
title: 【java-AOP】 aop注解类使用方法AspectJ
date: 2015-9-14 16:42:20
tags:  aop注解类使用方法AspectJ
reward: true
---

#  aop注解类使用方法AspectJ

###aop注解类使用方法AspectJ

1.AOP的作用

　　在OOP中，正是这种分散在各处且与对象核心功能无关的代码（横切代码）的存在，使得模块复用难度增加。AOP则将封装好的对象剖开，找出其中对多个对象产生影响的公共行为，并将其封装为一个可重用的模块，这个模块被命名为“切面”（Aspect），切面将那些与业务无关，却被业务模块共同调用的逻辑提取并封装起来，减少了系统中的重复代码，降低了模块间的耦合度，同时提高了系统的可维护性。


<!--more-->

###BeanFactory与ApplicationContext

　　ApplicationContext是BeanFactory的子接口，也被称为应用上下文。BeanFactory提供了Spring的配置框架和基本功能。

  ApplicationContext则添加了更多企业级功能（如国际化的支持），他另一重要优势在于当ApplicationContext容器初始化完成后，容器中所有的 singleton Bean 也都被实例化了，也就是说当你需要使用singleton Bean 是在应用中无需等待就可以用而其他BeanFactory接口的实现类，则会延迟到调用 getBean（）方法时构造。

  ApplicationContext的初始化时间会稍长些，调用getBean（）是由于Bean已经构造完毕，速度会更快。因此大部分系统都使用ApplicationContext，而只在资源较少的情况下，才考虑使用BeanFactory。

###AOP的实现策略

    （1）Java SE动态代理：
    使用动态代理可以为一个或多个接口在运行期动态生成实现对象，生成的对象中实现接口的方法时可以添加增强代码，从而实现AOP。缺点是只能针对接口进行代理，另外由于动态代理是通过反射实现的，有时可能要考虑反射调用的开销。
    （2）字节码生成（CGLib 动态代理）
    动态字节码生成技术是指在运行时动态生成指定类的一个子类对象，并覆盖其中特定方法，覆盖方法时可以添加增强代码，从而实现AOP。其常用工具是cglib。
    （3）定制的类加载器
    当需要对类的所有对象都添加增强，动态代理和字节码生成本质上都需要动态构造代理对象，即最终被增强的对象是由AOP框架生成，不是开发者new出来的。解决的办法就是实现自定义的类加载器，在一个类被加载时对其进行增强。JBoss就是采用这种方式实现AOP功能。
    （4）代码生成
    利用工具在已有代码基础上生成新的代码，其中可以添加任何横切代码来实现AOP。
    （5）语言扩展
    可以对构造方法和属性的赋值操作进行增强，AspectJ是采用这种方式实现AOP的一个常见Java语言扩展。

这种异常很常见，也很好发现，因为都提示了“Java heap space”了，定位问题的话，根据异常堆栈分析就好了，行号都有指示。解决方案的话，可以调大堆的大小或者从代码上检视是否存在某些对象生命周期过长、持有状态时间过长的情况，长时间少程序运行期间的内存消耗。

###注解方式实现


    (1):先引入aop相关的jar文件
    spring-aop-3.2.5.RELEASE.jar【去spring3.2源码里面找】
    aopalliance.jar【去spring2.5源码/lib/aopalliance文件里面找】
    aspectjweaver.jar【去spring2.5源码/lib/aspectj文件里面找】或者【aspectj-1.8.2/lib/aspectjweaver.jar】
    aspectjrt.jar【去spring2.5源码/lib/aspectj文件里面找】或者【aspectj-1.8.2/lib/aspectjrt.jar】
    注意：用到的spring2.5版本的jar本舰，如果用jd1.7版本可能会出现问题，
     需要升级以下aspectj组件，即使用aspectj-1.8.2版本中提供的jar文件aspectjweaver.jar和aspectjrt.jar  
     (2)bean.xml中引入aop名称空间

　　 技巧：找到文件spring-framework-3.2.5.RELEASE/docs/spring-framework-reference/htmlsingle

　　 打开index.html搜索xmlns:aop然后找到下面红色三句话，分别拷贝到bean.xml中


###xml配置

     <?xml version="1.0" encoding="UTF-8"?>
     <beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:p="http://www.springframework.org/schema/p"
    xmlns:context="http://www.springframework.org/schema/context"
    xmlns:aop="http://www.springframework.org/schema/aop"
    xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd">
      </beans>

### 开启扫描配置

    :bean.xml中开启aop注解扫描，如下配置所示：

    <beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:p="http://www.springframework.org/schema/p"
    xmlns:context="http://www.springframework.org/schema/context"
    xmlns:aop="http://www.springframework.org/schema/aop"
    xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd">

      <!-- 开启注解扫描 -->
      <context:component-scan base-package="com.bie.aop"></context:component-scan>   
        
      <!-- 开启aop注解方式,默认为false -->    
      <aop:aspectj-autoproxy></aop:aspectj-autoproxy>
      
     </beans>

###编写切面类：
此处的save ， delete啥的方法名称 自己定义
![](https://i.imgur.com/J7SuNaE.png)

在这里调用：
![](https://i.imgur.com/hJRDeMh.png)


