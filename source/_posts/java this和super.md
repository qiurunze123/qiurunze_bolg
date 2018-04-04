---
title: 【java基础】java this和super
date: 2015-8-14 16:42:20
tags:  java this和super
reward: true
---

# java的this和super调用

###1.this

this是自身的一个对象，代表对象本身，可以理解为：指向对象本身的一个指针。

this的用法在java中大体可以分为3种：

<!--more-->


1.普通的直接引用

这种就不用讲了，this相当于是指向当前对象本身。

2.形参与成员名字重名，用this来区分：

	package com.demo;

	public class Person {
    
    private int age = 10;
    
    public Person(){
        System.out.println("初始化年龄："+age);
    }
    
    public int GetAge(int age){
        this.age = age;
        return this.age;
    }
	}
	package com.demo;

	public class Test1 {
    
    public static void main(String[] args) {
        Person Harry = new Person();
        System.out.println("Harry's age is "+Harry.GetAge(12));
    }
	}
运行结果：

	初始化年龄：10
	Harry's age is 12
3.引用构造函数

这个和super放在一起讲，见下面。

###2.super

super可以理解为是指向自己超（父）类对象的一个指针，而这个超类指的是离自己最近的一个父类。

super也有三种用法：

1.普通的直接引用

与this类似，super相当于是指向当前对象的父类，这样就可以用super.xxx来引用父类的成员。

2.子类中的成员变量或方法与父类中的成员变量或方法同名

	package com.demo;

	public class Country {

    String name;
    void value() {
       name = "China";
    }
	}
	package com.demo;

	public class City extends Country{

    String name;

    void value() {
        name = "Shanghai";
        super.value(); // 调用父类的方法
        System.out.println(name);
        System.out.println(super.name);
    }

    public static void main(String[] args) {
        City c = new City();
        c.value();
    }
	}
运行结果：

Shanghai
China
可以看到，这里既调用了父类的方法，也调用了父类的变量。若不调用父类方法value()，只调用父类变量name的话，则父类name值为默认值null。

3.引用构造函数

super（参数）：调用父类中的某一个构造函数（应该为构造函数中的第一条语句）。
this（参数）：调用本类中另一种形式的构造函数（应该为构造函数中的第一条语句）。
	package com.demo.test;
	
	public class Person {

    public static void prt(String s) {
        System.out.println(s);
    }

    Person() {
        prt("父类·无参数构造方法： " + "A Person.");
    }// 构造方法(1)

    Person(String name) {
        prt("父类·含一个参数的构造方法： " + "A person's name is " + name);
    }// 构造方法(2)
	}

	package com.demo.test;

	public class Chinese extends Person {

    Chinese() {
        super(); // 调用父类构造方法（1）
        prt("子类·调用父类“无参数构造方法“： " + "A chinese coder.");
    }

    Chinese(String name) {
        super(name);// 调用父类具有相同形参的构造方法（2）
        prt("子类·调用父类”含一个参数的构造方法“： " + "his name is " + name);
    }

    Chinese(String name, int age) {
        this(name);// 调用具有相同形参的构造方法（3）
        prt("子类：调用子类具有相同形参的构造方法：his age is " + age);
    }

    public static void main(String[] args) {
        Chinese cn = new Chinese();
        cn = new Chinese("codersai");
        cn = new Chinese("codersai", 18);
    }
	}
运行结果：

	父类·无参数构造方法： A Person.
	子类·调用父类“无参数构造方法“： A chinese coder.
	父类·含一个参数的构造方法： A person's name is codersai
	子类·调用父类”含一个参数的构造方法“： his name is codersai
	父类·含一个参数的构造方法： A person's name is codersai
	子类·调用父类”含一个参数的构造方法“： his name is codersai
	子类：调用子类具有相同形参的构造方法：his age is 18
从本例可以看到，可以用super和this分别调用父类的构造方法和本类中其他形式的构造方法。

