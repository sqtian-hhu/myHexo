---
title: '''JavaNote01'''
date: 2020-03-12 12:07:38
tags: Java基础
categories: Java
---
## 第二章 日期时间类
2.1 Date类
	表示日期时间的类，精确到毫秒，特定的瞬间
	当前日期：2088-01-01
	时间原点：1970-01-01 00:00:00(英国格林威治)
	
<!--more-->

public class Demo01Date{
	public static void main(String[] args){
		System.out.println(System.currentTimeMiLLis());//获取时间原点到当前时间经历了多少毫秒
		
	}
}

public class Demo02Date {
	public static void main(String[] args) {
//		demo01();
		demo02();
		demo03();
	}

	//成员方法 Long getTime() 把日期转换为毫秒
	private static void demo03() {
		Date date = new Date();
		long time = date.getTime();
		System.out.println(time);//1583119265044
	}

	//带参数的构造方法
	//Date(Long date) 传递毫秒值，把毫秒值转换为Date日期
	private static void demo02() {
		Date date = new Date(1583119265044L);
		System.out.println(date);
	}

	// Date类空参数构造方法
	private static void demo01() {
		Date date = new Date();
		System.out.println(date);

	}

}


2.2 DateFormat类
	java.text
	日期时间格式化子类的抽象类
	
public class Demo01DateFormat {
	/*
	成员方法：
	String format(Date date) 按照指定模式，把Date日期格式化为符合模式的字符串
	Date parse(String source) 把符合模式的字符串解析为Date日期 throws 抛出异常
	构造方法：
	SimpleDateFormat(String pattern) 用给定模式构造
	y 年 M 月 d 日 H 时 m 分 s 秒
	写对应的模式，会把模式替换为对应的日期和时间
	‘yyyy-MM-dd HH:mm:ss’
	注意：
	模式中的字母不能改，连接模式的符号可以改
	‘yyyy年MM月dd日 HH时mm分ss秒’

	 */

	public static void main(String[] args) throws ParseException {
		demo01();
		demo02();
	}

	private static void demo01() {
		SimpleDateFormat sdf = new SimpleDateFormat("yyyy年MM月dd日 HH时mm分ss秒");
		Date date = new Date();
		String d = sdf.format(date);
		System.out.println(date);
		System.out.println(d);

	}

	private static void demo02() throws ParseException {
		SimpleDateFormat sdf = new SimpleDateFormat("yyyy年MM月dd日 HH时mm分ss秒");
		Date date = sdf.parse("2020年03月02日 13时05分54秒");
		System.out.println(date);

	}
}

2.4 Calendar类
	是一个抽象类，里面提供了很多操作日历字段的方法
	无法直接创建对象使用，里面有一个静态方法叫个体Instance(),该方法返回Calendar子类对象
	
	/*
常用成员方法：
public int get(int field): 返回给定日历字段的值
public void set(int field,int value): 将指定日历字段设置为给定值
public abstract void add(int field,int amount): 根据日历的规则，为给定日历字段添加或减去指定的时间量
public Date getTime(): 返回一个表示此Calendar时间值的Date对象

 */
public class CalendarClass {
	public static void main(String[] args) {

		demo01();
	}

	private static void demo01() {
		Calendar c = Calendar.getInstance();
		System.out.println(c);

		int year = c.get(Calendar.YEAR);
		System.out.println(year);
		int month = c.get(Calendar.MONTH) + 1;//计算机中从0开始
		System.out.println(month);
		int date = c.get(Calendar.DATE);
		System.out.println(date);

		//set 方法
		c.set(Calendar.YEAR, 200);
		c.set(Calendar.MONTH, 3);
		int year2 = c.get(Calendar.YEAR);
		System.out.println("after setting" + year2);
		int month2 = c.get(Calendar.MONTH) + 1;//计算机中从0开始
		System.out.println("after setting" + month2);
		//同时设置年月日

		c.set(1997, 3, 10);
		int year3 = c.get(Calendar.YEAR);
		System.out.println("after setting" + year3);
		int month3 = c.get(Calendar.MONTH) + 1;//计算机中从0开始
		System.out.println("after setting" + month3);

		//add方法
		c.add(Calendar.YEAR, 8);
		c.add(Calendar.MONTH, -2);
		int year4 = c.get(Calendar.YEAR);
		System.out.println("after setting" + year4);
		int month4 = c.get(Calendar.MONTH) + 1;//计算机中从0开始
		System.out.println("after setting" + month4);

		//getTime方法
		Date d = c.getTime();
		System.out.println(d);

	}
}





## 第三章 System类
提供大量静态方法，可以获取与系统相关的信息或系统级操作
常用方法：
public static long currentTimeNillis(): 返回以毫秒为单位的当前时间
public static void arraycopy(Object src,int srcPos,Object dest,int destPos,int,length): 将数组中指定数据拷贝到另一个数组中

public class Demo01System {
	public static void main(String[] args) {
		demo01();
		demo02();
	}

