---
title: 【spring】spring基础 初始化属性值
date: 2015-10-14 16:42:20
tags: spring基础-初始化属性值
reward: true
---


###属性方法对比

Spring InitializingBean和BeanPostProcessor与init-method

<!--more-->

###springbean的实例化过程如下：
 
   ![](https://i.imgur.com/Gt6WF8W.jpg)

大概的bean初始化流程图就是这样，从接口的名字其实我们也可以发现点什么？？


initialzingbean 的作用就是在bean初始化后执行定制化的操作。

其实spring中的容器都有生命周期：

Spring 允许在 Bean 在初始化完成后以及 Bean 销毁前执行特定的操作，常用的设定方式有以下三种：

	1.通过实现 InitializingBean/DisposableBean 接口来定制初始化之后/销毁之前的操作方法；

	2.通过 <bean> 元素的 init-method/destroy-method 属性指定初始化之后 /销毁之前调用的操作方法；

	3.在指定方法上加上@PostConstruct 或@PreDestroy注解来制定该方法是在初始化之后还是销毁之前调用。

总结：

spring为bean提供了两种初始化bean的方式，实现InitializingBean接口，实现afterPropertiesSet方法，或者在配置文件中通过init-method指定，两种方式可以同时使用
实现InitializingBean接口是直接调用afterPropertiesSet方法，比通过反射调用init-method指定的方法效率相对来说要高点。

但是init-method方式消除了对spring的依赖
先调用afterPropertiesSet，再执行 init-method 方法，如果调用afterPropertiesSet方法时出错，则不调用init-method指定的方法。

### 3.BeanPostProcessor

	针对所有Spring上下文中所有的bean，可以在配置文档applicationContext.xml中配置一个BeanPostProcessor

    然后对所有的bean进行一个初始化之前和之后的代理。BeanPostProcessor接口中有两个方法： 

     postProcessBeforeInitialization和postProcessAfterInitialization。 postProcessBeforeInitialization方法在bean初始化之前执行， postProcessAfterInitialization方法在bean初始化之后执行。

### 上代码

###1.initialzingbean


	/**
	 * 测试 InitializingBean
	 * @author QRZ
	 *
	 */
	public class TestInitialzingbean implements InitializingBean {

	String name;
	
    public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	private static Logger log = LoggerFactory.getLogger(TestInitialzingbean.class);
	@Override
	public void afterPropertiesSet() throws Exception {
		System.out.println("222222");
		log.warn("测试InitializingBean！！ 我已经被初始化了");
		
	}

### BeanPostProcessor


	public class TestBeanPostProcessor implements BeanPostProcessor {

	@Override
	public Object postProcessAfterInitialization(Object arg0, String arg1)
			throws BeansException {
		System.out.println("实例化完成后！");
		return null;
	}

	@Override
	public Object postProcessBeforeInitialization(Object arg0, String arg1)
			throws BeansException {
		System.out.println("实例化之前！");
		return null;
	}

### 总之，afterPropertiesSet 和init-method之间的执行顺序是afterPropertiesSet 先执行，init-method 后执行。从BeanPostProcessor的作用，可以看出最先执行的是postProcessBeforeInitialization，然后是afterPropertiesSet，然后是init-method，然后是postProcessAfterInitialization。