例子中Chinese类第三种构造方法调用的是本类中第二种构造方法，而第二种构造方法是调用父类的，因此也要先调用父类的构造方法，再调用本类中第二种，最后是重写第三种构造方法。

三、super 和 this 的异同

	super（参数）：调用基类中的某一个构造函数（应该为构造函数中的第一条语句） 
	this（参数）：调用本类中另一种形成的构造函数（应该为构造函数中的第一条语句）
	super：它引用当前对象的直接父类中的成员（用来访问直接父类中被隐藏的父类中成员数据或函数，基类与派生类中有相同成员定义时如：super.变量名    super.成员函数据名（实参）
	this：它代表当前对象名（在程序中易产生二义性之处，应使用this来指明当前对象；如果函数的形参与类中的成员数据同名，这时需用this来指明成员变量名）
	调用super()必须写在子类构造方法的第一行，否则编译不通过。每个子类构造方法的第一条语句，都是隐含地调用super()，如果父类没有这种形式的构造函数，那么在编译的时候就会报错。
	super()和this()类似，区别是，super()从子类中调用父类的构造方法，this()在同一类内调用其它方法。
	super()和this()均需放在构造方法内第一行。
	尽管可以用this调用一个构造器，但却不能调用两个。
	this和super不能同时出现在一个构造函数里面，因为this必然会调用其它的构造函数，其它的构造函数必然也会有super语句的存在，所以在同一个构造函数里面有相同的语句，就失去了语句的意义，编译器也不会通过。
	this()和super()都指的是对象，所以，均不可以在static环境中使用。包括：static变量，static方法，static语句块。
	从本质上讲，this是一个指向本对象的指针， 然而super是一个Java关键字。
	一、this

      Java关键字this只能用于方法体内。当一个对象创建后，Java虚拟机（JVM）就会给这个对象分配一个引用自身的指针，这个指针的名字就是this。因此，this只能在类中的非静态方法中使用，静态方法和静态的代码块中绝对不能出现this。并且this只和特定的对象关联，而不和类关联，同一个类的不同对象有不同的this。下面给出一个使用this的综合实例，以便说明问题：

	package com.demo.test;
	
	public class Testthis {

    private int number;
    private String username;
    private String password;
    private int x = 100;

    public Testthis(int n) {
        number = n; // 这个还可以写为: this.number=n;
    }

    public Testthis(int i, String username, String password) {
        // 成员变量和参数同名,成员变量被屏蔽,用"this.成员变量"的方式访问成员变量.
        this.username = username;
        this.password = password;
    }

    // 默认不带参数的构造方法
    public Testthis() {
        this(0, "未知", "空"); // 通过this调用另一个构造方法
    }

    public Testthis(String name) {
        this(1, name, "空"); // 通过this调用另一个构造方法
    }

    public static void main(String args[]) {
        Testthis t1 = new Testthis();
        Testthis t2 = new Testthis("游客");
        t1.outinfo(t1);
        t2.outinfo(t2);
    }

    private void outinfo(Testthis t) {
        System.out.println("-----------");
        System.out.println(t.number);
        System.out.println(t.username);
        System.out.println(t.password);
        f(); // 这个可以写为: this.f();
    }

    private void f() {
        // 局部变量与成员变量同名,成员变量被屏蔽,用"this.成员变量"的方式访问成员变量.
        int x;
        x = this.x++;
        System.out.println(x);
        System.out.println(this.x);
    }

    // 返回当前实例的引用
    private Testthis getSelf() {
        return this;
    }
	}
运行结果如下：

0
未知
空
100
101
0
游客
空
100
101
	看着上面的例子，说明在什么情况下需要用到this：

       第一、通过this调用另一个构造方法，用法是this(参数列表)，这个仅仅在类的构造方法中，别的地方不能这么用。

       第二、函数参数或者函数中的局部变量和成员变量同名的情况下，成员变量被屏蔽，此时要访问成员变量则需要用“this.成员变量名”的方式来引用成员变量。当然，在没有同名的情况下，可以直接用成员变量的名字，而不用this，用了也不为错，呵呵。

       第三、在函数中，需要引用该函数所属类的当前对象的时候，直接用this。其实这些用法总结都是从对“this是指向对象本身的一个指针”这句话的更深入的理解而来的，死记不然容易忘记而且容易搞错，要理解！

