---
title: 【java基础】异常原理
date: 2015-7-15 16:42:20
tags:  java异常原理
reward: true
---

# 异常概述

异常是程序中的一些错误，但并不是所有的错误都是异常，并且错误有时候是可以避免的。

比如说，你的代码少了一个分号，那么运行出来结果是提示是错误java.lang.Error；如果你用System.out.println(11/0)，那么你是因为你用0做了除数，会抛出java.lang.ArithmeticException的异常。

异常发生的原因有很多，通常包含以下几大类：

用户输入了非法数据。
要打开的文件不存在。
网络通信时连接中断，或者JVM内存溢出。
这些异常有的是因为用户错误引起，有的是程序错误引起的，还有其它一些是因为物理错误引起的。-
<!--more-->

要理解Java异常处理是如何工作的，你需要掌握以下三种类型的异常：
检查性异常：最具代表的检查性异常是用户错误或问题引起的异常，这是程序员无法预见的。例如要打开一个不存在文件时，一个异常就发生了，这些异常在编译时不能被简单地忽略。
运行时异常： 运行时异常是可能被程序员避免的异常。与检查性异常相反，运行时异常可以在编译时被忽略。
错误： 错误不是异常，而是脱离程序员控制的问题。错误在代码中通常被忽略。例如，当栈溢出时，一个错误就发生了，它们在编译也检查不到的。
异常指不期而至的各种状况，如：文件找不到、网络连接失败、除0操作、非法参数等。异常是一个事件，它发生在程序运行期间，干扰了正常的指令流程。
Java语言在设计的当初就考虑到这些问题，提出异常处理的框架的方案，所有的异常都可以用一个异常类来表示，不同类型的异常对应不同的子类异常（目前我们所说的异常包括错误概念），定义异常处理的规范，在JDK1.4版本以后增加了异常链机制，从而便于跟踪异常。
Java异常是一个描述在代码段中发生异常的对象，当发生异常情况时，一个代表该异常的对象被创建并且在导致该异常的方法中被抛出，而该方法可以选择自己处理异常或者传递该异常。

### 异常体系

java.lang.Throwable作为所有异常的超类

异常答题分为两大类：错误error和Exception

Java异常层次结构图如下图所示：

