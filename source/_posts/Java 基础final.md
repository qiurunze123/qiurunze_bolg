---
title: java基础（final使用）
date: 2017-7-17 10：53
tags:  java基础（final使用）
reward: true
---

谈到final关键字，想必很多人都不陌生，在使用匿名内部类的时候可能会经常用到final关键字。另外，Java中的String类就是一个final类，那么今天我们就来了解final这个关键字的用法。
<!--more-->

一、final关键字的基本用法

在Java中，final关键字可以用来修饰类、方法和变量（包括成员变量和局部变量）。下面就从这三个方面来了解一下final关键字的基本用法。

###1、修饰类

　  当用final修饰一个类时，表明这个类不能被继承。也就是说，如果一个类你永远不会让他被继承，就可以用final进行修饰。final类中的成员变量可以根据需要设为final，但是要注意final类中的所有成员方法都会被隐式地指定为final方法。
	在使用final修饰类的时候，要注意谨慎选择，除非这个类真的在以后不会用来继承或者出于安全的考虑，尽量不要将类设计为final类。

###2、修饰方法

   final修饰的方法表示此方法已经是“最后的、最终的”含义，亦即此方法不能被重写（可以重载多个final修饰的方法）。此处需要注意的一点是：因为重写的前提是子类可以从父类中继承此方法，如果父类中final修饰的方法同时访问控制权限为private，将会导致子类中不能直接继承到此方法，因此，此时可以在子类中定义相同的方法名和参数，此时不再产生重写与final的矛盾，而是在子类中重新定义了新的方法。（注：类的private方法会隐式地被指定为final方法。）


	public class B extends A {

    public static void main(String[] args) {

    }

    public void getName() {
        
    }
	}

	class A {

    /**
   	  * 因为private修饰，子类中不能继承到此方法，因此，子类中的getName方法是重新定义的、
     * 属于子类本身的方法，编译正常
     */
    private final void getName() {
        
    }

    /* 因为pblic修饰，子类可以继承到此方法，导致重写了父类的final方法，编译出错
    public final void getName() {
    
    }
    */
	}

###3、修饰变量
      修饰变量是final用得最多的地方，也是本文接下来要重点阐述的内容。

      final成员变量表示常量，只能被赋值一次，赋值后值不再改变。

　　当final修饰一个基本数据类型时，表示该基本数据类型的值一旦在初始化后便不能发生变化；如果final修饰一个引用类型时，则在对其初始化之后便不能再让其指向其他对象了，但该引用所指向的对象的内容是可以发生变化的。本质上是一回事，因为引用的值是一个地址，final要求值，即地址的值不发生变化。

　　final修饰一个成员变量（属性），必须要显示初始化。这里有两种初始化方式，一种是在变量声明的时候初始化；第二种方法是在声明变量的时候不赋初值，但是要在这个变量所在的类的所有的构造函数中对这个变量赋初值。

　　当函数的参数类型声明为final时，说明该参数是只读型的。即你可以读取使用该参数，但是无法改变该参数的值。



二、深入理解final关键字

在了解了final关键字的基本用法之后，这一节我们来看一下final关键字容易混淆的地方。

1、类的final变量和普通变量有什么区别？

     当用final作用于类的成员变量时，成员变量（注意是类的成员变量，局部变量只需要保证在使用之前被初始化赋值即可）必须在定义时或者构造器中进行初始化赋值，而且final变量一旦被初始化赋值之后，就不能再被赋值了。

     那么final变量和普通变量到底有何区别呢？下面请看一个例子：

	public class Test { 
    public static void main(String[] args)  { 
        String a = "hello2";   
        final String b = "hello"; 
        String d = "hello"; 
        String c = b + 2;   
        String e = d + 2; 
        System.out.println((a == c)); 
        System.out.println((a == e)); 
    } 
	} 

	输出结果：true、false
   大家可以先想一下这道题的输出结果。为什么第一个比较结果为true，而第二个比较结果为fasle。这里面就是final变量和普通变量的区别了，当final变量是基本数据类型以及String类型时，如果在编译期间能知道它的确切值，则编译器会把它当做编译期常量使用。也就是说在用到该final变量的地方，相当于直接访问的这个常量，不需要在运行时确定。这种和C语言中的宏替换有点像。因此在上面的一段代码中，由于变量b被final修饰，因此会被当做编译器常量，所以在使用到b的地方会直接将变量b 替换为它的值。而对于变量d的访问却需要在运行时通过链接来进行。想必其中的区别大家应该明白了，不过要注意，只有在编译期间能确切知道final变量值的情况下，编译器才会进行这样的优化，比如下面的这段代码就不会进行优化：


	public class Test { 
    public static void main(String[] args)  { 
        String a = "hello2";   
        final String b = getHello(); 
        String c = b + 2;   
        System.out.println((a == c)); 
  
    } 
      
    public static String getHello() { 
        return "hello"; 
    } 
	} 