	private static void demo01() {
		//public static long currentTimeNillis;//返回以毫秒为单位的当前时间,用来看程序的效率
		//程序开始前，获取一次
		long s = System.currentTimeMillis();
		//执行for循环
		for (int i = 1; i <= 9999; i++) {
			System.out.println(i);
		}

		//程序执行后，获取一次毫秒值
		long e = System.currentTimeMillis();
		System.out.println("total"+(e-s)+"ms");
	}


	private static void demo02() {
		/*public static void arraycopy(Object src,int srcPos,Object dest,int destPos,int,length)
		src 源数组
		srcPos 源数组中的起始位置(起始索引)
		dest 目标数组
		destPos 目标数组中的起始位置
		length 要复制的数组元素的数量
		 */

		// 将src前三个数字复制到dest前三个位置上
		int [] src = new int[]{1,2,3,4,5};
		int [] dest = {6,7,8,9,0};
		System.out.println("before copying"+ Arrays.toString(dest));
		System.arraycopy(src,0,dest,0,3);
		System.out.println("after copying"+ Arrays.toString(dest));
		
	}

}


## 第四章 StringBuilder类
String类是常量，创建之后不可改变。进行字符串相加，内存中就会有多个字符串，占用空间多，效率低下
StingBuilder类 字符串缓冲区，可以提高字符串的操作效率底层是一个数组，但没有被final修饰，可以改变长度
StringBuilder在内存中始终是一个数组，占用空间少，效率高。初始长度16，如果超出容量，会自动扩容

public class StringBuilderClass {
	/*
	构造方法：
		StringBuilder() 无参构造
		StringBuilder(String str) 带参构造，初始化为指定字符串内容，可用于String->StringBuilder
	常用成员方法：
	 	public StringBuilder append(): 添加任意类型数据的字符串形式，并返回当前对象自身
	 	public String toString(): 将当前StringBuilder对象转换为String，可用于StringBuilder->String


	 */

	public static void main(String[] args) {
		//无参构造
		StringBuilder bu = new StringBuilder();
		System.out.println("bu:"+bu);

		//带参构造
		StringBuilder bu2 = new StringBuilder("abc");
		System.out.println("bu2:"+bu2);

		StringBuilder bu1 = bu.append("abc");//把bu地址赋值给了bu1
		System.out.println(bu);//"abc"
		System.out.println(bu1);//"abc"
		System.out.println(bu==bu1);//比较的是地址，返回true
		//实际上使用append方法无需接受返回值
		bu.append(1);
		System.out.println(bu);
		//链式编程，返回的是对象，可以直接再调用方法
		bu.append("China").append("No.").append("1");
		System.out.println(bu);


		//toSting方法
		String str = "hello";
		System.out.println("str:"+str);
		StringBuilder builder = new StringBuilder(str);
		builder.append("world");
		System.out.println("builder:"+builder);
		String s = builder.toString();
		System.out.println("s:"+s);

	}
}

// .var 自动生成变量接收当前对象  new StringBuilder("abc").var =>StringBuilder bu2 = new StringBuilder("abc");
	

## 第五章 包装类
Java 提供了两个类型系统，基本类型和引用类型，使用基本类型在于效率，然而很多情况，会创建对象使用
因为对象可以做更多的功能，如果想要基本类型像对象一样操作，就可以使用基本类型对应的包装类
基本类型：byte, short, int, long, float, double, char, boolean
包装类：Byte, Short, Integer, Long, Float, Double, Character, Boolean

装箱拆箱
装箱：把基本类型的数据，包装到包装类中
	构造方法：
		Integer(int value) 构造一个新分配的Integer对象，它表示指定的int值
		Integer(String s) 构造一个新分配的Integer对象，它表示String参数所指示的int值
			传递的字符串，必须是基本类型的字符串，否则抛出异常，‘100’正确，‘a'异常
	静态方法：
		static Integer valueOf(int i) 返回一个表示指定的int值的Intger实例
		static Integer valueOf(String s) 返回保存指定的String 的值的Integer对象
		
拆箱：在包装类中取出基本类型的数据，包装到包装类中
	成员方法：
	int intValue() 以int类型返回该Integer的值

public class Demo01Integer {
	public static void main(String[] args) {
		//装箱
		Integer in1 = new Integer(1);//1
		System.out.println(in1);//重写了toString方法
		Integer in2 = new Integer("1");
		System.out.println(in2);//1

		//静态方法
		Integer in3 = Integer.valueOf("1");
		System.out.println(in3);

		//拆箱
		int i = in1.intValue();
		System.out.println(i);
		
		//自动装箱
		Integer in = 1;
		//自动拆箱
		in = in+2;//in是包装类，无法运算，会自动转换成int

	}
}


基本类型与字符串类型之间的互相转换
基本类型->字符串
	1. 基本类型的值 +"" 最简单的方法
	2. 包装类的静态方法toString(参数)，不是Obeject类的toString()重载
	3. String类的静态方法valueOf(参数)
		static String valueOf(int i) 
		
字符串->基本类型
		使用包装类的静态方法parseXXX("数值类型的字符串")
		Integer类：static int parseInt(String s)
		Double类：static double parseDouble(String s)