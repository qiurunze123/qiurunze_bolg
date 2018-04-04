---
title: 【java基础】java容器Arraylist详解
date: 2015-9-14 16:42:20
tags:  Arraylist详解
reward: true
---

# Arraylist详解


这个分类中，将会写写Java中的集合。集合是Java中非常重要而且基础的内容，因为任何数据必不可少的就是该数据是如何存储的，集合的作用就是以一定的方式组织、存储数据。这里写的集合，一部分是比较常见的、一部分是不常用但是我个人平时见到过的，一些比较相似的集合（比如HashMap和Hashtable）就只讲一个，突出它们之间的区别即可。

<!--more-->


1.ArrayList

ArrayList是最常见以及每个Java开发者最熟悉的集合类了，顾名思义，ArrayList就是一个以数组形式实现的集合，以一张表格来看一下ArrayList里面有哪些基本的元素：

	| 元素  |  作用 |
    | private transient Object[] elementData  |   ArrayList是基于数组的一个实现，elementData就是底层的数组   |
    | private int size;   | ArrayList里面元素的个数，这里要注意一下，size是按照调用add、remove方法的次数进行自增或者自减的，所以add了一个null进入ArrayList，size也会加1   |

arraylist 关注点

    | 关  注  点 | 结论 |
    | ArrayList是否允许空  |    允许  |
    | ArrayList是否允许重复数据  | 允许  |
  	| ArrayList是否有序  |  有序  |
  	| ArrayList是否线程安全  |  不安全 |


##添加元素

	有这么一段代码：

	public static void main(String[] args)
	{
    List<String> list = new ArrayList<String>();
    list.add("000");
    list.add("111");
	}
看下底层会做什么，进入add方法的源码来看一下：

	public boolean add(E e) {
	ensureCapacity(size + 1);  // Increments modCount!!
	elementData[size++] = e;
	return true;
	 }
先不去管第2行的ensureCapacity方法，这个方法是扩容用的，底层实际上在调用add方法的时候只是给elementData的某个位置添加了一个数据而已，用一张图表示的话是这样的：

