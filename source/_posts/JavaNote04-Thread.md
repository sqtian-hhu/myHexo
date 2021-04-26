---
title: '''JavaNote04_Thread'''
date: 2020-03-18 15:57:43
tags: Java基础
categories: Java
---
## 多线程
1. 并发与并行
并发：指两个或多个事件在同一时间段内发生
	  交替执行,一个人吃两个馒头
并行：指两个或多个事件在同一时刻发生(同时发生)
	  同时执行,两个人一人一个个馒头

<!--more-->
	  
2. 线程与进程
进程:一个内存中运行的应用程序

硬盘:永久存储ROM
内存:所有应用程序都需要进入内存中执行,临时存储RAM
点击应用程序执行,进入到内存中占用一些内存执行,进入到内存的程序叫进程
结束进程,就把进程从内存中清除了

一个程序运行后至少有一个进程,一个进程可以包含多个线程
CPU:中央处理器,对数据进行计算,指挥电脑中的软件和硬件工作
4核8线程
8线程:同时执行8个任务
点击电脑管家运行,会进入到内存中,就是一个进程.点击功能(病毒查杀,清理垃圾,电脑加速)执行,
就会开启一条应用程序到cpu的执行路径,cpu就可以通过这个路径执行功能,这个路径就叫线程
线程属于进程,是进程中的一个执行单元,负责程序的执行

单核心单线程CPU:cpu在多个线程之间做高速的切换,轮流执行多个线程,效率低
4核心8线程:同时执行8个线程,8个线程在多个任务之间高速切换,效率提高了8倍

线程调度:
分时调度:所有线程轮流使用CPU,平均分配每个线程占用CPU的时间
抢占式调度:优先让优先级高的线程占用

3. 创建线程类
主线程: 执行main方法的线程
单线程程序:Java程序中只有一个线程,执行从main开始,从上到下依次执行
public class Demo01MainThread {
	public static void main(String[] args) {
		Person p1 = new Person("小强",10);
		p1.run();//单线程从上而下,p2全打印完了才继续运行p2,如果p1出现了异常,p2就运行不了了
		Person p2 = new Person("旺财",8);
		p2.run();
	}
}


//1.创建一个Thread类的子类
public class MyThread extends Thread {
	//2. 在Thread类的子类中重写Thread类中的run方法,设置线程任务

	@Override
	public void run() {
		for (int i = 0; i < 10000; i++) {
			System.out.println("run:" + i);
		}
	}
}

/*
	创建多线程程序的第一种方式:创建Thread类的子类
	实现步骤:
		1. 创建一个Thread类的子类
		2. 在Thread类的子类中重写Thread类的run方法,设置线程任务(开启线程要做什么)
		3. 创建Thread类的子类对象
		4. 调用Thread类中的方法start方法,开启新的线程,执行run方法
			void start() 使线程开始执行; Java 虚拟机调用该线程的run方法
			结果是两个线程并发的运行当前线程(main线程)和另一个线程(创建的新线程,执行其run方法)
			多次启动一个线程是非法的,特别是当线程已经结束执行后,不能再重新启动
		java程序属于抢占式调度,哪个线程优先级高,哪个线程优先执行,同一优先级随机选择一个执行

 */
public class Demo01Thread {
	public static void main(String[] args) {
		//3. 创建Thread类子类对象
		MyThread mt = new MyThread();
		//4. 调用start
		mt.start();

		//主线程
		for (int i = 0; i < 10000; i++) {
			System.out.println("main:" + i);
		}
	}
}


## 多线程原理
JVM执行main方法,找OS开辟一条main方法通向cpu的路径,这个路径叫main线程,主线程
cpu通过这个线程,这个路径可以执行main方法

mythread.start() 执行run方法,开辟一条通向cpu的新路径,用来执行run方法
对于cpu而言,就有了两条执行的路径,cpu就有了选择的权力,cpu随机选择

start开辟新的栈空间,执行run方法,多个栈的线程互不影响


## Thread类的常用方法

