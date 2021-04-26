---
title: '''JavaNote10_Reflect'''
date: 2020-04-02 21:12:58
tags: Java基础
categories: Java
---
## Junit单元测试
测试分类：
	1. 黑盒测试：不需要写代码，给输入值，看程序是否能够输出期望的值
	
	2. 白盒测试：需要写代码，关注程序具体的执行流程

<!--more-->
	
junit使用：白盒测试
	步骤：
	1.定义一个测试类（测试用例）
		建议：
		测试类名：被测试的类名XXXXTest   
		包名：XXX.XXX.XX.test
	2. 定义测试方法：可以独立运行
		建议：
		方法名：test测试的方法名 testAdd()
		返回值: void
		参数列表: 空参
	3. 给方法加@Test
	4. 导入junit依赖环境
	
	判定结果: 红色失败,绿色成功
	一般使用断言操作来处理结果
	
	补充:
		@Before
			修饰的方法会在测试方法之前自动被执行
		@After
			修饰的方法会在测试方法之后自动被执行
		
		
public class CalculatorTest {
	/*
		初始化方法
		用于资源申请,所有测试方法在执行之前都会先执行该方法
	 */
	@Before
	public void init(){
		System.out.println("init...");
	}
	/*
		释放资源方法
		在所有测试方法执行完后,都会自动执行该方法
	 */
	@After
	public void close(){
		System.out.println("close");
	}
	/*
		测试add方法
	 */
	@Test
	public void testAdd(){
		Calcaulator c = new Calcaulator();
		int result = c.add(1,2);
//		System.out.println(result);

		//看的是颜色而不是输出结果
		//所以一般不会输出,而是断言
		Assert.assertEquals(3,result);
	}
	@Test
	public void testSub(){
		Calcaulator c = new Calcaulator();
		int result = c.sub(1,2);
		Assert.assertEquals(-1,result);
	}
}
---------------------------------------------------------


## 反射: 框架设计的灵魂
框架: 半成品软件,可以在框架的基础上进行软件开发,简化编码
反射: 将类的各个组成部分封装为其他对象,这就是反射机制
	好处:
	1. 可以在程序运行过程中,操作这些对象
	2. 可以解耦,提高程序的可扩展性
	
	
	
Java代码在计算机中经历三个阶段
Source源代码阶段 .class字节码文件
Class类对象阶段 把类的各个组成部分封装成Class类对象
Runtime运行时阶段 new 

获取Class对象的方式:
1. Class.forName("全类名"): 将字节码文件加载进内存,返回Class对象
	多用于配置文件,将类名定义在配置文件中.读取文件,加载类
2. 类名.class: 通过类名的属性class来获取
	多用于参数的传递
3. 对象.getClass(): getClass()方法在Object对象中
	多用于对象的获取字节码方式

-------------------------------------------------------------------
public class ReflectDemo01 {
	public static void main(String[] args) throws Exception {
		//1. Class.forName("全类名")
		Class cls1 = Class.forName("Note.Person");
		System.out.println(cls1);//class Note.Person

		//2. 类名.class
		Class cls2 = Person.class;
		System.out.println(cls2);

		//3. 对象.getClass()
		Person p = new Person();
		Class cls3 = p.getClass();
		System.out.println(cls3);

		//== 比较三个对象, 说明是同一个对象
		System.out.println(cls1 == cls2);//true
		System.out.println(cls3 == cls2);//true
	}
}

结论:
	同一个字节码文件(*.class)在一次程序运行过程中,只会被加载一次,不论通过哪一种方式获取的Class对象都是同一个
	
	
使用Class对象
Class对象功能:
	获取功能:
	1. 获取成员变量们
		Field[] getFields(): 只能获取所有public修饰的成员变量
		Field getField(String name) 获取指定名称的成员变量
		
		Field[] getDeclaredFields(): 获取所有的成员变量,不考虑修饰符
		Field getDeclaredField(String name)
		
		Field:成员变量
		操作: 1. 设置值 set 2. 获取值 get 3. 忽略访问权限修饰符的安全检查 setAccessible(true): 暴力反射
		public class ReflectDemo02 {
	//1. 获取Person的Class对象
	public static void main(String[] args) throws Exception {
		Class personClass = Person.class;
		//获取成员变量
		//1. Field[] getFields()
		Field[] fields = personClass.getFields();
		for (Field field : fields) {
			System.out.println(field);
		}
		//2. Field getField(String name) 获取指定名称的成员变量
		Field a = personClass.getField("a");

		Person p = new Person();
		//设置成员变量的值
		a.set(p,"RNG");

		//获取成员变量的值
		Object value = a.get(p);
		System.out.println(value);//RNG

		//3. Field[] getDeclaredFields(): 获取所有的成员变量,不考虑修饰符
		Field[] declaredFields = personClass.getDeclaredFields();
		for (Field declaredField : declaredFields) {
			System.out.println(declaredField);
		}
		//4. Field getDeclaredField(String name)
		Field d = personClass.getDeclaredField("age");
		//忽略访问权限修饰符的安全检查
		d.setAccessible(true);//暴力反射
		
		Object ageValue = d.get(p);
		System.out.println(ageValue);
	}
}
---------------------------------------------------------------------------

		
	2. 获取构造方法们
		Constructor<?>[] getConstructors()
		Constructor<T> getConstructor(类<?>... parameterTypes)
		
		Constructor<?>[] getDeclaredConstructors()
		Constructor<T> getDeclaredConstructor(类<?>... parameterTypes)
		
		构造方法: 创建对象 newInstance(Object... initargs)
		如果使用空参数构造方法创建对象,操作可以简化:Class对象的newinstance方法
		
		//获取构造方法
