---
title: '''JavaBasics5'''
date: 2020-03-12 12:05:43
tags: Java基础
categories: Java
---
## 内部类
一个事物的内部包含另一个事物，一个类内部包含另一个类

<!--more-->

1. 成员内部类
修饰符 class 外部类名称{
	修饰符 class内部类名称{
	}
}
内用外随意，外用内一定要内部对象

public class Body {
	//外部类的成员变量
	private String name;
	
	public void methodBody(){
		sout("外部类方法");
	}
	//成员内部类
	public class Heart{ 
		public void beat(){
			sout("心脏跳动");
			sout(name);
		}
	}
}

//如何使用成员内部类？
1. 间接方式：在外部类的方法当中，使用内部类，然后main只是调用外部类的方法。
2. 直接方式： 公式
			 类名称 对象名 = new 类名称();
             外部类名称.内部类名称 对象名 = new 外部类名称().new 内部类名称();
			 
重名问题：
public class Outer{
	int num = 10; //外部类成员变量
	public class Inner {
		int num = 20; //内部类成员变量
		public void methodInner(){
			int num = 30; //内部类方法的局部变量
			sout(num);//局部变量就近 30
			sout(this.num); //内部类成员变量 20
			sout(Outer.this.num); //外部类成员变量 10
		}
	}
}

2. 局部内部类
如果一个类是定义在一个方法内部的，那么这就是一个局部内部类
只有当前所属的方法才能使用他，出了这个方法就不能使用了

public class Outer{
	public void methodOuter(){
		//局部内部类
		class Inner{
			int num = 10;
			public void methodInner(){
				sout(num);
			}
		}
		Inner inner = new Inner();
		inner.methodInner();
	}
}

通过调用外部类方法 methodOuter()调用局部内部类


小结： 1. 外部类： public/(default)
	   2. 成员内部类：public/protected/(default)/private
	   3. 局部内部类：什么都不能写
	   
局部内部类如果希望访问所在方法的局部变量，那么这个局部变量必须是有效final的
从Java8开始，只要局部变量事实不变，final可省略
原因：1. new出来的对象在堆内存中
	  2. 局部变量是跟着方法走的，在栈内存中
	  3. 方法运行结束后，立刻出栈，局部变量就会立刻消失
	  4. 但是new出来的对象会在堆中持续存在，直到回收消失，因此局部内部类对象访问的局部变量不能发生改变

	  
3. 匿名内部类
如果接口的实现类(或者父类的子类)只需要使用一次
那么可省略定义，改用匿名内部类
格式:
接口名称 对象名 = new 接口名称(){
	//覆盖重写方法
}

原本： 一个接口 -> 建一个实现类 -> new一个对象 -> 使用
现在：使用匿名内部类
	MyInterface obj = new MyInterface(){
		@override
		//重写抽象方法
	}

注：
1. 匿名内部类，在创建对象的时候，只能使用唯一一次，
   如果希望多次创建对象，且类内容一样的话，那就要用单独定义的实现类
2. 匿名对象是在调用方法时只能调用一次，若想要调用多个方法，就需要创建对象
3. 匿名内部类是省略了实现[实现类/子类名称],但是匿名对象省略了对象名称

成员变量也可以是一个类
实际上任一个类型都可以作为成员变量,接口也可以
接口也可以作为方法的参数或返回值

## 发红包案例
package Redpackage;

import java.util.ArrayList;

public class Manager extends User {
	public Manager() {

	}

	public Manager(String name, int money) {
		super(name, money);
	}

	public ArrayList<Integer> send(int totalmoney,int count){
		//首先需要一个集合，用来存储若干个红包金额
		ArrayList<Integer> redList = new ArrayList<Integer>();

		//看一下群主有多少钱
		int leftMoney = super.getMoney();
		if (totalmoney > leftMoney ){
			System.out.println("余额不足");
			return redList;
		}

		//扣钱,其实就是重新设置余额
		super.setMoney(leftMoney - totalmoney);

		//发红包需要平均拆分为count份
		int avg = totalmoney/count;
		//余下的零头塞最后一个红包里
		int mod = totalmoney % count;
		int last = avg + mod;
		//塞红包
		for (int i = 0; i < count-1; i++) {
			redList.add(avg);
		}

		redList.add(last);
		return redList;
	}
}

-------------------------------------------------------------------------------------------

package Redpackage;

import java.util.ArrayList;
import java.util.Random;

public class Member extends User {
	public Member() {
	}

	public Member(String name, int money) {
		super(name, money);
	}

	public void receive(ArrayList<Integer> list){
		//从多个红包当中随便抽取一个给自己
		//随机获得一个集合当中的索引编号
		int index = new Random().nextInt(list.size());
		//根据索引从集合中删除，并且得到被删除的红包给自己
		int delta = list.remove(index);
		int money = super.getMoney();
		super.setMoney(money + delta);


	}
}


-------------------------------------------------------------------------------------------


package Redpackage;

public class User {
	private String name; //用户姓名
	private int money; //账户余额

	public User() {
	}

	public User(String name, int money) {
		this.name = name;
		this.money = money;
	}

	//展示一下当前用户有多少钱
	public void show(){
		System.out.println(name + '有' + money + '元');
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public int getMoney() {
		return money;
	}

	public void setMoney(int money) {
		this.money = money;
	}
}

-------------------------------------------------------------------------------------------

package Redpackage;

import java.util.ArrayList;

public class MainRed {
	public static void main(String[] args) {
		Manager manager = new Manager("群主",100);
		Member member1 = new Member("群员1",0);
		Member member2 = new Member("群员2",0);
		Member member3 = new Member("群员3",0);

		manager.show();
		member1.show();
		member2.show();
		member3.show();
		System.out.println("=========================");

		ArrayList<Integer> redlist = manager.send(10,3);
		member1.receive(redlist);
		member1.show();
		member2.receive(redlist);
		member2.show();
		member3.receive(redlist);
		member3.show();

	}
}