获取线程名称：
1. 使用Thread类中的方法getName()
	String getName() 返回该线程的名称
2. 可以先获取到当前正在执行的线程,使用线程方法getName()获取当前线程名称
		current Thread() 
		
		
设置线程名称:
1. 使用Thread类中的方法setName
2. 创建一个带参数的构造方法,参数传递线程的名称;调用父类的带参构造,把线程名称传递给父类,让父类给子线程起一个名字

//1.创建一个Thread类的子类
public class MyThread extends Thread {
	//2. 在Thread类的子类中重写Thread类中的run方法,设置线程任务

	public MyThread() {
	}

	public MyThread(String name) {
		super(name);//
	}

	@Override
	public void run() {
		//获取线程名称
//		String name = getName();
//		System.out.println(name);//Thread-0

//		Thread t = Thread.currentThread();
//		System.out.println(t);//Thread[Thread-0,5,main]
//		String name = t.getName();
//		System.out.println(name);//Thread-0
		System.out.println(Thread.currentThread().getName());
		
	}
}
-----------------------------------------------------------------------
/*
	线程的名称：
	主线程：main
	新线程：Thread-0，Thread-1...
 */
public class Demo01GetThreadName {
	public static void main(String[] args) {
		//创建Thread子类对象
		MyThread mt = new MyThread();
		//1. 直接设置线程名称
		mt.setName("CC");
		mt.start();

		//2. 父类构造方法设置名称
		new MyThread("TT").start();
	}
}



sleep方法
使当前正在执行的线程以指定的毫秒数暂停
毫秒数结束后,线程继续执行

public class Demo01Sleep {
	public static void main(String[] args) {
		//模拟秒表
		for (int i = 0; i < 60; i++) {
			System.out.println(i);
			//使用Thread静态方法sleep睡眠一秒钟
			try {
				Thread.sleep(1000);
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
		}
	}
}


## 创建多线程的另一种方式
实现Runnable接口
Runnable接口应该由那些打算通过某一线程执行其实例的类来实现,类必须定义一个称为run的无参方法
实现步骤:
1. 创建一个Runnable接口的实现类
2. 在实现类中重写Runnable接口的run方法,设置线程任务
3. 创建一个Runnable接口的实现类对象
4. 创建Thread类的对象,构造方法中传递Runnable接口的实现类对象
5. 调用Thread类中的方法start方法,开启新的线程

public class RunnableImpl implements Runnable{

	@Override
	public void run() {
		for (int i = 0; i < 20; i++) {
			System.out.println(Thread.currentThread().getName()+"-->"+i);
		}
	}
}
------------------------------------------------------------------------------
public class Demo01Runnable {
	public static void main(String[] args) {
		RunnableImpl run = new RunnableImpl();
		Thread t = new Thread(run);
		t.start();
		for (int i = 0; i < 20; i++) {
			System.out.println(Thread.currentThread().getName()+"-->"+i);
		}
	}
}


## Thread和Runnable的区别
实现Runnable接口创建多线程的好处:
	1. 避免了单继承的局限性
		一个类只能继承一个类,类继承了Thread类就不能继承其他的类了
		实现Runnable接口,还可以继承其他的类,实现其他接口
	2. 增强了程序的扩展性,降低了程序的耦合性
		实现Runnable接口的方式,把设置线程任务和开启新线程进行了分离
		实现类中,重写了run方法:用来设置线程任务
		创建Thread类对象,调用start方法:用来开启新线程
		Thread t = new Thread(run);分离后可以传递不同的Runnable接口的实现类对象

尽量通过Runnable接口的形式

## 匿名内部类的方式创建
把子类继承父类,重写父类方法,创建子类对象合一步完成
把实现类实现接口,重写接口中的方法,创建实现类对象合成一步完成

匿名内部类最终产物:子类对象/实现类对象,而这个类没有名字

格式:
	new 父类/接口(){
		重写方法
	};


public class Demo01InnerClassThread {
	public static void main(String[] args){
		//线程的父类是Thread
		// new MyThread().start();

		//匿名内部类
		new Thread(){
			@Override
			public void run() {
				for (int i = 0; i < 20; i++) {
					System.out.println(Thread.currentThread().getName()+"-->"+i+"TT");
				}
			}
		}.start();

		//接口方式
		//Runnable r = new RunnableImpl();//多态

		//匿名内部类
		Runnable r = new Runnable(){

			@Override
			public void run() {
				for (int i = 0; i < 20; i++) {
					System.out.println(Thread.currentThread().getName()+"-->"+i+"CC");
				}
			}
		};

		new Thread(r).start();

		new Thread(new Runnable(){

			@Override
			public void run() {
				for (int i = 0; i < 20; i++) {
					System.out.println(Thread.currentThread().getName()+"-->"+i+"AB");
				}
			}
		}).start();
	}
}



## 线程安全问题

卖票案例:
1. 只有一个窗口卖1-100号票,单线程,线程安全
2. 三个窗口,分别卖1-33,34-66,67-100,多线程程序,没有访问共享数据,也不会出现问题
3. 三个窗口,都在卖1-100号,多线程访问了共享数据,就会出现线程安全问题

public class RunnableImpl implements Runnable{
/*
 	实现卖票案例

 */

