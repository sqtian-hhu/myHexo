---
title: '''JavaBasics2'''
date: 2020-03-11 17:27:41
tags: Java基础
categories: Java
---
## 面向对象三大特征：封装，继承，多态

封装：
1. 方法就是一种封装
2. 关键字private也是一种封装

<!--more-->

问题描述：定义person的年龄时，无法阻止不合理的数值被设置进来。
解决方案：用private关键字将需要保护的成员变量进行修饰。一旦使用了private进行修饰，
那么本类当中仍然可以随意访问，但超出了本类范围之外就不能直接再访问
间接访问：定义一对get/set方法
public class Person {
	private int age;
	public void setAge(int age) {
		if(age>0 && age <= 100) {
			this.age = age;
		}else{
			System.out.println("input the real age,please");
		}
	}

	public int getAge() {
		return age;
	}
}

对于基本类型中的布尔值，Getter方法一定要写成isXXX形式

当方法的局部变量和类的成员变量重名的时候，根据就近原则，优先使用局部变量
要访问本类的成员变量，使用this.成员变量名，this一定是在方法内部
通过谁调用的方法，this就是谁

//构造方法
专门用来构造对象的方法，当我们通过new创建对象时，其实就是在调用构造方法
格式：
public 类名称(参数类型 参数名称){
	方法体
}

1. 构造方法的名称必须和所在类名称完全一样(包括大小写)
2. 构造方法不用返回值类型，连void都不写
3. 不能return一个具体返回值
4. 如果没有编写构造方法，编译器默认赠送一个构造方法，没有参数，方法什么都不做 public Person(){}

public class Person {
	private String name;
	private int age;
	private int weight;
	private int height;
	//无参构造
	public Person() {
	}
	//有参构造
	public Person(String name, int age, int weight, int height) {
		this.name = name;
		this.age = age;
		this.weight = weight;
		this.height = height;
	}
}

一个标准的类通常拥有下面四个部分
1. 所有成员变量要使用private类关键字修饰
2. 为每一个成员编写一对Getter/Setter方法
3. 编写一个无参构造方法
4. 编写一个全参构造方法

## API
应用程序编程接口，JDK提供使用的类
Scanner类:
功能：实现键盘输入数据到程序当中
Scanner sc = new Scanner(System.in);
使用：获取键盘输入的int：int num = sc.nextInt();
	  获取键盘输入的字符串：String str = sc.next();

匿名对象：new person().name = "cc";

只想输入一次时，使用Scanner匿名对象
	int num = new Scanner(System.in).nextInt();

使用一般写法作为参数传入
Scanner sc = new Scanner(System.in);
method(sc);
使用匿名对象传入
method(new Scanner(System.in));

Random类：
用来生成随机数字
Random r = new Random();
int num = r.nextInt();
int num = r.nextInt(10);//[0,10)范围

对象数组
	Person[] array = new Person[3];
	Person one = new Person();
	Person two = new Person();
	Person three = new Person();
	array[0] = one;
	array[1] = two;
	array[2] = three;
	
	array[0].getName();

	
ArrayList<E> 
<E>代表泛型 装什么类型，只能放引用类型,不能放基本类型
数组长度不可以改变
但ArrayList可以改变
ArrayList<String> list = new ArrayList<>();
ArrayList直接打印得到的不是地址值，而是内容
增加：list.add();
获取：list.get(i);
删除：list.remove(i);//返回删除的值
尺寸：int size = list.size();

想放基本类型，必须使用“包装类”
ArrayList<Integer> listc  = new ArrayList<>();


//字符串
1. 字符串内容永不可变
2. 因此，字符串可以共享使用
3. 字符串效果上相当于char[]

创建字符串的方法
常见3+1
public String(); //创建一个空白字符串

String str = "Hello";  //直接创建

public String(char[] array);  //根据字符数组内容创建

char[] charArray = {'A','B','C'};
String str1 = new String(charArray);
=> ABC

public String(byte[] array);  //根据字节数组

byte[] byteArray = {97,98,99};
String str2 = new String(byteArray);
=> ABC


字符串常量池：
程序当中直接写上的双引号字符串，就在字符串常量池中。
对于基本类型来说：==是进行数值的比较
对于引用类型来说：==是进行地址值的比较

1. 任何对象都能用object接收
对字符串内容比较：str1.equals(str2);
2. equals方法具有对称性 
a.equals(b)与b.equals(a)相同
3. 推荐"abc".equals(str),不推荐str.equals("abc");

equalsIgnoreCase: 忽略大小写的equals方法


//String中常用方法
长度： str.length()
拼接： str1.concat(str2) //str1不会变化，字符串是常量，返回全新的字符串
		""+""+""
获取指定索引的字符： charAt()
			   char ch = str.charAt(1);
查找参数字符串，第一次出现的索引，没有则返回-1
			   int index = original.indexOf("cc");
截取： [ , )
public String substring(int index);  //截取指定位置到最后
public String substring(int begin,int end);

转换成字符数组：
char[] chars = "Hello".toCharArray(); //变成数组
=> ['H','e','l','l','o']

转换成字节数组：
byte[] bytes = "abc".getBytes();
=>[97,98,99]

替换： 
String lang2 = lang1.replace(charsequence oldstring,charsequence newstring);
分割：
String str1 = "aaa,bbb,ccc";
String[] a1 = str1.split(",");
=>a1: ["aaa","bbb","ccc"]
split方法的参数是一个正则表达式，如果想按"."切分，必须写"//."转义



//static 静态
多个对象共享一份数据，一旦用了static关键字，则内容属于类，而不是对象自己
静态方法
public static void methord(){
静态方法属于类，可以通过对象名调用，也可以直接类名调用
推荐类名调用
如果没有static，就必须先创建对象，通过对象调用
}
注意：
1. 静态方法不能直接访问非静态变量(先人不知后人，static先产生，非static后产生)
2. 静态是随类产生的，与对象无关，不能用this

静态代码块：
当第一次用到本类时运行
典型用途：用来一次性对静态成员进行赋值
public class 类名称{
	static{
		//静态代码块
	}
}


//Arrays 数组工具类：
必须是一个数组，非数组可以通过toCharArray()转换
提供大量静态方法，用来实现数组常见操作
1. 将数组变为字符串
int [] intArray = {10,20,30};
String intStr = Arrays.toString(intArray);
=>[10,20,30]
2. 排序
Arrays.sort(array1);//默认升序
如果是自定义类型，那么自定义类型要有Comparable或Comparator接口支持


//Math 数学工具类
1. Math.abs():取绝对值
2. Math.ceil();向上取整，3.1->4.0,3.9->4.0
3. Math.floor();向下取整，3.1->3.0,3.9->3.0
4. Math.round();四舍五入
5. Math.PI