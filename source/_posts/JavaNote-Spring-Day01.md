---
title: JavaNote_Spring_Day01
date: 2020-06-29 09:54:45
tags: Java框架
categories: Java
---
## Day1
Spring框架的概述以及spring中基于XML的IOC配置

程序的耦合
	耦合：程序的依赖关系
		包括：
			类之间的依赖
			方法间的依赖
	解耦：
		降低程序间的依赖关系
	实际开发中：
		应该做到，编译期不依赖，运行时才依赖
	解耦的思路：
		第一步：使用反射来创建对象，而避免使用new关键字
		第二步：通过读取配置文件来获取要创建的对象全限定类名
		
<!--more-->
		
	创建Bean对象的工厂
	Bean：在计算机英语中，有可重用组件的含义
	JavaBean：用java编写的可重用组件
		javabean > 实体类
		它就是创建我们的service和dao对象的
		第一个: 需要一个配置文件来配置我们的service和dao
				配置的内容: 唯一标识=全限定类名(key=value)
		第二个: 通过读取配置文件中配置的内容, 反射创建对象
		
		我们的配置文件可以是xml也可以是properties



------------------------------------------------
用工厂模式创建对象
/**
 * 创建Bean对象的工厂
 */
public class BeanFactory {

	//定义一个Properties对象
	private static Properties properties;

	//定义一个Map,用于存放我们要创建的对象,我们称之为容器
	private static Map<String,Object> beans;

	//使用静态代码块为Properties对象赋值
	static{
		try {
			//实例化对象
			properties = new Properties();
			//获取properties文件的流对象
			InputStream in = BeanFactory.class.getClassLoader().getResourceAsStream("bean.properties");
			properties.load(in);

			//实例化容器
			beans = new HashMap<String, Object>();

			//取出配置文件中所有的Key
			Enumeration keys = properties.keys();
			//遍历枚举
			while(keys.hasMoreElements()){
				//取出每个key
				String key = keys.nextElement().toString();
				//根据key获取value
				String beanPath = properties.getProperty(key);
				//反射创建对象
				Object value = Class.forName(beanPath).newInstance();
				//把key和value存入容器中
				beans.put(key,value);
			}
		} catch (Exception e) {
			throw new ExceptionInInitializerError("初始化properties失败!");
		}

	}

	/**
	 * 根据Bean名称获取Bean对象
	 * @param beanName
	 * @return
	 */


//	public static Object getBean(String beanName){
//		Object bean = null;
//
//		try {
//			String beanPath = properties.getProperty(beanName);
//			bean = Class.forName(beanPath).newInstance();
////每次都会调用默认构造函数创建对象
//		} catch (Exception e) {
//			e.printStackTrace();
//		}
//		return bean;
//	}

	/**
	 * 静态代码块准备好beans后,每次getBean就简单了
	 * 现在是单例对象了
	 */

	public static Object getBean(String beanName){
		return beans.get(beanName);
	}
}

------------------------------------------------------------------------
public class AccountDaoImpl implements IAccountDao{
	/**
	 * 账户的持久层实现类
	 */

	public void saveAccount() {
		System.out.println("保存了账户");
	}


}
-------------------------------------------------------------
/**
 * 账户的业务层实现类
 * 业务层调用持久层
 */
public class AccountServiceImpl implements IAccountService {


//	private IAccountDao accountDao = new AccountDaoImpl();
	private IAccountDao accountDao = (IAccountDao) BeanFactory.getBean("accountDao");

//	private int i = 1;//变量若定义在方法外面,单例对象不会重置变量,想要i不变,则要定义到方法里

	public void saveAccount() {
		int i = 1;//一般应用层和持久层中不需要额外定义的变量
		accountDao.saveAccount();
		System.out.println(i);
		i++;

	}
}
----------------------------------------------------------------------------------------------		
/**
 * 模拟一个表现层,用于调用业务层
 * 实际开发中是一个servlet
 */
public class Client {

	public static void main(String[] args) {
//		IAccountService as = new AccountServiceImpl();

		IAccountService as = (IAccountService) BeanFactory.getBean("accountService");
		as.saveAccount();
	}
}


--------------------------------------------------------------------------------------
accountService = com.sqtian.service.impl.AccountServiceImpl
accountDao = com.sqtian.dao.impl.AccountDaoImpl




## IOC

IOC(Inversion of Control): 控制反转, 把创建对象的权力交给框架,是框架的重要特征
包括依赖注入和依赖查找
明确ioc的作用: 削减计算机程序的耦合(解除我们代码中的依赖关系)

Spring中的IOC