	//定义一个多线程共享的票源
	private int ticket = 100;
	//设置线程任务:卖票

	@Override
	public void run() {
		//使用循环,让卖票重复进行
		while(ticket>0){
			//票存在,卖票
			System.out.println(Thread.currentThread().getName()+"正在卖第"+ ticket +"张票");
			ticket--;
		}
	}
}
--------------------------------------------------------------------------------------
/*
	模拟卖票
	创建3个线程,同时开启,对共享的票进行出售
 */

public class Demo01Ticket {
	public static void main(String[] args) {
		//创建Runnable接口的实现类对象
		RunnableImpl run = new RunnableImpl();
		Thread t1 = new Thread(run);
		Thread t2 = new Thread(run);
		Thread t3 = new Thread(run);
		t1.start();
		t2.start();
		t3.start();
	}
}

## 解决线程安全问题
运用同步机制
!. 同步代码块
2. 同步方法
3. 锁机制


1. 同步代码块
public class RunnableImpl implements Runnable{
/*
 	实现卖票案例
 	出现了线程安全问题
 	卖出了不存在的票和重复的票
 	使用同步代码块解决
 	格式:
 		synchronized(锁对象){
 			可能会出现线程安全问题的代码块
 		}
 	注意:
 		1. 通过代码块中的锁对象,可以使用任意的对象
 		2. 但是必须保证多个线程使用的锁对象是同一个
 		3. 锁对象作用:把同步代码块锁住,只让一个线程在同步代码块中执行


 */

	//定义一个多线程共享的票源
	private int ticket = 100;
	//设置线程任务:卖票

	//创建一个锁对象
	Object obj = new Object();

	@Override
	public void run() {
		//使用循环,让卖票重复进行
		while(true){
			synchronized(obj){
				if(ticket > 0){
					//票存在,卖票
					System.out.println(Thread.currentThread().getName()+"正在卖第"+ ticket +"张票");
					ticket--;
				}

			}
		}
	}

--------------------------------------------------------------------------------------------------

同步技术的原理:
使用了一个锁对象,这个锁对象叫做同步锁,也叫对象锁,也叫对象监视器
3个线程一起抢夺cpu执行权,谁抢到了谁执行run方法进行卖票,
t0抢到了执行权,执行run方法,遇到synchronized代码块,
这是t0会检查synchronized代码块是否有锁对象,
发现有,就会获取到锁对象,进入到同步中执行
t1抢到了cpu的执行权,遇到synchronized代码块,
这是t1检查锁对象,发现没有,t1就会进入阻塞状态,会一直等待t0归还锁对象
t0执行完同步代码块中的代码,才会归还锁对象


2. 同步方法解决
使用步骤:
1. 把访问了共享数据的代码抽取出来,放到一个方法中
2. 在方法上添加synchronized修饰符

格式: 定义方法的格式
修饰符 synchronized 返回值类型 方法名(参数列表){
	可能出现线程安全问题的代码(共享数据的代码)
}


@Override
	public void run() {
		payTicket();
	}

	
	/*
		定义一个同步方法
		同步方法也会把方法内部的代码锁住
		只让一个线程执行
		同步方法的锁对象就是实现类对象 new RunnableImpl()
		也就是this
	 */
	public synchronized void payTicket(){
		//使用循环,让卖票重复进行
		while(ticket > 0){
			synchronized(obj){
				//票存在,卖票
				System.out.println(Thread.currentThread().getName()+"正在卖第"+ ticket +"张票");
				ticket--;
			}
		}
	}
	

静态同步方法

public class RunnableImpl implements Runnable{
	//静态访问静态
	private static int ticket = 100;
	//设置线程任务:卖票