2.super

      super关键字和this作用类似，使被屏蔽的成员变量或者成员方法变为可见，或者说用来引用被屏蔽的成员变量和成员方法。不过super是用在子类中，目的是访问直接父类中被屏蔽的成员，注意是直接父类（就是类之上最近的超类）。下面是一个综合运用super的例子，有两个类：一个Father类，一个Father类的子类Son，通过这两个类完全演示了super的用法，以下是代码：
	package com.demo.test;
	
	public class Father {

    public String v = "Father";
    public String x = "输出了Father类的public成员变量x!!!";

    public Father() {
        System.out.println("Father构造方法被调用!");
    }

    public Father(String v) {
        this.v = "Father类的带参数构造方法!运行了.";
    }

    public void outinfo() {
        System.out.println("Father的outinfo方法被调用");
    }

    public static void main(String[] args) {
        // TODO 自动生成方法存根
    }
	}
	package com.demo.test;
	
	public class Son extends Father {

    public String v = "Son";

    public Son() {
        super(); // 调用超类的构造方法,只能放到第一行.
        System.out.println("Son无参数构造方法被调用!");
        // super(); //错误的,必须放到构造方法体的最前面.
    }

    public Son(String str) {
        super(str);
        System.out.println("Son带参数构造方法被调用!");
    }

    // 覆盖了超类成员方法outinfo()
    public void outinfo() {
        System.out.println("Son的outinfo()方法被调用");
    }

    public void test() {

        String v = "哈哈哈哈!"; // 局部变量v覆盖了成员变量v和超类变量v

        System.out.println("------1-----");
        System.out.println(v); // 输出局部变量v
        System.out.println(this.v); // 输出(子类)成员变量v
        System.out.println(super.v); // 输出超类成员变量v

        System.out.println("------2-----");
        System.out.println(x); // 输出超类成员变量v,子类继承而来
        System.out.println(super.x); // 输出超类成员变量v

        System.out.println("------3-----");
        outinfo(); // 调用子类的outinfo()方法
        this.outinfo(); // 调用子类的outinfo()方法
        super.outinfo(); // 调用父类的outinfo()方法
    }

    public static void main(String[] args) {
        new Son().test();

    }
	}
运行结果：

Father构造方法被调用!
Son无参数构造方法被调用!
------1-----
哈哈哈哈!
Son
Father
------2-----
输出了Father类的public成员变量x!!!
输出了Father类的public成员变量x!!!
------3-----
Son的outinfo()方法被调用
Son的outinfo()方法被调用
Father的outinfo方法被调用
说明：此例子仅仅为了说明super的用法，实际在设计类的时候一般都尽可能私有(private)化。

通过上面的例子，下面总结一下super的用法：

      第一、在子类构造方法中要调用父类的构造方法，用“super(参数列表)”的方式调用，参数不是必须的。同时还要注意的一点是：“super(参数列表)”这条语句只能用在子类构造方法体中的第一行。

      第二、当子类方法中的局部变量或者子类的成员变量与父类成员变量同名时，也就是子类局部变量覆盖父类成员变量时，用“super.成员变量名”来引用父类成员变量。当然，如果父类的成员变量没有被覆盖，也可以用“super.成员变量名”来引用父类成员变量，不过这是不必要的。

      第三、当子类的成员方法覆盖了父类的成员方法时，也就是子类和父类有完全相同的方法定义（但方法体可以不同），此时，用“super.方法名(参数列表)”的方式访问父类的方法。

	this、super的用法也不过这些，只有理解了其中的原理，才不会跌入陷阱！