这段代码的输出结果为false。这里要注意一点就是：不要以为某些数据是final就可以在编译期知道其值，通过变量b我们就知道了，在这里是使用getHello()方法对其进行初始化，他要在运行期才能知道其值。

	2、被final修饰的引用变量指向的对象内容可变吗？

在上面提到被final修饰的引用变量一旦初始化赋值之后就不能再指向其他的对象，那么该引用变量指向的对象的内容可变吗？看下面这个例子：


	public class Test { 
    public static void main(String[] args)  { 
        final MyClass myClass = new MyClass(); 
        System.out.println(++myClass.i); 
  
    } 
	} 
  
	class MyClass { 
    public int i = 0; 
	} 

这段代码可以顺利编译通过并且有输出结果，输出结果为1。这说明引用变量被final修饰之后，虽然不能再指向其他对象，但是它指向的对象的内容是可变的。

###3、final参数的问题

     在实际应用中，我们除了可以用final修饰成员变量、成员方法、类，还可以修饰参数、若某个参数被final修饰了，则代表了该参数是不可改变的。如果在方法中我们修改了该参数，则编译器会提示你：The final local variable i cannot be assigned. It must be blank and not using a compound assignment。看下面的例子：


	public class TestFinal {
    
    public static void main(String[] args){
        TestFinal testFinal = new TestFinal();
        int i = 0;
        testFinal.changeValue(i);
        System.out.println(i);
        
    }
    
    public void changeValue(final int i){
        //final参数不可改变
        //i++;
        System.out.println(i);
    }
}

上面这段代码changeValue方法中的参数i用final修饰之后，就不能在方法中更改变量i的值了。值得注意的一点，方法changeValue和main方法中的变量i根本就不是一个变量，因为java参数传递采用的是值传递，对于基本类型的变量，相当于直接将变量进行了拷贝。所以即使没有final修饰的情况下，在方法内部改变了变量i的值也不会影响方法外的i。

再看下面这段代码：


	public class TestFinal {
    
    public static void main(String[] args){
        TestFinal testFinal = new TestFinal();
        StringBuffer buffer = new StringBuffer("hello");
        testFinal.changeValue(buffer);
        System.out.println(buffer);
        
    }
    
    public void changeValue(final StringBuffer buffer){
        //final修饰引用类型的参数，不能再让其指向其他对象，但是对其所指向的内容是可以更改的。
        //buffer = new StringBuffer("hi");
        buffer.append("world");
    }
}

运行这段代码就会发现输出结果为 helloworld。很显然，用final进行修饰虽不能再让buffer指向其他对象，但对于buffer指向的对象的内容是可以改变的。现在假设一种情况，如果把final去掉，结果又会怎样？看下面的代码：


	public class TestFinal {
    
    public static void main(String[] args){
        TestFinal testFinal = new TestFinal();
        StringBuffer buffer = new StringBuffer("hello");
        testFinal.changeValue(buffer);
        System.out.println(buffer);
        
    }
    
    public void changeValue(StringBuffer buffer){
        //buffer重新指向另一个对象
        buffer = new StringBuffer("hi");
        buffer.append("world");
        System.out.println(buffer);
    }
}

	运行结果：

	hiworld
	hello
从运行结果可以看出，将final去掉后，同时在changeValue中让buffer指向了其他对象，并不会影响到main方法中的buffer，原因在于java采用的是值传递，对于引用变量，传递的是引用的值，也就是说让实参和形参同时指向了同一个对象，因此让形参重新指向另一个对象对实参并没有任何影响