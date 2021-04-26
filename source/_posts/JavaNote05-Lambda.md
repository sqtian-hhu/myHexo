---
title: '''JavaNote05_Lambda'''
date: 2020-03-19 11:52:11
tags: Java基础
categories: Java
---

## Lambda表达式
3.1 函数式编程思想概述
尽量忽略面向对象的复杂语法，强调做什么，而不是以什么形式做

面向对象的思想：做一件事情，找一个能解决这个事情的对象，调用对象的方法，完成事情
函数式编程思想：只要能获取到结果，怎么做的都不重要，重视的是结果，不重视过程

<!--more-->

3.2 冗余的Runnable代码
传统写法
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
-------------------------------------------------------------------

匿名内部类简化：
public class Demo01Runnable {
	public static void main(String[] args) {
		Runnable task = new Runnable() {
			@Override
			public void run() {
				System.out.println("多任务执行");
			}
		};
		new Thread(task).start();
	}
}

--------------------------------------------------------------------
为了实现任务不得不创建了匿名内部类，但真正希望做的事情是：将run方法体内的代码传递给Thread类知晓
传递一段代码才是真正的目的
public class Demo02Lambda {
	public static void main(String[] args) {
		//使用匿名内部类的方式实现多线程
		new Thread(new Runnable() {
			@Override
			public void run() {
				System.out.println(Thread.currentThread().getName()+"多任务执行");
			}
		}).start();

		//使用Lambda表达式实现多线程
		new Thread(()-> {
				System.out.println(Thread.currentThread().getName()+"多任务执行");
			}
		).start();
	}
}
-------------------------------------------------------------------------------------
匿名内部类的好处和弊端
一方面可以帮助我们省去实现类的定义，另一方面，语法太复杂

Lambda语法中
()-> System.out.println(Thread.currentThread().getName()+"多任务执行");
()即run方法的参数,接口中抽象方法的参数列表
-> 表示将参数传递给后面的代码
后面的输出语句即业务逻辑代码,重写抽象方法的方法体

标准格式: (参数类型 参数名称) -> {代码语句}


## 有参数有返回
/*
	Lambda表达式有参数有返回值的练习
	需求:
		使用数组存储多个Person对象
		对数组中多个Person对象使用Arrays方法通过年龄进行升序排序
 */
public class Demo01Arrays {
	public static void main(String[] args) {
		//使用数组存储多个Person对象
		Person[] arr = {new Person("CC",18),
			new Person("TT",15),
			new Person("AB",8)};
		Arrays.sort(arr, new Comparator<Person>() {
			@Override
			public int compare(Person o1, Person o2) {
				return o1.getAge()-o2.getAge();
			}
		});

		//遍历数组
		for (Person p : arr) {
			System.out.println(p);
		}
		// 使用Lambda表达式简化匿名内部类,换成降序
		Arrays.sort(arr,(Person o1, Person o2) ->{
			return o2.getAge()-o1.getAge();
		});
		for (Person p : arr) {
			System.out.println(p);
		}
	}
}

-----------------------------------------------------------------
给定一个计算器Calculator接口,内含抽象方法calc可以将两个int数字相加得到和值
public interface Calculator {
	int calc(int a,int b);
}

使用Lambda标准格式调用invokeCalc方法,完成120,130的相加计算
public class Demo01Calculator {
	public static void main(String[] args) {
		//匿名内部类
		invokeCalc(10, 20, new Calculator() {
			@Override
			public int calc(int a, int b) {
				return a+b;
			}
		});

		//Lambda表达式
		invokeCalc(120, 130,(int a, int b)->{return a+b;});
	}

	public static void invokeCalc(int a,int b,Calculator c) {
		int sum = c.calc(a,b);
		System.out.println(sum);
	}
}