/**
 * 模拟一个表现层,用于调用业务层
 * 实际开发中是一个servlet
 * 获取spring的IOC核心容器，并根据id获取对象
 *
 * ApplicationContext的三个常用实现类
 * 	ClassPathXmlApplicationContext: 它可以加载类路径下的配置文件,要求配置文件必须在类路径下.不在的话加载不了
 * 	FileSystemXmlApplicationContext: 它可以加载磁盘任意路径下的配置文件(必须有访问权限)
 * 	AnnotationConfigApplicationContext: 它是用于读取注解创建容器的
 *
 * 	核心容器的两个接口引发出的问题
 * 	ApplicationContext:   单例对象适用    实际更多采用此接口
 * 		它在构建核心容器时,创建对象采取的策略是采用立即加载的方式. 只要一读取完配置文件马上就创建配置文件中的配置的对象
 * 	BeanFactory: 	      多例对象适用
 * 		它在构建核心容器时,创建对象采取的策略是采用延迟加载的方式. 什么时候根据id获取对象了, 什么时候才真正创建对象
 *
 */
public class Client {

	public static void main(String[] args) {
		//1. 获取核心容器对象
//		ApplicationContext ac = new ClassPathXmlApplicationContext("bean.xml"); //更常用
//		ApplicationContext ac = new FileSystemXmlApplicationContext("D:\\IntelliJ IDEACommunity Edition 2019.3.1\\com.sqtian.SpringIOC\\src\\main\\resources\\bean.xml");

		//-----------BeanFactory------------
		Resource resource = new ClassPathResource("bean.xml");
		BeanFactory factory = new XmlBeanFactory(resource);
		IAccountService as = (IAccountService) factory.getBean("accountService");
		IAccountService adao = (IAccountService) factory.getBean("accountDao");

		//2. 根据id获取Bean对象
//		IAccountService as = (IAccountService) ac.getBean("accountService");
//		IAccountDao adao = ac.getBean("accountDao",IAccountDao.class);

//		as.saveAccount();
	}
}



bean对象的细节

<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

<!--    把对象的创建交给spring来管理-->

    <bean id="accountService" class="com.sqtian.service.impl.AccountServiceImpl">
        <!-- collaborators and configuration for this bean go here -->
    </bean>


<!--    spring对bean的管理细节
    1. 创建bean的三种方式
    2. bean对象的作用范围
    3. bean对象的生命周期

    -->

    <!--创建bean的三种方式-->

    <!--第一种方式：使用默认构造函数创建
        在spring的配置文件中使用bean标签，配以id和class属性后，且没有其他标签时使用第一种。
        若类中没有默认构造函数则对象无法创建-->
    <bean id="accountDao" class="com.sqtian.dao.impl.AccountDaoImpl">
        <!-- collaborators and configuration for this bean go here -->
    </bean>

    <!--第二种方式： 使用普遍工厂中的方法创建对象（使用某个类中的方法创建对象，并存入spring容器-->
<!--    <bean id="instanceFactory" class="com.sqtian.factory.instanceFactory"></bean>-->
<!--    <bean id="accountService" factory-bean="instanceFactory" factory-method="getAccountService"></bean>-->


    <!--第三种方式：使用工厂中的静态方法创建对象（使用某个类中的静态方法创建对象，并存入spring容器）-->
<!--    <bean id="accountService" class="com.sqtian.factory.StaticFactory" factory-method="getAccountService"></bean>-->
    <!-- more bean definitions go here -->

    <!--bean的作用范围调整
        bean标签的scope属性：
            作用：用于指定bean的作用范围
            取值：
                singleton：单例的（默认值）
                prototype：多例的
                request：作用于web应用的请求范围
                session：作用于web应用的会话范围
                global-session：作用于集群环境的会话范围（全局会话范围），当不是集群环境时，它就是session
                
            -->

    <!--bean对象的生命周期
        单例对象
            出生：当容器创建时对象出生
            活着：只要容器还在，对象一直活着
            死亡：容器销毁，对象消亡
            总结：单例对象的生命周期和容器相同
        多例对象
            出生：当我们使用对象时spring框架为我们创建
            活着：对象只要是在使用过程中就一直活着
            死亡：当对象长时间不用且没有别的对象引用时，由java的垃圾回收器回收
            
            -->
</beans>


-------------------------------------------------------------------------------------------------

