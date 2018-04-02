---
title: 【java基础】自动装箱和拆箱
date: 2015-8-14 16:42:20
tags:  自动装箱和拆箱
reward: true
---

# 自动装箱和拆箱
Java为每种基本数据类型都提供了对应的包装器类型。举个例子：

	public class TestMain
	{
    public static void main(String[] args)
    {
        Integer i = 10;
    }
	}
这个过程中会自动根据数值创建对应的Integer对象，这就是自动装箱。再看另外一段代码：

	public class TestMain
	{
    public static void main(String[] args)
    {
        Integer integer = 10;
        int i = integer;
    }
	}
这个过程中会根据包装器类型自动将数据转换为基本类型，这就是自动拆箱。

至于自动装箱和自动拆箱的原理也很简单。通过命令行程序，进入CLASSPATH（也就是bin目录下.class文件所在的路径），javap反编译查看一下生成的字节码：

反编译出来的内容有很多，我们只关注重点部分：

	 1   public static void main(java.lang.String[]);
	 2     flags: ACC_PUBLIC, ACC_STATIC
	 3     Code:
	 4       stack=1, locals=3, args_size=1
	 5          0: iconst_1
	 6          1: invokestatic  #16                 // Method java/lang/Integer.valueO
	 7 f:(I)Ljava/lang/Integer;
	 8          4: astore_1
	 9          5: aload_1
	10          6: invokevirtual #22                 // Method java/lang/Integer.intVal
	11 ue:()I
	12          9: istore_2
	13         10: return
看到在自动装箱的时候，也就是第6行，Java虚拟机会自动调用Integer的valueOf方法；在自动拆箱的时候，也就是第10行，Java虚拟机会自动调用Integer的intValue方法。这就是自动拆箱和自动装箱的原理。

小心空指针异常

有这么一段代码：

	public static void main(String[] args) throws Exception
	{
    Object obj = getObj(null);
    int i = (Integer)obj;
	}
    
	public static Object getObj(Object obj)
	{
    return obj;
	}
如果运行的话：

	Exception in thread "main" java.lang.NullPointerException
    at main.Test7.main(Test7.java:8)
这种使用场景很常见，我们把一个int数值放在session或者request中，取出来的时候就是一个类似上面的场景了。所以，小心自动拆箱时候的空指针异常。

看两段代码，第一段代码为：

	public class TestMain
	{
    public static void main(String[] args)
    {
        Integer i1 = 100;
        Integer i2 = 100;
        Integer i3 = 200;
        Integer i4 = 200;
        
        System.out.println(i1 == i2);
        System.out.println(i3 == i4);
    }
	}
	运行结果为：
	
	true
	false

第二段代码为：

	public class TestMain
	{
    public static void main(String[] args)
    {
        Double d1 = 100.0;
        Double d2 = 100.0;
        Double d3 = 200.0;
        Double d4 = 200.0;
        
        System.out.println(d1 == d2);
        System.out.println(d3 == d4);
    }
	}

	运行结果为：
	
	false
	false
产生这样的结果的原因是：Byte、Short、Integer、Long、Char这几个装箱类的valueOf()方法是以128位分界线做了缓存的，假如是128以下且-128以上的值是会取缓存里面的引用的，以Integer为例，其valueOf(int i)的源代码为：

	public static Integer valueOf(int i) {
    final int offset = 128;
    if (i >= -128 && i <= 127) { // must cache 
        return IntegerCache.cache[i + offset];
    }
        return new Integer(i);
    }
而Float、Double则不会，原因也很简单，因为byte、Short、integer、long、char在某个范围内的整数个数是有限的，但是float、double这两个浮点数却不是