public class ReflectDemo03 {
	public static void main(String[] args) throws Exception {
		Class personClass = Person.class;
		//Constructor<T> getConstructor(类<?>... parameterTypes)
		Constructor constructor1 = personClass.getConstructor(String.class, int.class);
		System.out.println(constructor1);
		//创建对象
		Object person1 = constructor1.newInstance("张三",23);
		System.out.println(person1);

		System.out.println("-----------------------");
		//空参
		Constructor constructor2 = personClass.getConstructor();
		System.out.println(constructor2);
		Object person2 = constructor2.newInstance();
		System.out.println(person2);

		//空参简化
		Object o = personClass.newInstance();
		System.out.println(o);
	}
}
----------------------------------------------------------------
		
	3. 获取成员方法们
		Method[] getMethods()
		Method getMethod(String name, 类<?>... parameterTypes)
		
		Method[] getDeclaredMethods()
		Method getDeclaredMethod(String name, 类<?>... parameterTypes)
		
		Method:方法对象
		执行方法: Object invoke(Object obj,Object... args)
		获取方法名称: String getName 
		
		//获取成员方法
public class ReflectDemo04 {
	public static void main(String[] args) throws Exception {
		Class personClass = Person.class;
		//获取指定名称方法,无参
		Method eatMethod = personClass.getMethod("eat");
		Person p = new Person();
		//执行方法
		eatMethod.invoke(p);

		//获取指定名称方法,有参
		Method eatMethod2 = personClass.getMethod("eat",String.class);
		//执行方法
		eatMethod2.invoke(p,"饭");

		System.out.println("---------------------------");

		//获取所有public修饰的方法
		Method[] methods = personClass.getMethods();
		for (Method method : methods) {
			System.out.println(method);//还包括了Object里的方法
			String name = method.getName();
			System.out.println(name);//方法名
		}

		//获取类名
		String className = personClass.getName();
		System.out.println(className);//全类名 Note.Person
	}
}
}
------------------------------------------------------------------------------
		
	4. 获取类名
		String getName()
		
		
		
## 反射案例
写一个框架,可以帮助我们创建任意类的对象,并且执行其中任意方法
创建一个配置文件

className=Note.Person
methodName=eat

不改动任何代码的前提下,只要改动配置文件,达到目的
--------------------------------------------------
/*
	框架类
 */
public class ReflectTest {
	public static void main(String[] args) throws Exception {
		//可以使用任意类对象,任意方法
		/*
			前提: 不能改变该类的任何代码,传统方法没有意义
		 */
//		Person p = new Person();
//		p.eat();

		/*
			写一个框架,在不改变该类任何代码的前提下,帮助我们创建任意对象使用任意方法
			实现:
			1. 配置文件
			2. 反射
			步骤:
			1. 将需要创建的对象的全类名和需要执行的方法定义在配置文件中
			2. 在程序中加载读取配置文件
			3. 使用反射技术来加载类文件进内存
			4. 创建对象
			5. 执行方法
		 */

		//1. 加载配置文件
		//创建Properties对象
		Properties pro = new Properties();
		//加载配置文件,转换为一个集合
		ClassLoader classLoader = ReflectTest.class.getClassLoader();
		InputStream is = classLoader.getResourceAsStream("pro.properties");
		pro.load(is);

		//2. 获取配置文件中定义的数据
		String className = pro.getProperty("className");
		String methodName = pro.getProperty("methodName");

		//3. 加载该类进内存
		Class cls = Class.forName(className);
		//4. 创建对象
		Object obj = cls.newInstance();
		//5. 获取方法对象
		Method method = cls.getMethod(methodName);
		//执行方法
		method.invoke(obj);
	}
}
----------------------------------------------------------		
		
		

## 注解:
注释: 用文字描述程序,方便阅读.给程序员看的
注解: 也是用来说明程序,给计算机看的

作用分类:
编写文档: 通过代码里表示的注解生成文档(api里doc文档)
代码分析: 通过注解对代码进行分析
编译检查: 通过代码里表示的注解让编译器能够实现基本的编译检查

JDK中预定义的一些注解
@Override: 检测被该注解标注的方法是否是继承自父类
@Deprecated: 该注解标注的内容表示已过时,再调用此方法时会有横线划掉 
@SuppressWarnings: 压制警告,一般传递all SuppressWarnings("all")


## 自定义注解
格式:
	元注解
	public @interface 注解名称{
		属性列表;
	}

本质: 注解本质上就是一个接口,该接口默认继承Annotation接口
	public interface MyAnno extends java.lang.annotation.Annotation{}

