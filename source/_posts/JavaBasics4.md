---
title: '''JavaBasic4'''
date: 2020-03-12 12:02:30
tags: Java基础
categories: Java
---
## 多态性
一个对象具有多态
实现：父类引用指向子类对象，左父右子
   父类名称 对象名 = new 子类对象()
或 接口名称 对象名 = new 实现类名称()
子类当父类使用，猫是动物，作为动物类使用

访问成员变量：1. 等号左边是谁，就优先用谁，没有则向上找
			  2. 间接通过成员方法访问
		口诀：编译看左边，运行还看左边
成员方法：子类覆盖重写了就用子类方法，没有重写就用父类
    口诀：编译看左边，运行看右边

<!--more-->

多态的好处：
员工：work();//抽象
讲师类extends员工：work(){讲课}
助教类extends员工：work(){辅导}
如果不用多态：
Teacher one = new Teacher();
one.work(); //讲课
Assistant two = new Assistant();
two.work(); //辅导
我现在唯一要做的是调用work方法，并不关心哪一类员工工作
使用多态：
Employee one = new Teacher();
one.work();
Employee two = new Assistant();
two.work();
好处：无论右边new的时候换成哪个子类对象，等号左边调用方法不会变化。


对象的向上转型：
1. 对象的向上转型，其实就是多态写法
Animal animal = new Cat();
创建了一只猫，当作动物看待，没有问题
向上转型一定是安全的

向下转型：
其实是一个[还原]的动作
对象一旦上转型为父类，那么就无法调用子类原本特有的内容
用向下转型还原
格式：子类名称 对象名 = (子类名称)父类对象;
	  Cat cat = (Cat)animal;
	  必须保证对象本来创建时就是猫，否则报错


//instanceof 关键字：先判断再向下转型
Animal animal = new Cat(); //本来是一只猫
animal.eat(); //猫吃鱼
// instance of 返回 boolean值，判断前面对象能不能当作后面类的实例
if(animal instanceof Dog){
	Dog dog = (Dog)animal;
	dog.watchHouse();}
if(animal instanceof Cat){
	Cat cat = (Cat)animal;
	cat.catchMouse();}
	
## 笔记本USB接口案例


package CmoputerUsb;

public class Computer {
	public void powerOn(){
		System.out.println("笔记本开机");
	}

	public void powerOff(){
		System.out.println("笔记本关机");
	}

	//使用USB设备，使用USB接口作为参数

	public void useDevice(USB usb){
		usb.open();

		if(usb instanceof Mouse){
			Mouse mouse = (Mouse) usb; //向下转型
			mouse.click();
		}else if(usb instanceof Keyboard){
			Keyboard keyboard = (Keyboard) usb;
			keyboard.type();
		}else if(usb instanceof Upan){
			Upan u = (Upan) usb;
			u.copy();
		}

		usb.close();
	}
}

-------------------------------------------------------------------------------------------
package CmoputerUsb;

public interface USB {
	public abstract void open();//打开设备

	public abstract void close();//关闭设备

}

-------------------------------------------------------------------------------------------
package CmoputerUsb;

//键盘是一种USB设备
public class Keyboard implements USB {
	@Override
	public void open() {
		System.out.println("open the keyboard");
	}

	@Override
	public void close() {
		System.out.println("close the keyboard");
	}

	public void type() {
		System.out.println("type the keyboard,kakaka");
	}
}

-------------------------------------------------------------------------------------------

package CmoputerUsb;

import java.sql.SQLOutput;

//鼠标是一种USB设备
public class Mouse implements USB {
	@Override
	public void open() {
		System.out.println("open the mouse");
	}

	@Override
	public void close() {
		System.out.println("close the mouse");
	}

	public void click() {
		System.out.println("click the mouse,kikiki");
	}
}


-------------------------------------------------------------------------------------------
package CmoputerUsb;

public class Upan implements USB{

	@Override
	public void open() {
		System.out.println("插入U盘");
	}

	@Override
	public void close() {
		System.out.println("拔出U盘");
	}

	public void copy(){
		System.out.println("U盘拷贝资料");
	}
}


-------------------------------------------------------------------------------------------

package CmoputerUsb;

public class CpuMain {
	public static void main(String[] args) {
		//首先创建一个电脑
		Computer computer = new Computer();
		computer.powerOn();

		//准备一个鼠标
		Mouse mouse = new Mouse();
		//向上转型
		USB usb1 = mouse;
		//使用鼠标
		computer.useDevice(usb1);


		//使用键盘
		USB usb2 = new Keyboard();
		computer.useDevice(usb2);

		//使用U盘
		computer.useDevice(new Upan());

		computer.powerOff();
	}
}


## final 关键字
代表最终，不可改变的
常见四种用法：
1. 可以用来修饰一个类
2. 可以用来修饰一个方法
3. 修饰一个局部变量
4. 修饰一个成员变量

1. final修饰一个类时
当前这个类不能有任何子类，从而所有成员方法都无法重写(因为没有儿子)
public final class 类名称{
}

2. final修饰一个方法时
这个方法就是最终方法，也就不能覆盖重写
修饰符 final 返回值类型 方法名称{
}
final 与 abstract 不能同时使用，矛盾

3. 局部变量
final String lover = "呈呈";
一次赋值，终生不变
如果是基本类型
final int num = 200；
如果是引用类型
final Student stu = new Student("name1")
地址不会改变，但内容可由set方法改变
stu.setName("name2");

4. 对于成员变量，若使用final关键字修饰，必须手动赋值
要么直接赋值：private final String name = "name1";
要么通过构造方法赋值，且保证类中所有构造方法都对final成员变量赋值
private final String name;
public Person(){
	name = "...";
}
public Person(String name){
	this.name = name;
}

四种权限修饰符：
					public > protected > (default)不是关键字，指什么都不写 > private
同一个类（自己）	Yes      Yes         Yes                                 Yes    同一个类都能访问    
同一个包（妻子）	Yes      Yes         Yes                                 No     同一个包下无法访问另一个类的private
不同包子类（儿子）  Yes      Yes         No                                  No     不同包的子类只能访问protect以上的父类方法
不同包非子类（生人）Yes      No          No                                  No     只能访问Public