![](https://i.imgur.com/SRF7B1r.png)

    error：不希望程序捕获或者是程序无法处理的异常
    Exception：又分为运行时异常(RuntimeException)和非运行时异常。
    Java异常又可以分为不受检查异常（Unchecked Exception）和检查异常（Checked Exception

  检查异常：
  除了RuntimeException及其子类以外，其他的Exception类及其子类都属于检查异常，当程序中可能出现这类异常，要么使用try-catch语句进行捕获，要么用throws子句抛出，否则编译无法通过。

  不受检查异常：包括RuntimeException及其子类和Error。
  不受检查异常为编译器不要求强制处理的异常，检查异常则是编译器要求必须处置的异常。

#Java 异常的处理机制

 抛出异常：要理解抛出异常，首先要明白什么是异常情形（exception condition），它是指阻止当前方法或作用域继续执行的问题。其次把异常情形和普通问题相区分，普通问题是指在当前环境下能得到足够的信息，总能处理这个错误。对于异常情形，已经无法继续下去了，因为在当前环境下无法获得必要的信息来解决问题，你所能做的就是从当前环境中跳出，并把问题提交给上一级环境，这就是抛出异常时所发生的事情。抛出异常后，会有几件事随之发生。首先，是像创建普通的java对象一样将使用new在堆上创建一个异常对象；然后，当前的执行路径（已经无法继续下去了）被终止，并且从当前环境中弹出对异常对象的引用。此时，异常处理机制接管程序，并开始寻找一个恰当的地方继续执行程序，这个恰当的地方就是异常处理程序或者异常处理器，它的任务是将程序从错误状态中恢复，以使程序要么换一种方式运行，要么继续运行下去。
举个简单的例子，假使我们创建了一个学生对象Student的一个引用stu,在调用的时候可能还没有初始化。所以在使用这个对象引用调用其他方法之前，要先对它进行检查，可以创建一个代表错误信息的对象，并且将它从当前环境中抛出，这样就把错误信息传播到更大的环境中。
	if(stu == null){
    throw new NullPointerException();
	}
这就抛出了异常，它将在其他的地方得到执行或者处理，具体是哪个地方后面将很快介绍，代码中出现的 throw 是一个关键字，暂时先不做过多讲解，后面会详细讲解。

捕获异常：在方法抛出异常之后，运行时系统将转为寻找合适的异常处理器（exception handler）。潜在的异常处理器是异常发生时依次存留在调用栈中的方法的集合。当异常处理器所能处理的异常类型与方法抛出的异常类型相符时，即为合适的异常处理器。运行时系统从发生异常的方法开始，依次回查调用栈中的方法，直至找到含有合适异常处理器的方法并执行。当运行时系统遍历调用栈而未找到合适的异常处理器，则运行时系统终止。同时，意味着Java程序的终止。

	提示

	对于运行时异常、错误和检查异常，Java技术所要求的异常处理方式有所不同。
	
	由于运行时异常及其子类的不可查性，为了更合理、更容易地实现应用程序，Java规定，运行时异常将由Java运行时系统自动抛出，允许应用程序忽略运行时异常。
	
	对于方法运行中可能出现的Error，当运行方法不欲捕捉时，Java允许该方法不做任何抛出声明。因为，大多数Error异常属于永远不能被允许发生的状况，也属于合理的应用程序不该捕捉的异常。
	
	对于所有的检查异常，Java规定：一个方法必须捕捉，或者声明抛出方法之外。也就是说，当一个方法选择不捕捉检查异常时，它必须声明将抛出异常。

Java异常处理涉及到五个关键字，分别是：try、catch、finally、throw、throws。下面将骤一介绍，通过认识这五个关键字，掌握基本异常处理知识。

　　• try        -- 用于监听。将要被监听的代码(可能抛出异常的代码)放在try语句块之内，当try语句块内发生异常时，异常就被抛出。

　　• catch   -- 用于捕获异常。catch用来捕获try语句块中发生的异常。

　　• finally  -- finally语句块总是会被执行。它主要用于回收在try块里打开的物力资源(如数据库连接、网络连接和磁盘文件)。只有finally块，执行完成之后，才会回来执行try或者catch块中的return或者throw语句，如果finally中使用了return或者throw等终止方法的语句，则就不会跳回执行，直接停止。

　　• throw   -- 用于抛出异常。

　　• throws -- 用在方法签名中，用于声明该方法可能抛出的异常。

#异常处理的基本语法

	try{
	    //code that might generate exceptions    
	}catch(Exception e){
	    //the code of handling exception1
	}catch(Exception e){
	    //the code of handling exception2
	}

要明白异常捕获，还要理解监控区域（guarded region）的概念。它是一段可能产生异常的代码，并且后面跟着处理这些异常的代码。

因而可知，上述try-catch所描述的即是监控区域，关键词try后的一对大括号将一块可能发生异常的代码包起来，即为监控区域。Java方法在运行过程中发生了异常，则创建异常对象。将异常抛出监控区域之外，由Java运行时系统负责寻找匹配的catch子句来捕获异常。若有一个catch语句匹配到了，则执行该catch块中的异常处理代码，就不再尝试匹配别的catch块了。

匹配的原则是：如果抛出的异常对象属于catch子句的异常类，或者属于该异常类的子类，则认为生成的异常对象与catch块捕获的异常类型相匹配。

	public class TestException {  
    public static void main(String[] args) {  
        int a = 1;  
        int b = 0;  
        try { // try监控区域               
            if (b == 0) throw new ArithmeticException(); // 通过throw语句抛出异常  
            System.out.println("a/b的值是：" + a / b);  
            System.out.println("this will not be printed!");
        }  
        catch (ArithmeticException e) { // catch捕捉异常  
            System.out.println("程序出现异常，变量b不能为0！");  
        }  
        System.out.println("程序正常结束。");  
    }  
	}  

运行结果：

D:\java>java TestException
 
程序出现异常，变量b不能为0！

程序正常结束。
显示一个异常的描述，Throwable重载了toString()方法（由Object定义），所以它将返回一个包含异常描述的字符串。例如，将前面的catch块重写成：

catch (ArithmeticException e) { // catch捕捉异常  
    System.out.println("程序出现异常"+e);  
} 
结果：

D:\java>java TestException

程序出现异常java.lang.ArithmeticException

程序正常结束。
根据前面讲述的，算术异常属于运行时异常，因而实际上该异常不需要程序抛出，运行时系统自动抛出，将例子改为如下：

	public class TestException {  
    public static void main(String[] args) {  
        int a = 1;  
        int b = 0;    
        System.out.println("a/b的值是：" + a / b);
        System.out.println("this will not be printed!");
    }  
	}  
	结果：

	D:\java>java TestException

	Exception in thread "main" java.lang.ArithmeticException: / by zero
    at TestException.main(TestException.java:7)
使用多重的catch语句：很多情况下，由单个的代码段可能引起多个异常。处理这种情况，我们需要定义两个或者更多的catch子句，每个子句捕获一种类型的异常，当异常被引发时，每个catch子句被依次检查，第一个匹配异常类型的子句执行，当一个catch子句执行以后，其他的子句将被旁路。

编写多重catch语句块注意事项：

　　顺序问题：先小后大，即先子类后父类
![](https://i.imgur.com/MADzjkZ.jpg)

Java通过异常类描述异常类型。对于有多个catch子句的异常程序而言，应该尽量将捕获底层异常类的catch子句放在前面，同时尽量将捕获相对高层的异常类的catch子句放在后面。否则，捕获底层异常类的catch子句将可能会被屏蔽。

RuntimeException异常类包括运行时各种常见的异常，ArithmeticException类和ArrayIndexOutOfBoundsException类都是它的子类。因此，RuntimeException异常类的catch子句应该放在最后面，否则可能会屏蔽其后的特定异常处理或引起编译错误。

嵌套try语句：try语句可以被嵌套。也就是说，一个try语句可以在另一个try块的内部。每次进入try语句，异常的前后关系都会被推入堆栈。如果一个内部的try语句不含特殊异常的catch处理程序，堆栈将弹出，下一个try语句的catch处理程序将检查是否与之匹配。这个过程将继续直到一个catch语句被匹配成功，或者是直到所有的嵌套try语句被检查完毕。如果没有catch语句匹配，Java运行时系统将处理这个异常。

	class NestTry{
    public static void main(String[] args){
        try{
            int a = args.length;
            int b = 42 / a;
            System.out.println("a = "+ a);
            try{
                if(a == 1){
                a = a/(a-a);
                }
                if(a == 2){
                    int c[] = {1};
                    c[42] =99;
                }
            }catch(ArrayIndexOutOfBoundsException e){
                System.out.println("ArrayIndexOutOfBounds :"+e);
            }    
        }catch(ArithmeticException e){
            System.out.println("Divide by 0"+ e);
        }
    }
	}


正如程序中所显示的，该程序在一个try块中嵌套了另一个try块。程序工作如下：当你在没有命令行参数的情况下执行该程序，外面的try块将产生一个被0除的异常。程序在有一个命令行参数条件下执行，由嵌套的try块产生一个被0除的异常，由于内部的catch块不匹配这个异常，它将把异常传给外部的try块，在外部异常被处理。如果你在具有两个命令行参数的条件下执行该程序，将由内部try块产生一个数组边界异常。

	结果：
	
	D:\java>javac estTry.java
	
	D:\java>>java NestTry
	
	Divide by 0 java.lang.ArithmeticExceptio: / by zero
	
	D:\java>java NestTry one
	
	a = 1
	
	Divide by 0java.lang.ArithmeticException: / by zero
	
	D:\java>java NestTry one two
	
	a = 2

	ArrayIndexOutOfBounds :java.lang.ArrayIndexOutOfBoundsException: 42
注意：当有方法调用时，try语句的嵌套可以很隐蔽的发生。例如，我们可以将对方法的调用放在一个try块中。在该方法的内部，有另一个try语句。在这种情况下，方法内部的try仍然是嵌套在外部调用该方法的try块中的。下面我们将对上述例子进行修改，嵌套的try块移到方法nesttry()的内部：

	class NestTry{
    static void nesttry(int a){
        try{
            if(a == 1){
                a = a/(a-a);
            }
            if(a == 2){
                int c[] = {1};
                c[42] =99;
            }
        }catch(ArrayIndexOutOfBoundsException e){
            System.out.println("ArrayIndexOutOfBounds :"+e);
        }    
    }
    public static void main(String[] args){
        try{
            int a = args.length;
            int b = 42 / a;
            System.out.println("a = "+ a);
            nesttry(a);
        }catch(ArithmeticException e){
            System.out.println("Divide by 0"+ e);
        }
    }
	}
结果输出与前面例子一致：

	D:\java>javac NestTry.java
	
	D:\java>java NestTry
	
	Divide by 0java.lang.ArithmeticException: / by zero
	
	D:\java>java NestTry one
	
	a = 1
	
	Divide by 0java.lang.ArithmeticException: / by zero
	
	D:\java>java NestTry one two
	
	a = 2
	
	ArrayIndexOutOfBounds :java.lang.ArrayIndexOutOfBoundsException: 42

#2. throw

到目前为止，我们只是获取了被Java运行时系统引发的异常。然而，我们还可以用throw语句抛出明确的异常。Throw的语法形式如下：

throw ThrowableInstance;
这里的ThrowableInstance一定是Throwable类类型或者Throwable子类类型的一个对象。简单的数据类型，例如int，char,以及非Throwable类，例如String或Object，不能用作异常。有两种方法可以获取Throwable对象：在catch子句中使用参数或者使用new操作符创建。

程序执行完throw语句之后立即停止；throw后面的任何语句不被执行，最邻近的try块用来检查它是否含有一个与异常类型匹配的catch语句。如果发现了匹配的块，控制转向该语句；如果没有发现，次包围的try块来检查，以此类推。如果没有发现匹配的catch块，默认异常处理程序中断程序的执行并且打印堆栈轨迹。

例如：

	class TestThrow{
    static void proc(){
        try{
            throw new NullPointerException("demo");
        }catch(NullPointerException e){
            System.out.println("Caught inside proc");
            throw e;
        }
    }

    public static void main(String [] args){
        try{
            proc();
        }catch(NullPointerException e){
            System.out.println("Recaught: "+e);
        }
    }
	}

	结果：
	
	D:\java>java TestThrow
	
	Caught inside proc

	Recaught: java.lang.NullPointerException: demo
该程序两次处理相同的错误，首先，main()方法设立了一个异常关系然后调用proc()。proc()方法设立了另一个异常处理关系并且立即抛出一个NullPointerException实例，NullPointerException在main()中被再次捕获。

该程序阐述了怎样创建Java的标准异常对象，特别注意这一行：

1
throw new NullPointerException("demo");
此处new用来构造一个NullPointerException实例，所有的Java内置的运行时异常有两个构造方法：一个没有参数，一个带有一个字符串参数。当用第二种形式时，参数指定描述异常的字符串。如果对象用作print()或者println()的参数时，该字符串被显示。这同样可以通过调用getMessage()来实现，getMessage()是由Throwable定义的。


#3.throws

如果一个方法可以导致一个异常但不处理它，它必须指定这种行为以使方法的调用者可以保护它们自己而不发生异常。要做到这点，我们可以在方法声明中包含一个throws子句。一个throws子句列举了一个方法可能引发的所有异常类型。这对于除了Error或RuntimeException及它们子类以外类型的所有异常是必要的。一个方法可以引发的所有其他类型的异常必须在throws子句中声明，否则会导致编译错误。

下面是throws子句的方法声明的通用形式：

	public void info() throws Exception
	{
	   //body of method
	}
Exception 是该方法可能引发的所有的异常,也可以是异常列表，中间以逗号隔开。

例如：

	class TestThrows{
    static void throw1(){
        System.out.println("Inside throw1 . ");
        throw new IllegalAccessException("demo");
    }
    public static void main(String[] args){
        throw1();
    }
	}
上述例子中有两个地方存在错误，你能看出来吗？

该例子中存在两个错误，首先，throw1()方法不想处理所导致的异常，因而它必须声明throws子句来列举可能引发的异常即IllegalAccessException；其次，main()方法必须定义try/catch语句来捕获该异常。

正确例子如下：

	class TestThrows{
    static void throw1() throws IllegalAccessException {
        System.out.println("Inside throw1 . ");
        throw new IllegalAccessException("demo");
    }
    public static void main(String[] args){
        try {
            throw1();
        }catch(IllegalAccessException e ){
            System.out.println("Caught " + e);
        }
    }
	}
Throws抛出异常的规则：

如果是不受检查异常（unchecked exception），即Error、RuntimeException或它们的子类，那么可以不使用throws关键字来声明要抛出的异常，编译仍能顺利通过，但在运行时会被系统抛出。
必须声明方法可抛出的任何检查异常（checked exception）。即如果一个方法可能出现受可查异常，要么用try-catch语句捕获，要么用throws子句声明将它抛出，否则会导致编译错误
仅当抛出了异常，该方法的调用者才必须处理或者重新抛出该异常。当方法的调用者无力处理该异常的时候，应该继续抛出，而不是囫囵吞枣。
调用方法必须遵循任何可查异常的处理和声明规则。若覆盖一个方法，则不能声明与覆盖方法不同的异常。声明的任何异常必须是被覆盖方法所声明异常的同类或子类。

#4. finally

当异常发生时，通常方法的执行将做一个陡峭的非线性的转向，它甚至会过早的导致方法返回。例如，如果一个方法打开了一个文件并关闭，然后退出，你不希望关闭文件的代码被异常处理机制旁路。finally关键字为处理这种意外而设计。

finally创建的代码块在try/catch块完成之后另一个try/catch出现之前执行。finally块无论有没有异常抛出都会执行。如果抛出异常，即使没有catch子句匹配，finally也会执行。一个方法将从一个try/catch块返回到调用程序的任何时候，经过一个未捕获的异常或者是一个明确的返回语句，finally子句在方法返回之前仍将执行。这在关闭文件句柄和释放任何在方法开始时被分配的其他资源是很有用。

finally子句是可选项，可以有也可以无，但是每个try语句至少需要一个catch或者finally子句。

	class TestFinally{
    static void proc1(){
        try{
            System.out.println("inside proc1");
            throw new RuntimeException("demo");
        }finally{
            System.out.println("proc1's finally");
        }
    }
    static void proc2(){
        try{
            System.out.println("inside proc2");
            return ;
        } finally{
            System.out.println("proc2's finally");
        }
    } 
    static void proc3(){
        try{
            System.out.println("inside proc3");
        }finally{
            System.out.println("proc3's finally");
        }
    }
    public static void main(String [] args){
        try{
            proc1();
        }catch(Exception e){
            System.out.println("Exception caught");
        }
        proc2();
        proc3();
    }
	}
该例子中，proc1()抛出了异常中断了try，它的finally子句在退出时执行。proc2的try语句通过return语句返回，但在返回之前finally语句执行。在proc3()中try语句正常执行，没有错误，finally语句也被执行。

	输出结果：
	D:\java>java TestFinally
	
	inside proc1
	
	proc1's finally
	
	Exception caught
	
	inside proc2
	 
	proc2's finally
	 
	inside proc3
	 
	proc3's finally
	注：如果finally块与一个try联合使用，finally块将在try结束之前执行。

问题扩展（面试题）：

1、try{} 里有一个 return 语句，那么紧跟在这个 try 后的 finally{} 里的 code 会不会被执行，什么时候被执行，在 return 前还是后?

答案：会执行，在方法返回调用者前执行。

注意：在finally中改变返回值的做法是不好的，因为如果存在finally代码块，try中的return语句不会立马返回调用者，而是记录下返回值待finally代码块执行完毕之后再向调用者返回其值，然后如果在finally中修改了返回值，就会返回修改后的值。显然，在finally中返回或者修改返回值会对程序造成很大的困扰，C#中直接用编译错误的方式来阻止程序员干这种龌龊的事情，Java中也可以通过提升编译器的语法检查级别来产生警告或错误，Eclipse中可以在如图所示的地方进行设置，强烈建议将此项设置为编译错误。

2、Java语言如何进行异常处理，关键字：throws、throw、try、catch、finally分别如何使用？

答：Java通过面向对象的方法进行异常处理，把各种不同的异常进行分类，并提供了良好的接口。在Java中，每个异常都是一个对象，它是Throwable类或其子类的实例。当一个方法出现异常后便抛出一个异常对象，该对象中包含有异常信息，调用这个对象的方法可以捕获到这个异常并可以对其进行处理。Java的异常处理是通过5个关键词来实现的：try、catch、throw、throws和finally。一般情况下是用try来执行一段程序，如果系统会抛出（throw）一个异常对象，可以通过它的类型来捕获（catch）它，或通过总是执行代码块（finally）来处理；try用来指定一块预防所有异常的程序；catch子句紧跟在try块后面，用来指定你想要捕获的异常的类型；throw语句用来明确地抛出一个异常；throws用来声明一个方法可能抛出的各种异常（当然声明异常时允许无病呻吟）；finally为确保一段代码不管发生什么异常状况都要被执行；try语句可以嵌套，每当遇到一个try语句，异常的结构就会被放入异常栈中，直到所有的try语句都完成。如果下一级的try语句没有对某种异常进行处理，异常栈就会执行出栈操作，直到遇到有处理这种异常的try语句或者最终将异常抛给JVM。

3、运行时异常与受检异常有何异同？ 

答：异常表示程序运行过程中可能出现的非正常状态，运行时异常表示虚拟机的通常操作中可能遇到的异常，是一种常见运行错误，只要程序设计得没有问题通常就不会发生。受检异常跟程序运行的上下文环境有关，即使程序设计无误，仍然可能因使用的问题而引发。Java编译器要求方法必须声明抛出可能发生的受检异常，但是并不要求必须声明抛出未被捕获的运行时异常。异常和继承一样，是面向对象程序设计中经常被滥用的东西，在Effective Java中对异常的使用给出了以下指导原则： 
- 不要将异常处理用于正常的控制流（设计良好的API不应该强迫它的调用者为了正常的控制流而使用异常） 
- 对可以恢复的情况使用受检异常，对编程错误使用运行时异常 
- 避免不必要的使用受检异常（可以通过一些状态检测手段来避免异常的发生） 
- 优先使用标准的异常 
- 每个方法抛出的异常都要有文档 
- 保持异常的原子性 
- 不要在catch中忽略掉捕获到的异常

4、列出一些你常见的运行时异常？ 

答： 
- ArithmeticException（算术异常） 
- ClassCastException （类转换异常） 
- IllegalArgumentException （非法参数异常） 
- IndexOutOfBoundsException （下标越界异常） 
- NullPointerException （空指针异常） 
- SecurityException （安全异常）

![](https://i.imgur.com/uA8RSlE.jpg)

总结：

![](https://i.imgur.com/JOx0lRa.jpg)

![](https://i.imgur.com/N1ghEgP.jpg)