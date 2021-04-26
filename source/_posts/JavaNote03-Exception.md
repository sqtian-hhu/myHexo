---
title: '''JavaNote03_Exception'''
date: 2020-03-16 14:09:39
tags: Java基础
categories: Java
---
## 异常
指执行过程出现非正常情况,Java处理异常的方式是中断处理
Throwable 异常的根类
Error: extends Throwable,不能处理只能尽量避免
Exception: extends Throwable,由于使用不当导致,可以避免

<!--more-->

// Exception:编译期异常,进行编译时出现的问题
// RuntimeException:运行期异常,运行过程中出现的问题

1. psvm throws XXXException{}
2. try{
	   //可能会出现异常的代码	
	}catch(XXXException e){
		//异常的处理逻辑
		e.printStackTrace();
	}
	

## 异常的处理
// throw关键字:在指定的方法中抛出指定的异常
使用格式:
throw new XXXException("异常产生的原因")

注意:
1. throw关键字必须写在方法的内部
2. throw关键字后面new的对象必须是Exception或者Exception的子类对象
3. throw关键字抛出指定的异常对象,我们就必须处理这个异常对象
	throw关键字后面创建的是RuntimeException或其子类对象时我们可以不处理,默认交给JVM处理(打印异常对象,中断程序)
	throw关键字后面创建的是编译异常(写代码的时候报错),我们就必须处理这个异常,要么throw,要么try...catch

public class Demo03Throw {
	/*
		定义一个方法,获取数组指定索引处的元素
		参数:
		int[] arr
		int index
		我们首先必须对方法传递过来的参数进行合法性校验
		如果参数不合法,那么我们就必须使用抛出异常的方式,告知方法调用者,传递的参数有问题

		NullPointerException是一个运行期异常,我们不用处理,默认交给JVM处理

	 */
	public static void main(String[] args) {
//		int[] arr = null;
//		getElement(arr,0);
		int[] arr1 = new int[3];
		getElement(arr1, 3);

	}

	public static int getElement(int[] arr, int index) {
		/*
			对传递过来的参数进行合法性校验
			如果arr时null,我们就抛出空指针异常,告知调用者,"传递的数组的值是null"

		 */
		if (arr == null) {
			throw new NullPointerException("传递的数组的值是null");

		}

		/*
			如果index的范围不在数组索引范围类,就抛出数组索引越界异常,
			告知方法的调用者"传递的索引超出了数组的使用范围"
		 */
		if (index < 0 || index > arr.length - 1) {
			throw new ArrayIndexOutOfBoundsException("传递的索引超出了数组的使用范围");
		}

		int ele = arr[index];
		return ele;
	}
}


// Object非空判断
Objects类中的静态方法
public static <T> T requireNonNull(T obj):查看指定引用对象不是null
public static int getElement(int[] arr, int index) {
		//不用自己写校验合法性的代码,可以简化代码
		Objecs.requireNonNull(obj,"传递的数组的值是null");

		int ele = arr[index];
		return ele;
	}
	
	
	