属性: 接口中可以定义的成员方法
	要求:
		1. 属性的返回值类型
			基本数据类型
			String
			枚举 enum
			注解
			以上类型数组
		2. 定义了属性,在使用是需要给属性赋值
			定义属性时可以使用default关键字默认初始值
			如果只要一个属性需要赋值,且名称是value,则value可以省略
			数组赋值时,值使用{}包裹,如果数组只有一个值,则{}可以省略
		
			
public @interface MyAnno {
	int weight();
	String country() default "CN";
}

----------------------------------------

@MyAnno(weight = 120,country = "CN")
public class Person(){}
	
元注解: 用于描述注解的注解
	@Target: 描述注解能够作用的位置
		ElementType取值:
		TYPE: 可以作用在类上
		METHOD: 可以作用于方法上
		FIELD: 可以作用于成员变量上
		
	@Retention: 描述注解被保留的阶段
		@Retention(RetentionPolicy.RUNTIME): 作用阶段,一班自定义用RUNTIME
	@Documented: 描述注解是否被抽取到api文档中
	@Inherited: 描述注解是否被子类继承,比如Worker类家里MyAnno注解,如果MyAnno有@Inherited,Teacher继承了Worker后,自动有MyAnno注解
	

## 在程序中解析(使用)注解
	1. 获取注解定义位置的对象(Class,Method,Field)
	2. 获取指定的注解
		getAnnotation(class)
		
	3. 调用注解中的抽象方法获取配置的属性值
	
/**
 * 描述需要执行的类名,和方法名
 */
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
public @interface Pro {

	String className();
	String methodName();

}
----------------------------------------

@Pro(className = "Note.Person",methodName = "eat")
public class ReflectTest2 {
	public static void main(String[] args) throws Exception {
		/*
			前提: 不能改变该类的任何代码,传统方法没有意义
		 */

		//1.解析注解
		//1.1 获取该类的字节码文件对象
		Class<ReflectTest2> reflectTest2Class = ReflectTest2.class;
		//1.2 获取上面的注解
		//其实就是在内存中生成了一个该注解接口的子类实现对象
		/**
		 * 就相当于
		 * public class ProImpl implements Pro{
		 * 		public String className(){
		 * 			return "Note.Person"
		 * 		}
		 * 		public String methodName(){
		 * 			return "eat"
		 * 		}
		 * 	}
		 */
		Pro annotation = reflectTest2Class.getAnnotation(Pro.class);
		//1.3 调用注解对象中定义的抽象方法,获取返回值
		String className = annotation.className();
		String methodName = annotation.methodName();
//		System.out.println(className);//Note.Person
//		System.out.println(methodName);//eat

		//3. 加载该类进内存
		Class cls = Class.forName(className);
		//4. 创建对象
		Object obj = cls.newInstance();
		//5. 获取方法对象
		Method method = cls.getMethod(methodName);
		//执行方法
		method.invoke(obj);
	}
}

---------------------------------------------------

## 注解案例

/*
	小明定义的计算器类
 */
public class MingCalculator {

	//加法
	@Check
	public void add(){
		System.out.println("1 + 0 =" + (1 + 0));
	}

	//减法
	@Check
	public void sub(){
		System.out.println("1 - 0 =" + (1 - 0));
	}

	//乘法
	@Check
	public void mul(){
		System.out.println("1 * 0 =" + (1 * 0));
	}

	//除法
	@Check
	public void div(){
		System.out.println("1 / 0 =" + (1 / 0));
	}
	
	public void show(){
		System.out.println("永无bug");
	}
}
----------------------------------------------------

@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface Check {
}

---------------------------------------------------------
/*
	简单的测试框架
	当主方法执行后,会自动执行被检测的所有方法
 */
public class TestCheck {

	public static void main(String[] args) throws IOException {
		//1. 创建计算器对象
		MingCalculator c = new MingCalculator();
		//2. 获取字节码文件对象
		Class cls = c.getClass();
		//3. 获取所有方法
		Method[] methods = cls.getMethods();

		int number = 0;//出现异常的次数
		BufferedWriter bw = new BufferedWriter(new FileWriter("bug.txt"));
		for (Method method : methods) {
			//4. 判断方法上是否有Check注解
			if(method.isAnnotationPresent(Check.class)){
				//5. 有就执行
				try {
					method.invoke(c);
				} catch (Exception e) {
					//6. 捕获异常
					//记录到文件中
					number++;
					bw.write(method.getName()+"方法出异常了");
					bw.newLine();
					bw.write("异常的名称:"+e.getCause().getClass().getSimpleName());
					bw.newLine();
					bw.write("异常的原因:"+ e.getCause().getMessage());
					bw.newLine();
					bw.write("-------------------");
					bw.newLine();

				}
			}
		}

		bw.write("本次测试一共出现"+number+"次异常");

		bw.flush();
		bw.close();
	}
}

-------------------------------------------------------------------------------------
小结:
1. 以后大多数时候,我们会使用注解,而不是自定义注解
2. 注解给谁用? 
	1. 编译器
	2. 给解析程序用
3. 注解不是程序的一部分,可以理解为注解就是一个标签