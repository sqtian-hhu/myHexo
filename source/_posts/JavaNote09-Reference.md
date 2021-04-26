---
title: '''JavaNote09_Reference'''
date: 2020-04-02 21:10:05
tags: Java基础
categories: Java
---
## 方法引用
如果在Lambda中所指定的操作方案，已经有地方存在相同方案，是否还有必要再写重复逻辑
方法引用符: 双冒号::
如果Lambda要表达的函数方案已经存在与某个方法的实现中,那么则可以通过双冒号来引用该方法作为Lambda替代者
方法引用就是把要做的事情交给别人做,因为别人已经做过了,而Lambda还是自己做

<!--more-->

@FunctionalInterface
public interface Printable {
	//打印字符串的抽向方法
	void print(String s);
}
--------------------------------
public class Demo01Printable {
	//定义一个方法,参数传递Printable接口,对字符串进行打印
	public static void printString(Printable p) {
		p.print("HelloWorld");
	}

	public static void main(String[] args) {
		printString((s -> {
			System.out.println(s);
		}));

		/*
			分析:
				Lambda表达式的目的,打印参数传递的字符串
				把参数s,传递给了System.out对象,调用out对象中的方法println对字符串进行了输出
				注意:
					1. System.out对象已经存在
					2. println方法也已经存在
				所以我们可以使用方法引用来优化Lambda表达式
				可以使用System.out方法直接调用println方法
		 */
		printString(System.out::println);//replace lambda with method reference
	}
}

## 通过对象名引用成员方法
/*
	通过对象名引用成员方法
	使用前提是对象是已经存在的,成员方法也是已经存在
	就可以使用对象名来引用成员方法

 */
public class Demo01ObjectMethodReference {
	//定义一个方法,方法的参数传递Printable接口
	public static void printString(Printable p){
		p.print("Hello");
	}

	public static void main(String[] args) {
		//调用printString方法,方法的参数Printable是一个函数式接口
		printString((s -> {
			//创建MethodRerObject对象
			MethodRerObject obj = new MethodRerObject();
			//调用成员方法
			obj.printUpperCaseString(s);
		}));

		/*
			使用方法引用优化Lambda
			对象是已经存在的MethodRerObject
			成员方法也是已经存在的printUpperCaseString
			所以可以使用对象名引用成员方法
		 */
		MethodRerObject obj = new MethodRerObject();
		printString(obj::printUpperCaseString);
	}
}

## 通过类名引用静态成员方法
类存在,静态成员方法也已经存在
就可以通过类名直接引用静态成员方法


@FunctionalInterface
public interface Calcable {
	//定义一个抽象方法,传递一个整数,对整数进行绝对值计算,并返回
	int calsAbs(int number);
}

----------------------------------------
public class Demo01StaticMethodReference {
	public static int method(int number, Calcable c){
		return c.calsAbs(number);
	}

	public static void main(String[] args) {
		//调用方法,传递计算绝对值的整数,和Lambda表达式
		int number = method(-10,(n)->{
			//绝对值计算
			return Math.abs(n);
		});
		System.out.println(number);

		/*
			使用方法引用优化Lambda表达式
			Math类已经存在
			abs计算绝对值的静态方法也已经存在
			所以可以直接引用
		 */
		int number2 = method(-10, Math::abs);
		System.out.println(number2);
	}
}

## 通过super引用成员方法
如果存在继承关系,当Lambda中需要出现super调用时,也可以使用引用方法进行替代
@FunctionalInterface
//定义一个见面的函数式接口
public interface Greetable {
	void greet();
}
-----------------------------------------------------
//定义一个父类
public class Human {
	public void sayHello(){
		System.out.println("Hello,I'm Human");
	}
}
------------------------------------------------------
public class Man extends Human {
	@Override
	public void sayHello() {
		System.out.println("Hello,I'm Man");
	}

	//定义一个方法传递Greetable接口
	public void method(Greetable g){
		g.greet();
	}
	public void show(){
		//调用method方法,方法的参数Greetable时应该函数式接口
		method(()->{
			//创建Human对象
			Human h = new Human();
			//调用父类的sayHello方法
			h.sayHello();
		});
		//因为有子父类关系,所以存在一个关键字super,代表父类,可以直接使用super调用父类的成员方法
		method(()->{
			super.sayHello();
		});

		method(super::sayHello);
	}

	public static void main(String[] args) {
		new Man().show();
	}
}
----------------------------------------------------------------

## 通过this引用成员方法
如果需要引用的方法就是当前类中的成员方法，那么可以使用this::成员方法的格式来使用方法引用
//定义一个富有的函数式接口
@FunctionalInterface
public interface Richable {
	//定义一个想买什么就买什么的方法
	void buy();
}
---------------------------------------------
/*
	通过this引用本类的成员方法
 */
public class Husband {
	//定义一个买房子的方法
	public void buyHouse(){
		System.out.println("北京二环内买一套四合院");
	}

	//定义一个结婚的方法,参数传递Richable接口
	public void marry(Richable r){
		r.buy();
	}

	//定义一个非常高兴的方法
	public void soHappy(){
//		//调用结婚的方法
//		marry(()->{
//			//使用this调用本类买房子的方法
//			this.buyHouse();
//		});

	/*
		this已经存在
		本类的方法也已经存在
		直接使用this引用本类的成员方法buyHouse
	 */
		marry(this::buyHouse);
	}

	public static void main(String[] args) {
		new Husband().soHappy();
	}
}
--------------------------------------------------------

## 类的构造器引用
由于构造器的名称与类名完全一样,并不固定,所以构造器引用使用类名称::new 的格式表示

@FunctionalInterface
public interface PersonBuilder {
	//定义一个方法,根据传递的姓名,创建Person对象
	Person buildPerson(String name, int age);
}
--------------------------------------------------------
/*
	类的构造器引用
 */
public class Demo {

	public static void printName(String name,int age, PersonBuilder pb){
		Person person = pb.buildPerson(name,age);
		System.out.println(person.getName());
	}

	public static void main(String[] args) {
		printName("西门吹雪",18,(String name,int age)->{
			return new Person(name,age);
		});

		/*
			构造方法new Person(String name,int age)已知
			创建对象已知
			就可以使用Person引用new创建对象

		 */
		printName("李寻欢",19,Person::new);
	}
}
-------------------------------------------------------------------

## 数组的构造器引用
/*
	定义一个创建数组的函数式接口
 */
@FunctionalInterface
public interface ArrayBuilder {
	//定义一个创建int类型数组的方法,参数传递数组长度
	int[] builderArray(int length);
}
----------------------------------------------------------
/*
	数组的构造器引用
 */
public class Demo02 {
	/*
	定义一个方法
	方法的参数传递创建数组的长度和ArrayBuilder接口
	方法内部根据传递的长度使用ArrayBuilder中的方法创建数组并返回
	 */
	public static int[] createArray(int length,ArrayBuilder ab){
		return ab.builderArray(length);
	}

	public static void main(String[] args) {
		//调用createArray方法,传递数组的长度和Lambda表达式
		int[] arr1 = createArray(10,(len)->{
			return new int[len];
		});
		System.out.println(arr1.length);//10
		/*
			使用方法引用优化
			已知创建的式int[]数组
			数组的长度也已知
		 */
		int[] arr2 = createArray(10,int[]::new);
		System.out.println(arr2.length);//10
	}
}