spring中的依赖注入

    <!--spring中的依赖注入
        依赖注入：
            Dependency Injection
        IOC的作用：
            降低程序的耦合（依赖关系）
        依赖关系的管理：
            以后都交给spring来维护
        在当前类需要用到其他类的对象，由spring为我们提供，我们只需要在配置文件中说明
        依赖关系的维护：
            就称之为依赖注入
        依赖注入：
            能注入的数据有三类：
                基本类型和String
                其他bean类型（在配置文件中或者注解配置过的bean）
                复杂类型/集合类型
            注入的方式有三种：
                第一种：使用构造函数提供
                第二种：使用set方法提供
                第三种：使用注解提供
    -->

    <!--构造函数的注入:
            使用标签: constructor-arg
            标签出现的位置: bean标签的内部
            标签中的属性
                type:用于指定要注入的数据的数据类型, 该数据类型也是构造函数中某个或某些参数的类型
                index:用于指定要注入的数据给构造函数中指定索引位置的参数赋值.索引从0开始
                name:用于指定给构造函数中指定名称的参数赋值                 常用的
                ===================以上三个用于指定给构造函数中哪个参数赋值====================================
                value:用于提供基本类型和String类型的数据
                ref:用于指定其他的bean类型数据. 他指的就是在spring的IOC核心容器中出现过的bean对象

            优势:
                在获取bean对象时,注入数据是必须操作,否则对象无法创建成功
            弊端:
                改变了bean对象的实例化方式,使我们在创建对象时,如果用不到这些数据,也必须提供

            -->
    <bean id="accountService" class="com.sqtian.service.impl.AccountServiceImpl">
<!--        <constructor-arg type="java.lang.String" value="test"></constructor-arg>-->
        <constructor-arg name="name" value="test"></constructor-arg>
        <constructor-arg name="age" value="18"></constructor-arg>
        <constructor-arg name="birthday" ref="now"></constructor-arg>

    </bean>

    <!--配置一个日期对象-->
    <bean id="now" class="java.util.Date"></bean>


    <!--set方法注入                 更常用
        涉及的标签: property
        出现位置: bean标签内部
        标签属性:
            name:用于指定注入时所调用的set方法名称                 常用的
            value:用于提供基本类型和String类型的数据
            ref:用于指定其他的bean类型数据. 他指的就是在spring的IOC核心容器中出现过的bean对象

        优势:
            创建对象时没有明确的限制,可以直接使用默认构造函数
        弊端:
            如果有某个成员必须有值,则获取对象时有可能set方法没有执行

    -->
    <bean id="accountService2" class="com.sqtian.service.impl.AccountServiceImpl2">
        <property name="name" value="TEST"></property>
        <property name="age" value="21"></property>
        <property name="birthday" ref="now"></property>

    </bean>


    <!--复杂类型/集合类型的注入
        用于给List结构集合注入的标签:
            list array set
        用于给Map结构集合注入的标签:
            map props
        结构相同,标签可以互换

    -->
    <bean id="accountService3" class="com.sqtian.service.impl.AccountServiceImpl3">
        <property name="myStrs">
            <array>
                <value>AAA</value>
                <value>BBB</value>
                <value>CCC</value>
            </array>
        </property>

        <property name="myList">
            <list>
                <value>AAA</value>
                <value>BBB</value>
                <value>CCC</value>
            </list>
        </property>

        <property name="mySet">
            <set>
                <value>AAA</value>
                <value>BBB</value>
                <value>CCC</value>
            </set>
        </property>

        <property name="myMap">
            <map>
                <entry key="testA" value="aaa"></entry>
                <entry key="testB">
                    <value>BBB</value>
                </entry>
            </map>
        </property>

        <property name="myProps">
            <props>
                <prop key="testC">ccc</prop>
                <prop key="testD">ddd</prop>
            </props>
        </property>

    </bean>

------------------------------------------------------------------------------------------------------
	
public class AccountServiceImpl2 implements IAccountService {

	//如果是经常变化的数据，并不适用于注入的方式

	private String name;
	private Integer age;
	private Date birthday;

	public void setName(String name) {
		this.name = name;
	}

	public void setAge(Integer age) {
		this.age = age;
	}

	public void setBirthday(Date birthday) {
		this.birthday = birthday;
	}
	//	private IAccountDao accountDao = new AccountDaoImpl();

	public void saveAccount() {
//		accountDao.saveAccount();
		System.out.println("service中的saveAccount方法执行了。。。"+name+","+age+","+birthday);

	}
}
-----------------------------------------------------------------------------------	
public class AccountServiceImpl3 implements IAccountService {

	//如果是经常变化的数据，并不适用于注入的方式

	private String[] myStrs;
	private List<String> myList;
	private Set<String> mySet;
	private Map<String,String> myMap;
	private Properties myProps;

	public void setMyStrs(String[] myStrs) {
		this.myStrs = myStrs;
	}

	public void setMyList(List<String> myList) {
		this.myList = myList;
	}

	public void setMySet(Set<String> mySet) {
		this.mySet = mySet;
	}

	public void setMyMap(Map<String, String> myMap) {
		this.myMap = myMap;
	}

	public void setMyProps(Properties myProps) {
		this.myProps = myProps;
	}

	public void saveAccount() {

		System.out.println(Arrays.toString(myStrs));
		System.out.println(myList);
		System.out.println(mySet);
		System.out.println(myMap);
		System.out.println(myProps);
	}
}

-----------------------------------------------------------------------------------------------------	
	