	@Override
	public void run() {
		payTicket();
	}
	public static synchronized void payTicket(){
		//使用循环,让卖票重复进行
		while(ticket > 0){
			synchronized(obj){
				//票存在,卖票
				System.out.println(Thread.currentThread().getName()+"正在卖第"+ ticket +"张票");
				ticket--;
			}
		}
	}
	
静态方法的锁对象不能是this，this是创建对象后产生的，静态方法优先于对象
静态方法的锁对象是本类的class属性 --> class文件对象 RunnableImpl.class

3. Lock锁
Lock接口可获得更广泛的锁定结构
lock获取锁
unlock释放锁
		/*
			第三种方案:Lock锁
			ReentrantLock implements Lock 接口
			使用步骤:
				1. 在成员位置创建一个ReentrantLock对象
				2. 在可能出现安全问题的代码前调用Lock接口中的lock方法获取锁
				3. 在可能出现安全问题的代码后调用Lock接口中的unlock方法释放锁

		 */
	
	Lock l = new Reentrant Lock();

	@Override
	public void run() {
		//使用循环,让卖票重复进行
		l.lock();
		while(ticket > 0){
			synchronized(obj){
					//票存在,卖票
					System.out.println(Thread.currentThread().getName()+"正在卖第"+ ticket +"张票");
					ticket--;
			}
		}
		l.unlock();
	}
	
	
如果try...catch异常,把unlock放在finally语句里,无论程序是否异常,都会把锁释放
	@Override
	public void run() {
		//使用循环,让卖票重复进行
		l.lock();
		while(ticket > 0){
		
			try {
				Thread.sleep(1000);
				//票存在,卖票
					System.out.println(Thread.currentThread().getName()+"正在卖第"+ ticket +"张票");
					ticket--;
			} catch (InterruptedException e) {
				e.printStackTrace();
			}finally{
				l.unlock();
			}	
		}
	}
	
	
	
## 线程状态
NEW 新建状态 
BLOCKED 阻塞状态  RUNNABLE 运行状态
TIMED_WAITING 休眠状态  WAITING 无限等待状态
TERMINATED 死亡状态

TIMED_WAITING 休眠状态  sleep
BLOCKED 阻塞状态    lock

WAITING 无限等待状态 
一个正在无限等待另一个线程执行一个特别的唤醒动作的线程处于这一状态
卖包子案例：
顾客要买包子，和老板说明买包子的数量和种类，顾客就等着老板做包子(调用wait方法) Waiting状态 无限等待


老板开始做包子,做好包子告诉顾客(调用notify方法)

/*
	等待唤醒案例：线程之间的通信
		创建一个顾客线程（消费者）：告知老板要的包子种类和数量，调用wait，放弃cpu执行，进入无限等待
		创建一个老板线程（生产者）：花了5秒做包子，做好包子后调用notify唤醒顾客
	注意：
		顾客和老板线程必须使用同步代码包裹起来，保证等待和唤醒只能有一个执行
		同步使用的锁对象必须保证唯一
		只有锁对象才能调用wait和notify方法
	Object类中的方法
	void wait()
		在其他线程调用此对象的notify()方法或notifyAll()方法前,导致当前线程等待
	void notify()
		唤醒在此对象监视器上等待的单个线程
		会继续执行wait方法之后的代码

 */
public class Demo01WaitAndNotify {
	public static void main(String[] args) {
		//创建锁对象保证唯一
		Object obj = new Object();
		//创建一个顾客线程
		new Thread(){
			@Override
			public void run() {
				//保证等待和唤醒的线程只能有一个执行,需要使用同步技术
				synchronized (obj){
					System.out.println("告知老板包子的种类和数量");
					//调用wait方法
					try {
						obj.wait();
					} catch (InterruptedException e) {
						e.printStackTrace();
					}
					//唤醒之后执行的代码
					System.out.println("包子做好了,真香");
				}
			}
		}.start();


		//创建一个老板线程
		new Thread(){
			@Override
			public void run() {
				//花了五秒做包子
				try {
					Thread.sleep(5000);
				} catch (InterruptedException e) {
					e.printStackTrace();
				}

				//保证等待和唤醒的线程只能有一个执行,需要使用同步技术
				synchronized (obj){
					//调用notify唤醒顾客
					System.out.println("唤醒顾客");
					obj.notify();
				}
			}
		}.start();
	}
}


进入TimeWaiting有两种方式
	1. 使用sleep(Long m)方法,计时结束后自动进入Runnable/Blocked状态
	2. 使用wait(Long m)方法,毫秒结束后如果还没有被唤醒,自动进入Runnable/Blocked状态
	
唤醒的方法:
	notify() 唤醒单个线程,如果有多个,随机唤醒一个
	notifyAll() 唤醒监视器上等待的所有线程
		
		
## 等待唤醒机制
线程间通信
多个线程在处理同一个资源,但是处理的动作(线程的任务)却不相同
比如:线程A生产包子,线程B吃包子,包子是同一资源,但是线程A线程B处理的动作不同,就存在线程通信问题


包子案例实现:

public class BaoZi {
	//皮
	String pi;
	//馅
	String xian;
	//包子的状态
	boolean flag = false;
}
--------------------------------------------------------------------

/*
	包子铺线程和顾客线程关系-->通信(互斥)
	必须使用同步技术保证两个线程只有一个执行
	锁对象必须保证唯一,可以使用包子对象作为锁对象
	包子铺类和顾客类就需要把包子对象作为参数传递进来
		1. 需要在成员位置创建一个包子变量
		2. 使用带参数构造方法,为包子变量赋值
 */
public class BaoZiPu extends Thread{
	private BaoZi bz;

