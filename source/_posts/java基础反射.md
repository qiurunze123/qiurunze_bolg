---
title: 【java】java反射方法与使用
date: 2015-7-14 16:42:20
tags:  java反射
reward: true
---

# java的反射方法

Java反射描述的是，在运行状态中：  1、对于任意一个类，都能够知道这个类的所有属性和方法  2、对于任意一个类，都能够调用它的任意一个属性和方法  之所以强调属性、方法，是因为属性、方法是开发者对于一个类最关注的两个部分。实际上通过反射，不仅仅可以获知类的属性、方法，还可以获知类的父类、接口、包等信息  至于反射的原理，不难，Java类加载机制一文中讲到了，一个类在加载的时候，会在内存中生成一个代表这个.class文件的java.lang.Class对象，.classs文件里面就包含了描述这个类的信息的一切内容。至于.class文件，是由Java编译器（注意是Java编译器，指的不仅仅是Javac）编译而来的，是编译原理的领域；

<!--more-->




### 本文用到的实体类

	package cn.com.sinosure.biz.claimtrace.indemnity;
	
	import java.io.Serializable;
	import java.util.List;
	
	public class Reflection implements Cloneable ,Serializable{

    private String str;

    private double d ;

    public boolean b ;

    public static short s ;

    public Reflection() {
    }

    public Reflection(String str) {
        this.str = str;
    }

    public Reflection(String str, double d, boolean b) {
        this.str = str;
        this.d = d;
        this.b = b;
    }

    private void privateMethod() {
    }

    public String publicMethod() {
        privateMethod();
        return null;
    }

    public String publicMethod(int i) {
        return null;
    }

    public String publicMethod(int i, double d, List<String> l) {
        return "Reflection.publicMethod(int i, double d), i = " + i + ", d = " + d;
    }

    public static int returnOne() {
        return 1;
    }

    public String toString() {
        return "str = " + str + ", d = " + d + ", b = " + b;
	    }
	}
### Class和ClassLoader

 ![](https://i.imgur.com/J9n48rj.png)

### Package
![](https://i.imgur.com/EBxLrIy.png)

### Field
![](https://i.imgur.com/9z9Js5K.png)
![](https://i.imgur.com/q3Cjgcf.png)

### Constructor
![](https://i.imgur.com/4x59389.png)

### Method
![](https://i.imgur.com/TdtTD8W.png)
![](https://i.imgur.com/SLG6gfQ.png)


#### Modifier枚举值列表

Field、Constructor、Method中都有getModifiers()方法，返回的是表示此对象的Java语言修饰符，详细看下每个修饰符对应的枚举值：

![](https://i.imgur.com/0nvUhyd.png)