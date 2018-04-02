---
title: 【java基础】理解java
date: 2015-7-14 16:42:20
tags:  理解java
reward: true
---

# 理解java
1、字符串不变性

下面这张图展示了这段代码做了什么


	String s = "abcd";
	s = s.concat("ef");
![](https://i.imgur.com/gE5s3Xp.jpg)

2.equals()方法、hashCode()方法的区别

HashCode被设计用来提高性能。equals()方法与hashCode()方法的区别在于：

如果两个对象相等(equal)，那么他们一定有相同的哈希值。
如果两个对象的哈希值相同，但他们未必相等(equal)。
![](https://i.imgur.com/dTnskMv.jpg)
3.Java异常类的层次结构

图中红色部分为受检查异常。它们必须被捕获，或者在函数中声明为抛出该异常。
![](https://i.imgur.com/0S0UM1r.jpg)
4.集合类的层次结构

注意Collections和Collection的区别。（Collections包含有各种有关集合操作的静态多态方法）
![](https://i.imgur.com/fbv3Mys.jpg)

5.Java同步

Java同步机制可通过类比建筑物来阐明。
![](https://i.imgur.com/OeKjKjw.jpg)

6.别名

别名意味着有多个变量指向同一可被更新的内存块，这些别名分别是不同的对象类型。

![](https://i.imgur.com/U51yrMV.jpg)

7.堆和栈

图解表明了方法和对象在运行时内存中的位置。

![](https://i.imgur.com/vU5OtcA.png)

8.Java虚拟机运行时数据区域

图解展示了整个虚拟机运行时数据区域的情况。
![](https://i.imgur.com/E6E3dPW.jpg)