![](https://i.imgur.com/oZLiMxt.png)

elementData中存储的应该是堆内存中元素的引用，而不是实际的元素，这么画给人一种感觉就是说elementData数组里面存放的就是实际的元素，这是不太严谨的。不过这么画主要是为了方便起见，只要知道这个问题就好了。

###扩容

我们看一下，构造ArrayList的时候，默认的底层数组大小是10：

	public ArrayList() {
	this(10);
	}
那么有一个问题来了，底层数组的大小不够了怎么办？答案就是扩容，这也就是为什么一直说ArrayList的底层是基于动态数组实现的原因，动态数组的意思就是指底层的数组大小并不是固定的，而是根据添加的元素大小进行一个判断，不够的话就动态扩容，扩容的代码就在ensureCapacity里面：

	public void ensureCapacity(int minCapacity) {
	modCount++;
	int oldCapacity = elementData.length;
	if (minCapacity > oldCapacity) {
	    Object oldData[] = elementData;
	    int newCapacity = (oldCapacity * 3)/2 + 1;
	        if (newCapacity < minCapacity)
	    newCapacity = minCapacity;
	           // minCapacity is usually close to size, so this is a win:
	           elementData = Arrays.copyOf(elementData, newCapacity);
	}
	}
看到扩容的时候把元素组大小先乘以3，再除以2，最后加1。可能有些人要问为什么？我们可以想：

1、如果一次性扩容扩得太大，必然造成内存空间的浪费

2、如果一次性扩容扩得不够，那么下一次扩容的操作必然比较快地会到来，这会降低程序运行效率，要知道扩容还是比价耗费性能的一个操作

所以扩容扩多少，是JDK开发人员在时间、空间上做的一个权衡，提供出来的一个比较合理的数值。最后调用到的是Arrays的copyOf方法，将元素组里面的内容复制到新的数组里面去：

	public static <T,U> T[] copyOf(U[] original, int newLength, Class<? extends T[]> newType) {
       T[] copy = ((Object)newType == (Object)Object[].class)
           ? (T[]) new Object[newLength]
           : (T[]) Array.newInstance(newType.getComponentType(), newLength);
       System.arraycopy(original, 0, copy, 0,
                        Math.min(original.length, newLength));
       return copy;
	}
用一张图来表示就是这样的：

![](https://i.imgur.com/JRwLsv9.png)

 

###删除元素

接着我们看一下删除的操作。ArrayList支持两种删除方式：

1、按照下标删除

2、按照元素删除，这会删除ArrayList中与指定要删除的元素匹配的第一个元素

对于ArrayList来说，这两种删除的方法差不多，都是调用的下面一段代码：

	int numMoved = size - index - 1;
	if (numMoved > 0)
	    System.arraycopy(elementData, index+1, elementData, index,
	             numMoved);
	elementData[--size] = null; // Let gc do its work
其实做的事情就是两件:

1、把指定元素后面位置的所有元素，利用System.arraycopy方法整体向前移动一个位置

2、最后一个位置的元素指定为null，这样让gc可以去回收它

比方说有这么一段代码：

	public static void main(String[] args)
	{
    List<String> list = new ArrayList<String>();
    list.add("111");
    list.add("222");
    list.add("333");
    list.add("444");
    list.add("555");
    list.add("666");
    list.add("777");
    list.add("888");
    list.remove("333");
	}
用图表示是这样的：

![](https://i.imgur.com/NPvHB3k.png)

插入元素

看一下ArrayList的插入操作，插入操作调用的也是add方法，比如：

 	 public static void main(String[] args)
  	 {
      List<String> list = new ArrayList<String>();
      list.add("111");
      list.add("222");
      list.add("333");
      list.add("444");
      list.add("555");
      list.add("666");
     list.add("777");
     list.add("888");
     list.add(2, "000");
     System.out.println(list);
 	}
有一个地方不要搞错了，第12行的add方法的意思是，往第几个元素后面插入一个元素，像第12行就是往第二个元素后面插入一个000。看一下运行结果也证明了这一点：

[111, 222, 000, 333, 444, 555, 666, 777, 888]
还是看一下插入的时候做了什么：

 	 public void add(int index, E element) {
  	 if (index > size || index < 0)
      throw new IndexOutOfBoundsException(
      "Index: "+index+", Size: "+size);
      ensureCapacity(size+1);  // Increments modCount!!
	  System.arraycopy(elementData, index, elementData, index + 1,
           size - index);
  	elementData[index] = element;
  	size++;
 	}
看到插入的时候，按照指定位置，把从指定位置开始的所有元素利用System.arraycopy方法做一个整体的复制，向后移动一个位置（当然先要用ensureCapacity方法进行判断，加了一个元素之后数组会不会不够大），然后指定位置的元素设置为需要插入的元素，完成了一次插入的操作。用图表示这个过程是这样的：

![](https://i.imgur.com/1qhTV7D.png)

ArrayList的优缺点

从上面的几个过程总结一下ArrayList的优缺点。ArrayList的优点如下：

1、ArrayList底层以数组实现，是一种随机访问模式，再加上它实现了RandomAccess接口，因此查找也就是get的时候非常快

2、ArrayList在顺序添加一个元素的时候非常方便，只是往数组里面添加了一个元素而已

不过ArrayList的缺点也十分明显：

1、删除元素的时候，涉及到一次元素复制，如果要复制的元素很多，那么就会比较耗费性能

2、插入元素的时候，涉及到一次元素复制，如果要复制的元素很多，那么就会比较耗费性能

因此，ArrayList比较适合顺序添加、随机访问的场景。


ArrayList和Vector的区别

ArrayList是线程非安全的，这很明显，因为ArrayList中所有的方法都不是同步的，在并发下一定会出现线程安全问题。那么我们想要使用ArrayList并且让它线程安全怎么办？一个方法是用Collections.synchronizedList方法把你的ArrayList变成一个线程安全的List，比如：

	List<String> synchronizedList = Collections.synchronizedList(list);
	synchronizedList.add("aaa");
	synchronizedList.add("bbb");
	for (int i = 0; i < synchronizedList.size(); i++)
	{
	    System.out.println(synchronizedList.get(i));
	}
另一个方法就是Vector，它是ArrayList的线程安全版本，其实现90%和ArrayList都完全一样，区别在于：

1、Vector是线程安全的，ArrayList是线程非安全的

2、Vector可以指定增长因子，如果该增长因子指定了，那么扩容的时候会每次新的数组大小会在原数组的大小基础上加上增长因子；如果不指定增长因子，那么就给原数组大小*2，源代码是这样的：

int newCapacity = oldCapacity + ((capacityIncrement > 0) ?
                                 capacityIncrement : oldCapacity);
 

为什么ArrayList的elementData是用transient修饰的？

最后一个问题，我们看一下ArrayList中的数组，是这么定义的：

private transient Object[] elementData;
不知道大家有没有想过，为什么elementData是使用transient修饰的呢？关于这个问题，说说我的看法。我们看一下ArrayList的定义：

		public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable
看到ArrayList实现了Serializable接口，这意味着ArrayList是可以被序列化的，用transient修饰elementData意味着我不希望elementData数组被序列化。这是为什么？因为序列化ArrayList的时候，ArrayList里面的elementData未必是满的，比方说elementData有10的大小，但是我只用了其中的3个，那么是否有必要序列化整个elementData呢？显然没有这个必要，因此ArrayList中重写了writeObject方法：

	private void writeObject(java.io.ObjectOutputStream s)
	        throws java.io.IOException{
	// Write out element count, and any hidden stuff
	int expectedModCount = modCount;
	s.defaultWriteObject();
	        // Write out array length
	       s.writeInt(elementData.length);
	    // Write out all elements in the proper order.
	for (int i=0; i<size; i++)
	           s.writeObject(elementData[i]);
	    if (modCount != expectedModCount) {
	           throw new ConcurrentModificationException();
	    }
	}
每次序列化的时候调用这个方法，先调用defaultWriteObject()方法序列化ArrayList中的非transient元素，elementData不去序列化它，然后遍历elementData，只序列化那些有的元素，这样：

1、加快了序列化的速度

2、减小了序列化之后的文件大小