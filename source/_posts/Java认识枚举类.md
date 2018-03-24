---
title: 【java认识】枚举类的使用
date: 2015-7-14 16:42:20
tags:  枚举类的使用
reward: true
---

# 枚举类的使用方法

在没有枚举类的时候一般我们定义星期几或者其他的类型的数据，一般都通过上面的那种静态变量的形式来，为了简化许多ifelse,枚举类出现了，他可以看作是java 语法的语法糖！

<!--more-->

### 上代码

	public enum  WeekAllDay{//日期枚举类
	SUN,MON,TUS,WED,THU,FRI,SAT;	
    }

### 枚举类方法

        // 它的作用是传来一个字符串，然后将它转变为对应的枚举变量。
		//前提是你传的字符串和定义枚举变量的字符串一抹一样，区分大小写
		//如果你传了一个不存在的字符串，那么会抛出异常。
		System.out.println(WeekAllDay.valueOf("MON"));

		//返回weekallday的全部数组
		System.out.println(WeekAllDay.values());

		//默认情况下枚举类会给枚举一个默认的次序该次序从0开始
		System.out.println(WeekAllDay.MON.ordinal());

		//该方法用来比较两个枚举变量的”大小”，实际上比较的是两个枚举变量的次序，返回两个次序相减后的结果，如果为负数
		//就证明变量1”小于”变量2 （变量1.compareTo(变量2)，返回【变量1.ordinal() - 变量2.ordinal()】）
		System.out.println(WeekAllDay.FRI.compareTo(WeekAllDay.MON));

		//返回一个枚举定义枚举变量的字符串，比如MON就返回[MON]
		//他和name 方法是一样的唯一不同的是你可以重写tostring方法
		System.out.println(WeekAllDay.MON.toString());
		
	}

### 枚举类高级使用方法

### 上代码

		SUN("0","星期六"),MON("1","星期一"),TUS("2","星期二"),WED("3","星期三"),THU("4","星期四"),FRI("5","星期五"),SAT("6","星期六");
	//final 之后无法改变name和value值
	private final String name;
	private final String value;
	//构造
	private WeekAllDay(String name , String value){
		this.name = name;
		this.value = value;
	}
	//获取枚举类value 可以用来获取变量值
	public static String getWeekValue( String name ){
		for (WeekAllDay week : WeekAllDay.values()) {
			if(week.name.equals(name)){
				return week.getValue();
			}
		}
		return null;
	}
		//判断是否为星期五
		public boolean  ifWeekDay(){
			if(WeekAllDay.FRI.equals(this)){
				return true;
			}
			return false;
		
	}
	
	
	public String getName() {
		return name;
	}
	public String getValue() {
		return value;
	}
	
	
### 实现抽象方法


	SUN("0","星期天") {
		@Override
		public WeekAllDay getNextLamp() {
			System.out.println("接口");
			return null;
		}
	};
	//final 之后无法改变name和value值
	private final String name;
	private final String value;
	//构造
	private WeekAllDay(String name , String value){
		this.name = name;
		this.value = value;
	}
	//获取枚举类value 可以用来获取全部变量
	public static String getWeekValue( String name ){
		for (WeekAllDay week : WeekAllDay.values()) {
			if(week.name.equals(name)){
				return week.getValue();
			}
		}
		return null;
	}
	
	public String getName() {
		return name;
	}
	public String getValue() {
		return value;
	}
	
    public abstract WeekAllDay getNextLamp();
    }
	
### switch 语句中使用


    Signal color = Signal.RED;
    public void change() {
        switch (color) {
        case RED:
            color = Signal.GREEN;
            break;
        case YELLOW:
            color = Signal.RED;
            break;
        case GREEN:
            color = Signal.YELLOW;
            break;
        }
        }
### 创建单例
    public class Singleton{
    private static final Singleton INSTANCE = new Singleton();

    private Singleton(){}

    public static Singleton getSingleton(){
        return INSTANCE;
    }
    }
    枚举式
    public class eaySingleton{
    INSTANCE
    }

枚举类的实现可以看作是new 出来本身的对象引用 和咱们别的类new出的对象差不多 ，而且是final的！
