---
title: 【spring源码】 Bean的生命周期
date: 2015-10-14 16:42:20
tags: Bean的生命周期
reward: true
---


###Bean的生命周期

我们知道一个对象的生命周期：创建（实例化-初始化）-使用-销毁，而在Spring中，Bean对象周期当然遵从这一过程，但是Spring提供了许多对外接口，允许开发者对三个过程（实例化、初始化、销毁）的前后做一些操作。 

　　这里就实例化、初始化区别做一个说明，在Spring Bean中，实例化是为bean对象开辟空间（具体可以理解为构造函数的调用），初始化则是对属性的初始化，说的具体点，这里的属性初始化应该是属性的注入（构造函数也可以有属性的初始化语句，但不属于这一部分），属性注入是通过setter方法注入属性（不管是注解方式还是bean配置property属性方式，其实质都是通过属性的setter方法实现的）。


<!--more-->

### 相关接口、方法说明

Bean自身方法：init-method/destroy-method，通过为配置文件bean定义中添加相应属性指定相应执行方法。
Bean级生命周期接口：BeanNameAware、BeanFactoryAware、InitializingBean和DiposableBean这些接口的方法。

每个Bean选择实现，可选择各自的个性化操作。
容器级生命周期接口方法：这个包括了InstantiationAwareBeanPostProcessor 和 BeanPostProcessor 这两个接口实现（前者继承自后者），一般称它们的实现类为“后处理器”（其实我觉得这个名称对新手有误导的意思），这些接口是每个bean实例化或初始化时候都会调用。

工厂后处理器接口方法：这些方法也是容器级别的，但它们是在上下文装置配置文件之后调用，例如BeanFactoryPostProcessor、 CustomAutowireConfigurer等。 
（这里说一点，这些类名都很长，有的也挺相似，学习的话要仔细看清楚哪个是哪个。）

![](https://i.imgur.com/ESyJmBG.png)


根据分析大概是这个样子：

	1.Bean容器找到配置文件中Spring Bean的定义。
	2.Bean容器利用Java Reflection API创建一个Bean的实例。
	3.如果涉及到一些属性值 利用set方法设置一些属性值。
	4.如果Bean实现了BeanNameAware接口，调用setBeanName()方法，传入Bean的名字。
	5.如果Bean实现了BeanClassLoaderAware接口，调用setBeanClassLoader()方法，传入ClassLoader对象的实例。
	6.如果Bean实现了BeanFactoryAware接口，调用setBeanClassLoader()方法，传入ClassLoader对象的实例。
	与上面的类似，如果实现了其他*Aware接口，就调用相应的方法。
	如果有和加载这个Bean的Spring容器相关的BeanPostProcessor对象，执行postProcessBeforeInitialization()方法
	7.如果Bean实现了InitializingBean接口，执行afterPropertiesSet()方法。
	8.如果Bean在配置文件中的定义包含init-method属性，执行指定的方法。
	9.如果有和加载这个Bean的Spring容器相关的BeanPostProcessor对象，执行postProcessAfterInitialization()方法
	10.当要销毁Bean的时候，如果Bean实现了DisposableBean接口，执行destroy()方法。
	11.当要销毁Bean的时候，如果Bean在配置文件中的定义包含destroy-method属性，执行指定的方法。

代码就不贴了，太多了


![](https://i.imgur.com/Lgc9TI4.png)