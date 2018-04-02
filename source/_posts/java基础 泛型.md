---
title: 【java基础】java的泛型
date: 2015-8-14 16:42:20
tags:  java的泛型
reward: true
---

# java的泛型
泛型初探

在泛型（Generic type或Generics）出现之前，是这么写代码的：

	public static void main(String[] args)
	{
    List list = new ArrayList();
    list.add("123");
    list.add("456");
    
    System.out.println((String)list.get(0));
	}
<!--more-->
当然这是完全允许的，因为List里面的内容是Object类型的，自然任何对象类型都可以放入、都可以取出，但是这么写会有两个问题：

1、当一个对象放入集合时，集合不会记住此对象的类型，当再次从集合中取出此对象时，该对象的编译类型变成了Object

2、运行时需要人为地强制转换类型到具体目标，实际的程序绝不会这么简单，一个不小心就会出现java.lang.ClassCastException，即类型转换异常

所以，泛型出现之后，上面的代码就改成了大家都熟知的写法：

	public static void main(String[] args)
	{
    List<String> list = new ArrayList<String>();
    list.add("123");
    list.add("456");
    
    System.out.println(list.get(0));
	}
这就是泛型。泛型是对Java语言类型系统的一种扩展，有点类似于C++的模板，可以把类型参数看作是使用参数化类型时指定的类型的一个占位符。引入泛型，是对Java语言一个较大的功能增强，带来了很多的好处：

	1、类型安全。类型错误现在在编译期间就被捕获到了，而不是在运行时当作java.lang.ClassCastException展示出来，将类型检查从运行时挪到编译时有助于开发者更容易找到错误，并提高程序的可靠性
	
	2、消除了代码中许多的强制类型转换，增强了代码的可读性
	
	3、为较大的优化带来了可能

 

getClass()相同

看一段代码：

	public static void main(String[] args)
	{
    List<String> stringList = new ArrayList<String>();
    List<Integer> integerList = new ArrayList<Integer>();
    System.out.println(stringList.getClass() == integerList.getClass());
	}
	运行结果为：

	true
	这意味着，泛型是什么并不会对一个对象实例是什么类型的造成影响，所以，通过改变泛型的方式试图定义不同的重载方法也是不可以的：

 

 

尽量使用精确的类型定义泛型

尽量使用精确的类型定义泛型，除非必要，否则不要写一个接口或者父类上去：

	public static void main(String[] args)
	{
    List<Number> list = new ArrayList<Number>();
    list.add(4);
    list.add(2.2);
    for (Number number : list)
        System.out.println(number);
	}
就像这样，list中的是一个Number类型，往里面添加的是Integer与Double，这样导致get出来的元素也都是Number类型的，失去了子类扩展的功能。

如果要让子类变为Interger和Double也可以，(Integer)list.get(0)和(Double)list.get(1)强转就可以了，但是这样不就失去了泛型的意义了吗？所以，尽量用精确的类型去定义泛型。

使用类型通配符

	List<Object>不是List<String>的父类型，List<Integer>不是List<Number>的父类型，试图用以下方式赋值是不允许的：
	
	1 public static void main(String[] args)
	2 {
	3     List<Number> numberList = new ArrayList<Number>();
	4     List<Integer> integerList = new ArrayList<Integer>();
	5     numberList = integerList;
	6 }
第5行将报错"Type mismatch: cannot convert from List<Integer> to List<Number>"。有人可能觉得这样很不方便：我在一个方法里面只需要循环检索一个List，也不能利用多态放一个父类型进去，也不能重载，那怎么办呢？针对这个问题，Java给开发者提供了通配符"?"，看一下：


	public static void main(String[] args)
	{
	    List<String>  stringList = new ArrayList<String>();
	    List<Integer> integerList = new ArrayList<Integer>();
	        
	    printList(stringList);
	    printList(integerList);
	}
	    
	private static void printList(List<?> l)
	{
	    for (Object o : l)
	        System.out.println(o);
	}
<?>是类型通配符，表示是任何泛型的父类型，这样List<Object>、List<String>这些都可以传递进入printList方法中，注意这里的参数不能写成List<E>，这样就报错了，E未定义。当然<?>也可以不加，不过这样会有警告：如果传递一个List<E>给List，相当于传递一个只承诺将它当作List（原始类型）的方法，这将会破坏使用泛型的类型安全。

再注意一点，使用类型通配符，只能从中检索元素，不能添加元素。

泛型方法

	public static void main(String[] args)
	{
	    System.out.println(ifThenElse(false, "111", "222"));
	}
	    
	private static <T> T ifThenElse(boolean b, T first, T second)
	{
	    return b ? first : second;
	}
返回结果为：

222
这说明，方法也可以被泛型化，不管定义在其中的类是不是泛型化的。这意味着不用显式告诉编译器，想要T什么值：编译器只知道这些T都必须相同。

静态资源不认识泛型

接上一个话题，如果把<T>去掉，那么：

报错，T未定义。但是如果我们再把static去掉：
这并不会有任何问题。两相对比下，可以看出static方法并不认识泛型，所以我们要加上一个<T>，告诉static方法，后面的T是一个泛型。既然static方法不认识泛型，那我们看一下static变量是否认识泛型：
这证明了，static变量也不认识泛型，其实不仅仅是static方法、static变量，static块也不认识泛型，可以自己试一下。总结起来就是一句话：静态资源不认识泛型。
泛型约束

可以对泛型参数作约束，本来觉得应该有规律，后来发现没有，那就把自己研究的结论发一下，假设有一组类继承关系C继承自B，B继承自A：

	1、定义class的时候只能使用extends关键字且不能用通配符"?"

	public class TestMain<T extends B>
	{
	    public static void main(String[] args)
	    {
	        new TestMain<C>();
	    }
	}
	正确。

	TestMain类的泛型只能传B的子类，也就是C。new TestMain<A>()
	public class TestMain<? extends B>"、
	public class TestMain<T super B>"都是错误的写法

2、作为方法的参数，泛型可以使用"? extends B"或者"? super B"，前者表示实际类型只可以是B的子类，后者表示实际类型只可以是B的父类，以下两种写法都是正确的：

	public static void main(String[] args)
	{
	    print(new ArrayList<C>());
	}
	    
	public static void print(List<? extends B> list)
	{
	    
	}
	public static void main(String[] args)
	{
	    print(new ArrayList<A>());
	    print(new ArrayList<Object>());
	}

	public static void print(List<? super B> list)
	{
	        
	}
3、作为局部变量的参数，泛型可以使用"? extends B"或者"? super B"，不过前者好像没什么意义，后者表示只可以传以B为父类的对象，所以以下的写法是正确的：

	public static void main(String[] args)
	{
	    List<? super B> list = new ArrayList<B>();
	    list.add(new C());
	}
不要写"list.add(new A())"，JDK将会认为这是类型不匹配的。