	public BaoZiPu(BaoZi bz) {
		this.bz = bz;
	}

	//设置线程生产包子
	@Override
	public void run() {
		int count = 0;
		//让包子一直生产到100个包子
		while(count<100){
			//同步技术
			synchronized (bz){
				if(bz.flag == true){
					//包子铺调用wait方法进入等待状态
					try {
						bz.wait();
					} catch (InterruptedException e) {
						e.printStackTrace();
					}
				}
				//被唤醒后生产包子
				//交替生产两种包子
				if(count%2 == 0){
					//生产薄皮三鲜馅儿
					bz.pi = "薄皮";
					bz.xian = "三鲜馅儿";
				}else{
					//生产冰皮牛肉大葱馅儿
					bz.pi = "冰皮";
					bz.xian = "牛肉大葱馅儿";
				}
				count++;

				System.out.println("包子铺正在做"+bz.pi+bz.xian+"的包子");
				//做一个包子五秒钟
				try {
					Thread.sleep(5000);
				} catch (InterruptedException e) {
					e.printStackTrace();
				}
				//做好包子,flag改成有
				bz.flag = true;
				bz.notify();
				System.out.println("包子铺做好了"+bz.pi+bz.xian+"的包子");
		}

		}
	}
}


-------------------------------------------------------------------------------------


public class ChiHuo extends Thread {
	private BaoZi bz;

	public ChiHuo(BaoZi bz) {
		this.bz = bz;
	}