## Lambda省略格式
可推导可省略
可以省略的内容:
	1. (参数列表):括号中的参数列表的数据类型,可以省略不写
	2. (参数列表:括号中的参数如果只有一个,那么类型和()都可以省略
	3. {一些代码}:如果{}中的代码只有一行,无论是否有返回值,都可以省略({},return,分号)
		注意:要省略,{},return,分号就得一起省略
		
		
Lambda方法的使用前提
1. 使用Lambda必须具有接口,且要求接口中有且仅有一个抽象方法
2. 使用Lambda必须具有上下文推断,也就是方法的参数或局部变量类型必须为Lambda对应的接口类型,
   才能使用Lambda作为该接口的实例
有且仅有一个抽象方法的接口称为函数式接口


## 函数式接口
函数式接口在Java中指有且只有一个抽象方法的接口
/*
	函数式接口只有一个抽象方法，
	当然接口中可以包含其他的方法(默认,静态,私有)
	@FunctionalInterface注解,检测接口是否是一个函数式接口
 */
@FunctionalInterface
public interface MyFunctionalInterface {
	//定义一个抽象方法
	public abstract void method();
}

使用：
/*
	函数式接口的使用:一般作为方法的参数和返回值类型
 */
public class Demo {
	//定义一个方法,参数使用函数式接口
	public static void show(MyFunctionalInterface myinter) {
		myinter.method();
	}

	public static void main(String[] args) {
		//调用show方法,方法参数是一个接口,所以可以传递接口的实现类对象MyFunctionalInterfaceImpl
		//或者使用匿名内部类
		show(new MyFunctionalInterface() {
			@Override
			public void method() {
				System.out.println("使用匿名内部类重写接口中的抽象方法");
			}
		});
		//或者lambda表达式
		show(() -> System.out.println("使用Lambda表达式重写接口中的抽象方法"));
	}
}

## 函数式编程
有些场景的代码执行后，结果不一定会被使用，从而造成性能的浪费，Lambda表达式是延迟执行的，正好可以作为解决方案

性能浪费的日志案例
注：日志可以帮助我们快速定位问题，记录程序运行中的情况，以便项目的监控和优化
/*
	使用Lambda优化日志案例
	Lambda特点:延迟加载
	Lambda使用前提:存在函数式接口
 */
public class Demo02Lambda {
	//定义一个显示日志方法,参数传递等级和MessageBuilder接口
	public static void showLog(int level,MessageBuilder mb) {
		//对日志的等级进行判断
		if(level == 1){
			System.out.println(mb.builderMessage());
		}
	}

	public static void main(String[] args) {
		//定义三个日志信息
		String msg1 = "Hello";
		String msg2 = "world";
		String msg3 = "Java";
		
		//函数式接口使用Lambda
		showLog(1,()->{
			return msg1+msg2+msg3;
		});
		
		/*
			使用Lambda表达式作为参数传递,仅仅是把参数传递到showLog方法中
			只有满足条件,日志的等级是1级,才会调用接口
			才会进行字符串的拼接
			如果条件不满足
			MessageBuilder接口中的方法builderMessage就不会执行
			
		 */
	}
}
----------------------------------------------------------------------
Lambda作为参数和返回值
public class Demo01Runnable {
	//定义一个方法startThread(Runnable run)
	public static void startThread(Runnable run) {
		new Thread(run).start();
	}

	public static void main(String[] args) {
		startThread(()-> System.out.println(Thread.currentThread().getName()+"-->"+"线程启动了"));
	}
}

返回函数式接口
/*
	如果一个方法的返回值类型是一个函数式接口,那么就可以直接返回一个Lambda表达式
	当需要通过一个方法获取一个java.util.Comparator接口类型的对象作为排序器时,就可以调该方法获取

 */
public class Demo02Comparator {
	//定义一个方法,方法的返回值类型使用函数式接口Comparator
	public static Comparator<String> getComparator(){
		//方法返回值是一个函数式接口,使用Lambda
//		return (String o1, String o2)-> {
//				//降序
//				return o2.length()-o1.length();
//		};
		//进一步优化,String可以省略,大括号省略,return省略
		return (o1, o2)-> o2.length()-o1.length();
	}

	public static void main(String[] args) {
		//创建一个字符串组
		String[] arr = {"aaa","b","ccccc","dddddddddddddd"};
		//输出排序前的数组
		System.out.println(Arrays.toString(arr));//[aaa, b, ccccc, dddddddddddddd]
		//调用Arrays中的sort方法排序
		Arrays.sort(arr,getComparator());
		System.out.println(Arrays.toString(arr));//[dddddddddddddd, ccccc, aaa, b]
	}
}

## 常用的函数式接口
java.util.function
1. Supplier接口
/*
	Supplier<T>接口仅包含一个无参方法:T get() 用来获取一个泛型参数指定类型的对象数据
	Supplier<T>接口被称为生产型接口,指定接口的泛型是什么类型,那么接口中的get方法就会产生声明类型的数据

 */
public class Demo01Supplier {
	//定义一个方法,方法的参数传递Supplier<T>接口,泛型执行String,get方法就会返回一个String
	public static String getString(Supplier<String> sup){
		return sup.get();
	}

	public static void main(String[] args) {
		//调用getString方法,方法的参数Supplier是一个函数时接口
		String s = getString(()->{
			//生产一个字符串,并返回
			return "TSQ";
		});
		System.out.println(s);
	}
}

练习：
使用Supplier接口作为方法参数类型，通过Lambda表达式求出int数组中的最大值
提示：接口泛型使用Integer
public class Demo02Test {
	//定义一个方法,用于获取int数据类型数组中元素的最大值,方法参数传递Supplier接口,泛型使用Integer
	public static int getMax(Supplier<Integer> sup){
		return sup.get();
	}

	public static void main(String[] args) {
		//定义一个int类型的数组,并赋值
		int[] arr = {100,0,-50,88,99,33,-30};
		//调用getMax方法,方法的参数Supplier是一个函数式接口,可传递Lambda表达式
		int maxValue = getMax(()->{
			//获取数组的最大值,并返回
			//定义一个变量,把数组中第一个元素赋值给该变量,记录数组中元素的最大值
			int max = arr[0];
			for (int i : arr) {
				if(i>max){
					max = i;
				}
			}
			return max;
		});
		System.out.println(maxValue);
	}
}

-------------------------------------------------------------------
2. Consumer接口
Cunsumer接口是一个消费型接口，泛型执行什么类型，就可以使用accept方法消费什么类型的数据

public class Demo01Consumer {
	/*
		定义一个方法
		方法的参数传递一个字符串的姓名
		方法的参数传递Consumer接口,泛型使用String
		可以使用Consumer接口消费字符串的姓名

	 */
	public static void method(String name, Consumer<String> con) {
		con.accept(name);
	}

	public static void main(String[] args) {
		//调用method方法,传递字符串姓名,方法的另一个参数时Consumer接口
		method("西门吹雪",(String name)->{
			//对传递的字符串进行消费
			//消费方式:直接输出字符串
			System.out.println(name);
			//消费方式:把字符串进行反转输出
			String reName = new StringBuilder(name).reverse().toString();
			System.out.println(reName);
		});
	}
}

-----------------------------------------------------------------------------
默认方法：andThen
/*
	Consumer接口的默认方法andThen
	作用:需要两个Consumer接口,可以把两个Consumer接口组合到一起,再对数据进行消费
	例如:
	Consumer<String> con1
	Consumer<String> con2
	String s = "hello";
	con1.accept(s);
	con2.accept(s);
	//等同于
	con1.andThen(con2).accept(s);//谁写前面谁先消费

 */
public class Demo02AndThen {
	//定义一个方法,方法传递一个字符串和两个Consumer接口
	public static void method(String s, Consumer<String> con1,Consumer<String> con2){
		con1.accept(s);
		con2.accept(s);
		//使用andThen方法把两个接口连接到一起
		con1.andThen(con2).accept(s);
	}

	public static void main(String[] args) {
		method("Hello",(t)->{
			//消费方式,把字符串转换为大写输出
			System.out.println(t.toUpperCase());
		},(t)->{
			System.out.println(t.toLowerCase());
		});
	}
}

练习：
格式化打印信息
public class Demo03Test {
	public static void printInfo(String[] arr, Consumer<String> con1,Consumer<String> con2){
		//遍历字符串数组
		for (String message : arr) {
			con1.andThen(con2).accept(message);
		}
	}

	public static void main(String[] args) {
		//定义一个字符串类型的数组
		String[] arr = {"西门吹雪,男","绯村剑心,男","小龙女,女"};

		//调用printInfo方法,传递一个字符串数组,和两个Lambda表达式
		printInfo(arr,(message)->{
			//消费方式:对message切割,获取姓名,按照指定格式输出
			String name = message.split(",")[0];
			System.out.print("姓名:"+name);
		},(message)->{
			String age = message.split(",")[1];
			System.out.println("。性别:"+age+"。");
		});
	}
}

3. Predicate接口
对某种类型的数据进行判断
public class Demo01Predicate {
	/*
		定义一个方法
		参数传递一个String类型的字符串
		传递一个Predicate接口，泛型使用String
		使用Predicate中的方法test对字符串进行判断，并把判断的结果返回
	 */
	public static boolean checkString(String s, Predicate<String> pre) {
		return pre.test(s);
	}

	public static void main(String[] args) {
		//定义一个字符串
		String s = "abcde";
		//调用checkString方法对字符串进行校验
		boolean b = checkString(s,(String str)->{
			//对参数传递的字符串进行判断,判断字符串的长度是否大于5
			return str.length()>5;
		});
		System.out.println(b);
	}
}
-------------------------------------------------
默认方法：and
/*
	逻辑表达式:可以连接多个判断条件
	&&:与运算符,有false则false
	//:或运算符,有true则true
	!:非,取反

	需求:判断一个字符串,有两个判断条件
		1. 判断字符串的长度是否大于5
		2. 判断字符串是否包含a
		两个条件同时满足,我们可以用&&连接两个条件
		Predicate接口中有一个方法and,也可以用于连接两个判断条件

 */
public class Demo02Predicate_and {
	/*
		定义一个方法,传递一个字符串,两个Predicate接口
		一个判断条件一,一个判断条件二

	 */

	public static boolean checkString(String s, Predicate<String> pre1,Predicate<String> pre2) {
		//return pre1.test(s) && pre2.test(s);
		//等价于
		return pre1.and(pre2).test(s);
	}

	public static void main(String[] args) {
		//定义一个字符串
		String s = "abcdef";
		boolean b = checkString(s,(String str)->{
			return str.length()>5;
		},(String str)->{
			return str.contains("a");
		});
		System.out.println(b);
	}
}
--------------------------------------------------------------
默认方法：or 
或
//return pre1.test(s) || pre2.test(s);
//等价于
return pre1.or(pre2).test(s);
默认方法：negate
取反
//return !pre.test(s);
return pre.negate().test(s);

练习:
集合信息筛选
/*
	练习:集合信息筛选
	数组当中有多条"姓名+性别"的信息如下,
	String[] arr = {"西门吹雪,男","绯村剑心,男","小龙女,女","杨过,男"};
	通过Predicate接口的拼装将符合条件的字符串筛选到ArrayList中
	同时满足
	1. 男生
	2. 姓名4个字
 */
public class Demo05Test {
	public static ArrayList<String> filter(String[] arr, Predicate<String> pre1,Predicate<String> pre2){
		ArrayList<String> list = new ArrayList<>();
		for (String s : arr) {
			boolean b = pre1.and(pre2).test(s);
			if(b){
				//条件满足,信息存入list
				list.add(s);
			}
		}
		return list;
	}

	public static void main(String[] args) {
		String[] arr = {"西门吹雪,男","绯村剑心,男","小龙女,女","杨过,男"};
		ArrayList<String> list = filter(arr,(String s)->{
			return s.split(",")[0].length()==4;
		},(String s)->{
			return s.split(",")[1].equals("男");
		});

		//遍历集合
		for (String s : list) {
			System.out.println(s);
		}
	}
}

4. Function接口
用来根据一个类型的数据得到另一个类型的数据
抽象方法:R apply(T t), 根据类型T的参数获取类型R的结果
public class Demo01Function {
	/*
		定义一个方法
		方法参数传递一个字符串类型的整数
		方法的参数传递一个Function接口,泛型使用<String,Integer>
		使用Function接口中的方法apply,把字符串类型的整数转换为Integer类型的整数
	 */

	public static void change(String s, Function<String,Integer> fun){
		Integer in = fun.apply(s);
		System.out.println(in);
	}

	public static void main(String[] args) {
		//定义一个字符串类型的整数
		String s = "1234";
		//调用change方法
		change(s,(String str)->{
			//把字符串类型的整数转换为Integer类型的整数返回
			return Integer.parseInt(str);
		});
	}
}
---------------------------------------------------------------
默认方法:andThen
import java.util.function.Function;

/*
	Function接口中的默认方法andThen:用来进行组合操作
	需求:
		把String类型的"123",转换为Integer类型,把转换后的结果加10
		把增加后的Integer类型数据转换为String类型
	分析:
		转换流两次
		第一次是把String类型转换为Integer类型
			所以我们可以使用Function<String,Integer> fun1
			Integer i = fun1.apply("123")+10;
		第二次把Integer转换为String类型
			String s = fun2.apply(i);

		我们可以使用andThen方法,把两次转换组合到一起使用
		String s = fun1.andThen(fun2).apply("123");
 */
public class Demo02Function_andThen {
	public static void change(String s, Function<String,Integer> fun1, Function<Integer,String> fun2){
		String ss = fun1.andThen(fun2).apply(s);
		System.out.println(ss);
	}

	public static void main(String[] args) {
		//定义一个字符串方法
		String s = "123";
		//调用change方法
		change(s,(String str)->{
			//把字符串转换为整数+10
			return Integer.parseInt(str)+10;
		},(Integer in)->{
			//把整数转换为字符串
			return in+"";
		});
	}
}
-----------------------------------------------------------------------------
练习:
自定义函数模型拼接
/*
	练习:自定义函数模型拼接
	请使用Function进行函数模型的拼接,按照顺序需要执行的多个函数操作为
		String str = "西门吹雪,20";
	分析:
	1. 将字符串截取数字年龄部分,得到字符串;
		Function<String,String> "西门吹雪,20"->"20"
	2. 将上一步字符串转换为int类型
	Function<String,Integer> "20"->20
	3. 将上一步的int数字累加100,得到int数字
	Function<Integer,Integer> 20->120

 */
public class Demo06Test {
	public static int change(String s, Function<String,String> fun1,
							 Function<String,Integer> fun2,Function<Integer,Integer> fun3){
		//使用andThen
		return fun1.andThen(fun2).andThen(fun3).apply(s);
	}

	public static void main(String[] args) {
		String str = "西门吹雪,20";
		//调用change方法
		int num = change(str,(String s)->{
			return s.split(",")[1];
		},(String s)->{
			return Integer.parseInt(s);
		},(Integer i)->{
			return i+100;
		});
		System.out.println(num);
	}
}