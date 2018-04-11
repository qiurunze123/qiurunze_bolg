---
title: 【spring源码】 Bean的加载流程
date: 2015-10-14 16:42:20
tags: 源码-Bean的加载流程
reward: true
---


###Bean的加载流程

对于程序员来说，一个Web项目用到Spring，只是配置一下配置文件而已，Spring的加载过程相对是不太透明的，不太好去找加载的代码入口。

下面有很简单的一段代码可以作为Spring代码加载的入口：

 	1 ApplicationContext ac = new ClassPathXmlApplicationContext("spring.xml");
 	2 ac.getBean(XXX.class);


<!--more-->

ClassPathXmlApplicationContext用于加载CLASSPATH下的Spring配置文件，可以看到，第二行就已经可以获取到Bean的实例了，那么必然第一行就已经完成了对所有Bean实例的加载，因此可以通过ClassPathXmlApplicationContext作为入口。为了后面便于代码阅读，先给出一下ClassPathXmlApplicationContext这个类的继承关系：

![](https://i.imgur.com/ItY6ws9.png)

左下角的ApplicationContext应当还有一层继承关系，比较关键的一点是它是BeanFactory的子接口。

ApplicationContent 相比较beanfactory 添加了更多企业级应用，一般的大型项目都是使用ApplicationContent ,只有在资源比较少的情况下才是用beanfactory 

###ClassPathXmlApplicationContext存储内容

为了更理解ApplicationContext，举例：

ClassPathXmlApplicationContext举例，看一下里面存储的内容，加深对ApplicationContext的认识，以表格形式展现：
    

      |对象名            |类型 |	     作用          |归属类
      configResources	Resource[]	配置文件资源对象 ClassPathXmlApplicationContext

      configLocations	String[]	配置文件字符串数组，存储配置文件路径  AbstractRefreshableConfigApplicationContext
      beanFactory	DefaultListableBeanFactory	上下文使用的Bean工厂	AbstractRefreshableApplicationContext
      beanFactoryMonitor Object	Bean工厂使用的同步监视器	AbstractRefreshableApplicationContext
      id	             String	上下文使用的唯一Id，标识此ApplicationContext	AbstractApplicationContext
      parent	ApplicationContext	父级ApplicationContext	AbstractApplicationContext
      beanFactoryPostProcessors	List<BeanFactoryPostProcessor>	存储BeanFactoryPostProcessor接口，Spring提供的一个扩展点	AbstractApplicationContext
      startupShutdownMonitor	Object	refresh方法和destory方法公用的一个监视器，避免两个方法同时执行	AbstractApplicationContext
      shutdownHook	Thread	Spring提供的一个钩子，JVM停止执行时会运行Thread里面的方法	AbstractApplicationContext
      resourcePatternResolver	ResourcePatternResolver	上下文使用的资源格式解析器	AbstractApplicationContext
      lifecycleProcessor	LifecycleProcessor	用于管理Bean生命周期的生命周期处理器接口	AbstractApplicationContext
      messageSource	MessageSource	用于实现国际化的一个接口AbstractApplicationContext
      applicationEventMulticaster	ApplicationEventMulticaster	Spring提供的事件管理机制中的事件多播器接口	AbstractApplicationContext
      applicationListeners	Set<ApplicationListener>	Spring提供的事件管理机制中的应用监听器	AbstractApplicationContext
###ClassPathXmlApplicationContext构造函数
	public ClassPathXmlApplicationContext(String configLocation) throws BeansException {
     this(new String[] {configLocation}, true, null);
     }

	public ClassPathXmlApplicationContext(String[] configLocations, boolean refresh, ApplicationContext parent)
        throws BeansException {

    super(parent);//设置父集ApplicationContent ，这里是null

    setConfigLocations(configLocations);
    if (refresh) {
        refresh();
    }
    }


   setConfigLocations(configLocations);

  （1）将指定的Spring配置文件的路径存储到本地

（2）解析Spring配置文件路径中的${PlaceHolder}占位符，替换为系统变量中PlaceHolder对应的Value值，System本身就自带一些系统变量比如class.path、os.name、user.dir等，也可以通过System.setProperty()方法设置自己需要的系统变量

　3、refresh()

　　　　这个就是整个Spring Bean加载的核心了，它是ClassPathXmlApplicationContext的父类AbstractApplicationContext的一个方法，顾名思义，用于刷新整个Spring上下文信息，定义了整个Spring上下文加载的流程。


### refresh方法


    public void refresh() throws BeansException, IllegalStateException {
        synchronized (this.startupShutdownMonitor) {
            // Prepare this context for refreshing.
            prepareRefresh();

            // Tell the subclass to refresh the internal bean factory.
            ConfigurableListableBeanFactory beanFactory = obtainFreshBeanFactory();

            // Prepare the bean factory for use in this context.
            prepareBeanFactory(beanFactory);

            try {
                // Allows post-processing of the bean factory in context subclasses.
                postProcessBeanFactory(beanFactory);

                // Invoke factory processors registered as beans in the context.
                invokeBeanFactoryPostProcessors(beanFactory);

                // Register bean processors that intercept bean creation.
                registerBeanPostProcessors(beanFactory);

                // Initialize message source for this context.
                initMessageSource();

                // Initialize event multicaster for this context.
                initApplicationEventMulticaster();

                // Initialize other special beans in specific context subclasses.
                onRefresh();

                // Check for listener beans and register them.
                registerListeners();

                // Instantiate all remaining (non-lazy-init) singletons.
                finishBeanFactoryInitialization(beanFactory);

                // Last step: publish corresponding event.
                finishRefresh();
            }

            catch (BeansException ex) {
                // Destroy already created singletons to avoid dangling resources.
                destroyBeans();

                // Reset 'active' flag.
                cancelRefresh(ex);

                // Propagate exception to caller.
                throw ex;
            }
        }
    }

每个子方法的功能之后一点一点再分析，首先refresh()方法有几点是值得我们学习的：

　　1、方法是加锁的，这么做的原因是避免多线程同时刷新Spring上下文

　　2、尽管加锁可以看到是针对整个方法体的，但是没有在方法前加synchronized关键字，而使用了对象锁startUpShutdownMonitor，这样做有两个好处：

　　　　（1）refresh()方法和close()方法都使用了startUpShutdownMonitor对象锁加锁，这就保证了在调用refresh()方法的时候无法调用close()方法，反之亦然，避免了冲突

　　　　（2）另外一个好处不在这个方法中体现，但是提一下，使用对象锁可以减小了同步的范围，只对不能并发的代码块进行加锁，提高了整体代码运行的效率

　　3、方法里面使用了每个子方法定义了整个refresh()方法的流程，使得整个方法流程清晰易懂。

主要方法有两个：

### prepareRefresh();

1、设置一下刷新Spring上下文的开始时间

2、将active标识位设置为true

另外可以注意一下12行这句日志，这句日志打印了真正加载Spring上下文的Java类。
### prepareBeanFactory(beanFactory);		


	/**
	 * Tell the subclass to refresh the internal bean factory.
	 * @return the fresh BeanFactory instance
	 * @see #refreshBeanFactory()
	 * @see #getBeanFactory()
	 */
	protected ConfigurableListableBeanFactory obtainFreshBeanFactory() {
		refreshBeanFactory();
		ConfigurableListableBeanFactory beanFactory = getBeanFactory();
		if (logger.isDebugEnabled()) {
			logger.debug("Bean factory for " + getDisplayName() + ": " + beanFactory);
		}
		return beanFactory;
	}



	/**
	 * This implementation performs an actual refresh of this context's underlying
	 * bean factory, shutting down the previous bean factory (if any) and
	 * initializing a fresh bean factory for the next phase of the context's lifecycle.
	 */
	@Override
	protected final void refreshBeanFactory() throws BeansException {
		if (hasBeanFactory()) {
			destroyBeans();
			closeBeanFactory();
		}
		try {
			DefaultListableBeanFactory beanFactory = createBeanFactory();
			beanFactory.setSerializationId(getId());
			customizeBeanFactory(beanFactory);
			loadBeanDefinitions(beanFactory);
			synchronized (this.beanFactoryMonitor) {
				this.beanFactory = beanFactory;
			}
		}
		catch (IOException ex) {
			throw new ApplicationContextException("I/O error parsing bean definition source for " + getDisplayName(), ex);
		}
	}

核心createBeanFactory

这行点出了DefaultListableBeanFactory这个类，这个类是构造Bean的核心类，这个类的功能会在下一篇文章中详细解读，首先给出DefaultListableBeanFactory的继承关系图：

![](https://i.imgur.com/mMuM5VF.png)

AbstractAutowireCapableBeanFactory这个类的继承层次比较深，版面有限，就没有继续画下去了，本图基本上清楚地展示了DefaultListableBeanFactory的层次结构。

为了更清晰地说明DefaultListableBeanFactory的作用，列举一下DefaultListableBeanFactory中存储的一些重要对象及对象中的内容，DefaultListableBeanFactory基本就是操作这些对象，以表格形式说明：

![](https://i.imgur.com/d0HJVV0.png)