// throws关键字: 异常处理的第一种方式,交给别人处理
	作用:
		当方法内部抛出异常对象的时候,我们必须处理这个异常对象
	使用格式:
	修饰符 返回值类型 方法名(参数列表) throws AAAException{ton,BBBException{ton...{
	throw new AAAException("产生原因");
	throw new BBBException("产生原因");
	...
	}
	
	
public class Demo05Throws {
	public static void main(String[] args) throws IOException {
		readFile("c:\\a.txd");
		readFile("d :\\a.txt");


	}
	/*
		定义一个方法,对传递的文件路径进行合法判断
		如果路径不是"c:\\a.txt",就抛出文件找不到异常对象
		注意:FileNotFoundException是编译异常,抛出编译异常就必须处理
			 可以使用throws继续声明抛出FileNotFoundException这个异常对象,让方法调用者处理
			 FileNotFoundException extends IOException,直接声明父类异常即可
	 */
	public static void readFile(String filename) throws IOException {
		if(!("c:\\a.txt").equals(filename)){
			throw new FileNotFoundException("传递路径错误");
		}
		/*
			如果传递路径不是.txt结尾
			那么我们就抛出IO异常对象,告知方法的调用者,文件后缀名不对

		 */
		if(!filename.endsWith(".txt")){
			throw new IOException("文件后缀名错误");
		}
		System.out.println("路径正确,读取文件");

	}
}


//  try...catch:异常处理的第二种方式,自己处理异常
	格式:
		try{
			可能产生异常的代码
		}catch(定义一个异常的变量,用来接受try中抛出的异常对象){
			异常的处理逻辑,怎么处理异常对象
			一班会把异常信息记录到一个日志中
		}
		...
		catch(异常类名 变量名){
		}
		
	注意:
		1. try中可能会抛出多个异常对象,那么就可以使用多个catch来处理这些异常对象
		2. 如果try中产生了异常,那么就会执行catch中的异常处理逻辑,执行完毕catch中的处理逻辑,继续执行try...catch后面的代码
		   如果try中没有产生异常,那么就不会执行catch中异常的出路逻辑,执行完try中代码,继续执行try...catch后面的代码
		   

public class Demo01TryCatch {
	public static void main(String[] args) {
		try{
			readFile("c:\\a.txd");
		}catch(IOException e){// try中抛出什么异常对象,catch就定义什么异常变量,用来接收这个异常对象
			System.out.println("文件后缀名错误");
		}
		System.out.println("后续代码");
	}
	public static void readFile(String filename) throws IOException {

		if(!filename.endsWith(".txt")){
			throw new IOException("文件后缀名错误");
		}
		System.out.println("路径正确,读取文件");
	}
}


// Throwable类中的3个异常处理方法
String getMessage() 返回此throwable的简短描述
String toString() 返回此 throwable 的详细消息字符串
void printStackTrace() JVM打印异常对象,默认此方法,异常消息最全面

		try{
			readFile("c:\\a.txd");
		}catch(IOException e){// try中抛出什么异常对象,catch就定义什么异常变量,用来接收这个异常对象
//			System.out.println("文件后缀名错误");
//			System.out.println(e.getMessage());//文件后缀名错误
//			System.out.println(e.toString());//java.io.IOException: 文件后缀名错误
			e.printStackTrace();//最全面
		}		
	


// finally代码块
		try{
			可能产生异常的代码
		}catch(定义一个异常的变量,用来接受try中抛出的异常对象){
			异常的处理逻辑,怎么处理异常对象
			一班会把异常信息记录到一个日志中
		}
		...
		catch(异常类名 变量名){
		}finally{
			无论是否出现异常都会执行
		}
		注意:
			1. finally不能单独使用,必须和try一起使用
			2. finally一般用于资源释放(资源回收),无论程序是否出现异常,最后都要资源释放
			
	public static void main(String[] args) {
		try{
			readFile("c:\\a.txd");
		}catch(IOException e){
			e.printStackTrace();
		}finally{
			System.out.println("资源释放");
		}
	}
	
	如果finally有return语句,永远返回finally中的结果,避免该情况,所以尽量不要在finally里面写return
	
// 多异常的捕获处理
1. 多个异常分别处理:
	有一个异常用一次try...catch
2. 多个异常一次捕获,多次处理
	一个try多个catch
	注意:catch里面定义的异常变量,如果有子父类关系,那么子类的异常变量必须写在上面,否则会报错
3. 多个异常一次捕获一次处理
	catch父类
	try{}catch(Exception e){}
	
// 子父类异常
如果父类抛出了多个异常,子类重写父类方法时,抛出和父类相同的异常或者是父类异常的子类或者不抛出异常
父类方法没有抛出异常,子类重写父类该方法时也不可以抛出异常,此时子类产生该异常,只能捕获处理,不能声明抛出
注意: 父类异常时什么样,子类异常就什么样
public class Fu {
	public void show01() throws NullPointerException,ClassCastException{}
	public void show02() throws IndexOutOfBoundsException{}
	public void show03() throws IndexOutOfBoundsException{}
	public void show04(){}
	public void show05(){}

}
class Zi extends Fu{
	//重写父类方法时抛出和父类相同的异常
	public void show01() throws NullPointerException,ClassCastException{}
	//或者抛出父类异常的子类
	public void show02() throws ArrayIndexOutOfBoundsException{}
	//或者不声明异常
	public void show03(){}

	//父类没有抛出异常,子类也不能抛出异常
	public void show04(){}
	//如果产生该异常,只能捕获处理,try...catch
	public void show05(){
		try {
			throw new Exception("编译器异常");
		} catch (Exception e) {
			e.printStackTrace();
		}
	}

}


## 自定义异常
自定义一个注册异常
/*
	自定义异常类
	格式:
	public class XXXException extends Exception/RuntimeException{
		添加一个空参数的构造方法
		添加一个带异常信息的构造方法
	}
	注意:
		1. 自定义异常类一班都是以Exception结尾,说明是一个异常类
		2. 必须继承Exception/RuntimeException
 */
public class RegisterException extends Exception {
	//添加一个空参数的构造方法

	public RegisterException() {
		super();
	}

	/*
		添加一个带异常信息的构造方法
		所有异常类都会有一个带异常信息的构造方法,方法内部会调用父类异常信息的构造方法,让父类来处理这个异常信息
	 */
	public RegisterException(String message) {
		super(message);
	}
}



/*
	要求:模拟注册操作,如果用户名已注册,则抛出异常,并提示:该用户名已被注册
	分析:
	1. 使用数组保存已经注册过的用户名(数据库)
	2. 使用Scanner获取用户输入的注册用户名
	3. 定义一个方法,对用户输入中注册的用户名进行判断
		遍历存储已经注册过用户名的数组,获取一个用户名
		使用获取到的用户名和用户名比较
		true:已存在,抛出异常
		false:继续遍历比较
		循环结束没有找到重名,注册成功

 */
public class Demo01RegisterException {
	//1. 使用数组保存已经注册过的用户名
	static String[] usernames = {"张三","李四","王八"};

	public static void main(String[] args) throws RegisterException {
		//2. Scanner获取
		Scanner sc = new Scanner(System.in);
		System.out.println("请输入要注册的用户名:");
		String username = sc.next();
		checkUsername(username);
	}

	//3. 定义判断方法
	public static void checkUsername(String username) throws RegisterException {
		//遍历存储用户名
		for (String name : usernames) {
			if(name.equals(username)){
				throw new RegisterException("该用户名已被注册");
			}
		}
		System.out.println("注册成功");
	}
}