	@Override
	public void run() {
		int count=0;
		while(count<100){
			synchronized (bz){
				if(bz.flag == false){
					try {
						bz.wait();
					} catch (InterruptedException e) {
						e.printStackTrace();
					}
				}

				//被唤醒后吃包子
				System.out.println("顾客在吃"+bz.pi+bz.xian+"的包子");
				try {
					Thread.sleep(3000);
				} catch (InterruptedException e) {
					e.printStackTrace();
				}
				//吃完包子修改状态为没有
				bz.flag = false;
				count++;
				//唤醒包子铺继续生产
				bz.notify();
				System.out.println("顾客把"+bz.pi+bz.xian+"的包子吃完了");
				System.out.println("--------------------------------------");
			}
		}
	}
}

-------------------------------------------------------------------------------------

public class Demo {
	public static void main(String[] args) {
		//创建包子对象
		BaoZi bz = new BaoZi();
		//创建包子铺线程,开始生产包子
		new BaoZiPu(bz).start();
		//创建顾客线程,开始吃包子
		new ChiHuo(bz).start();
	}
}




## 线程池

我们使用线程的时候就去创建一个线程，这样实现起来非常简便，但是存在一个问题：
如果并发线程数量很多，并且每一个线程都执行一个时间很短的任务就结束了，
这样频繁创建线程会大大降低系统的效率，
需要一种办法使线程可以复用，执行完一个任务，并不被销毁，而是可以继续执行其他的任务

线程池：一个容器-->集合(ArrayList,HashSet,LinkedList<Thread>,HashMap)

当程序第一次启动的时候,创建多个线程,保存到一个集合中,
当我们想要使用线程的时候,就可以从集合中取出来线程使用
Thread t = list.remove(0):返回的使被移除的元素,线程只能被一个任务使用
或者 Thread t = linked.removeFirst();
当我们使用完毕线程,需要把线程归还给线程池
list.add(t);
或者linked.addLast(t);

JDK1.5之后,JDK内置了线程池,可以直接使用

//2. 创建一个类,实现Runnable接口,重写run方法,设置线程任务
public class RunnableImpl2 implements Runnable{

	@Override
	public void run() {
		System.out.println(Thread.currentThread().getName()+"创建一个新的线程执行");
	}
}
------------------------------------------------------------------------------------------------

import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

/*
	java.util.concurrent.Executor: 线程池的工厂类,用来生成线程池
	Executors类中的方法:
		static ExecutorService newFixed Thread Pool(int nThreads) 创建一个可重用固定线程数的线程池
		参数:
			int nThreads:创建线程池中包含的线程数量
			返回值:
				ExecutorService接口,返回的是ExecutorService接口的实现类对象,我们可以使用ExecutorService接口接收(面向接口编程)
	java.util.concurrent.ExecutorService:线程池接口
		用来从线程池中获取线程,调用start方法执行线程任务
			submit(Runnable task)提交一个 Runnable 任务用于执行
		关闭线程池的方法: void shutdown()
	线程池的使用步骤:
		1. 使用线程的工厂类Executors里提供的静态方法newFixedThreadPool生产一个指定线程数量的线程池
		2. 创建一个类,实现Runnable接口,重写run方法,设置线程任务
		3. 调用ExecutorService中的方法submit,传递线程任务(实现类),开启线程,执行run方法
		4. 调用ExecutorService中的方法shutdown销毁线程池(不建议执行)

 */
public class Demo01ThreadPool {
	public static void main(String[] args) {
		//1. 使用线程的工厂类Executors里提供的静态方法newFixedThreadPool生产一个指定线程数量的线程池
		ExecutorService es = Executors.newFixedThreadPool(2);
		//3. 调用ExecutorService中的方法submit,传递线程任务(实现类),开启线程,执行run方法
		es.submit(new RunnableImpl2());//pool-1-thread-1创建一个新的线程执行
		es.submit(new RunnableImpl2());//pool-1-thread-2创建一个新的线程执行
		es.submit(new RunnableImpl2());//pool-1-thread-1创建一个新的线程执行
		es.shutdown();
	}
}

-----------------------------------------------------------------------------------------------------------


