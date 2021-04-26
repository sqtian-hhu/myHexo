---
title: JavaNote_Spring_Day03
date: 2020-06-29 09:57:03
tags: Java框架
categories: Java
---
## Day3
spring中的app和基于XML以及注解的AOP配置

1. 完善我们的account案例
2. 分析案例中的问题
3. 回顾之前讲过的一个技术: 动态代理
4. 动态代理另一种实现方式
5. 解决案例中的问题
6. AOP的概念
7. spring中的AOP相关术语
8. spring中基于XML和注解的AOP配置

		编码问题，改成utf8
        <property name="jdbcUrl" value="jdbc:mysql://localhost:3306/lovelycc?characterEncoding=utf8"></property>

<!--more-->

实现转账功能
1. 在持久层接口中添加根据名称找账户的方法
2. 在持久层实现类中实现方法
	public Account findAccountByName(String accountName) {
		try {
			List<Account> accounts = runner.query("select * from account where name = ?",new BeanListHandler<Account>(Account.class),accountName);
			if(accounts == null || accounts.size() == 0){
				return null;
			}else if(accounts.size() > 1){
				throw new RuntimeException("结果集不唯一,数据有问题");
			}else{
				return accounts.get(0);
			}

		} catch (Exception e) {
			throw new RuntimeException(e);
		}
	}
	
3. 在业务层接口中添加转账方法
4. 在业务层实现类中实现方法
	public void transfer(String sourceName, String targetName, Double money) {
		//1. 根据名称查询转出账户
		Account source = accountDao.findAccountByName(sourceName);
		//2. 根据名称查询转入账户
		Account target = accountDao.findAccountByName(targetName);
		//3. 转出账户减钱
		source.setBalance(source.getBalance()-money);
		//4. 转入账户加钱
		target.setBalance(target.getBalance()+money);
		//5. 更新转出账户
		accountDao.updateAccount(source);
		
		//int i=1/0
		
		//6. 更新转入账户
		accountDao.updateAccount(target);
	}


问题：方法中没调用一次accountDao，就获取了一个连接，不同连接有自己的事务。如果中间报错，转账不完整。
需要使用ThreadLocal对象把Connection和当前线程绑定，从而使一个线程中只有一个能控制事务的对象

事务控制应该都是在业务层

1. 添加连接池工具类
package com.sqtian.utils;

import javax.sql.DataSource;
import java.sql.Connection;

/**
 * 连接工具类,用于从数据源中获取一个连接,并且实现线程的绑定
 */
public class ConnectionUtils {
	private ThreadLocal<Connection> tl = new ThreadLocal<Connection>();
	private DataSource datasource;

	public void setDatasource(DataSource datasource) {
		this.datasource = datasource;
	}

	public Connection getThreadConnection(){
		//1. 先从ThreadLocal上获取
		Connection connection = tl.get();
		try {
			//2. 判断当前线程上是否有连接
			if(connection == null){
				//3. 从数据源中获取一个连接,并且存入ThreadLocal中
				connection = datasource.getConnection();
				tl.set(connection);
			}
			//4. 返回当前线程上的连接
			return connection;
		} catch (Exception e) {
			throw new RuntimeException(e);
		}
	}

	/**
	 * 把连接和线程解绑
	 */
	public void removeConnection(){
		tl.remove();
	}

}
--------------------------------------------------------------------------------

2. 添加事务管理工具类

package com.sqtian.utils;

import java.sql.SQLException;

/**
 * 和事务管理相关的工具类,它包含了,开启事务,提交事务,回滚事务和释放连接
 */
public class TransactionManager {

	private ConnectionUtils connectionUtils;

	public void setConnectionUtils(ConnectionUtils connectionUtils) {
		this.connectionUtils = connectionUtils;
	}

	/**
	 * 开启事务
	 */
	public void beginTransaction(){
		try {
			connectionUtils.getThreadConnection().setAutoCommit(false);
		} catch (SQLException e) {
			e.printStackTrace();
		}
	}

	/**
	 * 提交事务
	 */
	public void commit(){
		try {
			connectionUtils.getThreadConnection().commit();
		} catch (SQLException e) {
			e.printStackTrace();
		}
	}

	/**
	 * 回滚事务
	 */
	public void rollback(){
		try {
			connectionUtils.getThreadConnection().rollback();
		} catch (SQLException e) {
			e.printStackTrace();
		}
	}

	/**
	 * 释放连接
	 */
	public void release(){
		try {
			connectionUtils.getThreadConnection().close();//换回连接池中
			connectionUtils.removeConnection();
		} catch (SQLException e) {
			e.printStackTrace();
		}
	}
}

------------------------------------------------------------------------------------
3. 在业务层控制事务



package com.sqtian.service.impl;

import com.sqtian.dao.IAccountDao;
import com.sqtian.domain.Account;
import com.sqtian.service.IAccountService;
import com.sqtian.utils.TransactionManager;

import java.util.List;

/**
 * 账户的业务层实现类
 *
 * 事务控制应该都是在业务层
 */
public class AccountServiceImpl implements IAccountService {

	private IAccountDao accountDao;
	private TransactionManager txManager;
	
	//交给spring注入
	public void setTxManager(TransactionManager txManager) {
		this.txManager = txManager;
	}

	//交给spring注入
	public void setAccountDao(IAccountDao accountDao) {
		this.accountDao = accountDao;
	}


	public void transfer(String sourceName, String targetName, Double money) {
		try{
			//1. 开启事务
			txManager.beginTransaction();
			//2. 执行操作


			//2.1. 根据名称查询转出账户
			Account source = accountDao.findAccountByName(sourceName);
			//2.2. 根据名称查询转入账户
			Account target = accountDao.findAccountByName(targetName);
			//2.3. 转出账户减钱
			source.setBalance(source.getBalance()-money);
			//2.4. 转入账户加钱
			target.setBalance(target.getBalance()+money);
			//2.5. 更新转出账户
			accountDao.updateAccount(source);
			//2.6. 更新转入账户
			accountDao.updateAccount(target);


			//3. 提交事务
			txManager.commit();

		}catch (Exception e){
			//5. 回滚操作
			txManager.rollback();
			throw new RuntimeException(e);
		}finally {
			//6. 释放连接
			txManager.release();
		}
	}
}

-------------------------------------------------------------------

4. 持久层实现类改变连接方式
	//不再希望通过queryRunner里取连接,而是通过connectionUtils取
	//删去原来的注入<constructor-arg name="ds" ref="dataSource"></constructor-arg>

	private ConnectionUtils connectionUtils;

	public void setConnectionUtils(ConnectionUtils connectionUtils) {
		this.connectionUtils = connectionUtils;
	}

	//改成加入connectionUtils.getThreadConnection()参数的guery方法
	public Account findAccountByName(String accountName) {
		try {
			List<Account> accounts = runner.query(connectionUtils.getThreadConnection(),"select * from account where name = ?",new BeanListHandler<Account>(Account.class),accountName);
			if(accounts == null || accounts.size() == 0){
				return null;
			}else if(accounts.size() > 1){
				throw new RuntimeException("结果集不唯一,数据有问题");
			}else{
				return accounts.get(0);
			}

		} catch (Exception e) {
			throw new RuntimeException(e);
		}
	}

-----------------------------------------------------------------
5. 注入新的依赖
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="accountService" class="com.sqtian.service.impl.AccountServiceImpl">
        <!--注入dao对象-->
        <property name="accountDao" ref="accountDao"></property>
        <!--注入事务管理器-->
        <property name="txManager" ref="txManager"></property>
    </bean>

    <bean id="accountDao" class="com.sqtian.dao.impl.AccountDaoImpl">
        <property name="runner" ref="runner"></property>
        <property name="connectionUtils" ref="connectionUtils"></property>
    </bean>

    <!--配置QueryRunner,如果用单例时,一个要用,另一个可能还没用完,线程受到影响.因此要用多例-->
    <bean id="runner" class="org.apache.commons.dbutils.QueryRunner" scope="prototype">
        <!--注入数据源-->

    </bean>

    <!--配置数据源连接池-->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <!--配置连接数据库的必备信息-->
        <property name="driverClass" value="com.mysql.jdbc.Driver"></property>
        <property name="jdbcUrl" value="jdbc:mysql://localhost:3306/lovelycc?characterEncoding=utf8"></property>
        <property name="user" value="root"></property>
        <property name="password" value="root"></property>
    </bean>

    <!--配置Connection的工具类 ConnectionUtils-->
    <bean id="connectionUtils" class="com.sqtian.utils.ConnectionUtils">
        <!--注入数据源-->
        <property name="dataSource" ref="dataSource"></property>
    </bean>

    <!--配置事务管理器-->
    <bean id="txManager" class="com.sqtian.utils.TransactionManager">
        <property name="connectionUtils" ref="connectionUtils"></property>
    </bean>
</beans>

------------------------------------------------------------------------------------------------------------

## 动态代理

现在依赖太繁杂，方法之间也存在依赖。如果一个方法名改变了，则需要变动大量代码。
需要使用动态代理
类似于客户与生产厂家之间的代理商

基于接口的动态代理
1.
package com.sqtian.proxy;

/**
 * 对生产厂家要求的接口
 */
public interface IProducer {

	/**
	 * 销售
	 * @param money
	 */
	public void saleProduct(Double money);
	/**
	 * 售后
	 * @param money
	 */
	public void afterService(Double money);


}
--------------------------------------------------------
2. 
package com.sqtian.proxy;

/**
 * 一个生产者
 *
 */
public class Producer implements IProducer{

	/**
	 * 销售
	 * @param money
	 */
	public void saleProduct(Double money){
		System.out.println("销售产品,并拿到钱:"+money);
	}

	/**
	 * 售后
	 * @param money
	 */
	public void afterService(Double money){
		System.out.println("提供售后服务,并拿到钱:"+money);
	}
}
-------------------------------------------------------------------
package com.sqtian.proxy;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

/**
 * 模拟一个消费者
 */
public class Client {
	public static void main(String[] args) {
		//匿名内部类访问外部成员时,外部成员要求是最终的
		final Producer producer = new Producer();

		producer.saleProduct(10000d);

		/**
		 * 动态代理:
		 * 	特点: 字节码随用随创建,随用随加载
		 * 	作用: 不修改源码的基础上对方法加强
		 * 	分类:
		 * 		基于接口的动态代理
		 * 		基于子类的动态代理
		 * 	基于接口的动态代理:
		 * 		涉及的类: Proxy
		 * 		提供者: JDK官方
		 * 	如何创建代理对象:
		 * 		使用Proxy类中的newProxyInstance方法
		 * 	创建代理对象的要求:
		 * 		被代理类最少实现一个接口,如果没有则不能使用
		 * 	newProxyInstance方法的参数:
		 * 		ClassLoader: 类加载器
		 * 			它用于加载代理对象字节码的.和被代理对象使用相同的类加载器. 固定写法 producer.getClass().getClassLoader()
		 * 		Class[]
		 * 			它是用于让代理对象和被代理对象有相同方法. 固定写法 producer.getClass().getInterfaces()
		 * 		InvocationHandler: 用于提供增强的代码
		 * 			它是让我们写如何代理.我们一般是都是些一个该接口的实现类.通常情况下都是匿名内部类.
		 * 			此接口的实现类都是谁用谁写
		 *
		 *
		 */
		IProducer proxyProducer = (IProducer) Proxy.newProxyInstance(producer.getClass().getClassLoader(), producer.getClass().getInterfaces(),
			new InvocationHandler() {
				/**
				 * 作用: 执行被代理对象的任何接口方法都会经过该方法
				 * @param proxy   代理对象的引用
				 * @param method  当前执行的方法
				 * @param args    当前执行方法所需的参数
				 * @return        和被代理对象方法有相同的返回值
				 * @throws Throwable
				 */
			public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
				//提供增强的代码
				//比如经销商提取20%利润

				Object returnValue = null;
				//1. 获取方法执行的参数
				Double money = (Double) args[0];
				//2. 判断当前方法是不是销售
				if("saleProduct".equals(method.getName())){
					returnValue = method.invoke(producer,money*0.8d);
				}
				return returnValue;
			}
		});

		proxyProducer.saleProduct(10000d);
	}

}
-----------------------------------------------------------------------------------------------
如果没有实现接口，则会报错

引出第二种代理方式
基于子类的动态代理
Producer 不需要实现接口

1. 导入cglib的jar包
    <dependencies>
        <dependency>
            <groupId>cglib</groupId>
            <artifactId>cglib</artifactId>
            <version>2.1_3</version>
        </dependency>
    </dependencies>
	
2. 
/**
 * 模拟一个消费者
 */
public class Client {
	public static void main(String[] args) {
		//匿名内部类访问外部成员时,外部成员要求是最终的
		final Producer producer = new Producer();

		producer.saleProduct(10000d);

		/**
		 * 动态代理:
		 * 	特点: 字节码随用随创建,随用随加载
		 * 	作用: 不修改源码的基础上对方法加强
		 * 	分类:
		 * 		基于接口的动态代理
		 * 		基于子类的动态代理
		 * 	基于接口的动态代理:
		 * 		涉及的类: Enhancer
		 * 		提供者: 第三方cglib库
		 * 	如何创建代理对象:
		 * 		使用Enhancer类中的create方法
		 * 	创建代理对象的要求:
		 * 		被代理类不能是最终类
		 * 	create方法的参数:
		 * 		Class: 字节码
		 * 			它用于指定被代理对象的字节码
		 * 		Callback: 用于提供增强的代码
		 * 			它是让我们写如何代理.我们一般是都是些一个该接口的实现类.通常情况下都是匿名内部类.
		 * 			此接口的实现类都是谁用谁写
		 *			一般写的都是该接口的子接口实现类: MethodInterceptor
		 *
		 */
		Producer cglibProducer = (Producer) Enhancer.create(producer.getClass(),new MethodInterceptor(){
			/**
			 * 作用: 执行被代理对象的任何接口方法都会经过该方法
			 * @param proxy   代理对象的引用
			 * @param method  当前执行的方法
			 * @param args    当前执行方法所需的参数
			 * @param methodProxy 当前执行方法的代理对象
			 * @return        和被代理对象方法有相同的返回值
			 * @throws Throwable
			 */
			public Object intercept(Object proxy, Method method, Object[] args, MethodProxy methodProxy) throws Throwable {

					//提供增强的代码
					//比如经销商提取20%利润

					Object returnValue = null;
					//1. 获取方法执行的参数
					Double money = (Double) args[0];
					//2. 判断当前方法是不是销售
					if("saleProduct".equals(method.getName())){
						returnValue = method.invoke(producer,money*0.8d);
					}
					return returnValue;
			}
		});

		cglibProducer.saleProduct(10000d);
	}

}
------------------------------------------------------------------------------


回到转账案例
AccountServiceImpl中太多事务管理的冗余代码，通过代理对象统一管理
1. 把事务管理的代码交给BeanFactory
package com.sqtian.service.impl;

/**
 * 账户的业务层实现类
 *
 * 事务控制应该都是在业务层
 */
public class AccountServiceImpl implements IAccountService {

	private IAccountDao accountDao;


	//交给spring注入
	public void setAccountDao(IAccountDao accountDao) {
		this.accountDao = accountDao;
	}
	public void transfer(String sourceName, String targetName, Double money) {
		//2.1. 根据名称查询转出账户
		Account source = accountDao.findAccountByName(sourceName);
		//2.2. 根据名称查询转入账户
		Account target = accountDao.findAccountByName(targetName);
		//2.3. 转出账户减钱
		source.setBalance(source.getBalance()-money);
		//2.4. 转入账户加钱
		target.setBalance(target.getBalance()+money);
		//2.5. 更新转出账户
		accountDao.updateAccount(source);
		//2.6. 更新转入账户
		accountDao.updateAccount(target);

		System.out.println("转账成功");
	}
}

-----------------------------------------------------------------

package com.sqtian.factory;

import com.sqtian.service.IAccountService;
import com.sqtian.utils.TransactionManager;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

/**
 * 用于创建Service的代理对象的工厂
 *
 */
public class BeanFactory {
	private IAccountService accountService;
	private TransactionManager txManager;

	//交给spring注入
	public void setTxManager(TransactionManager txManager) {
		this.txManager = txManager;
	}

	public final void setAccountService(IAccountService accountService) {
		this.accountService = accountService;
	}

	/**
	 * 获取Service代理对象
	 */
	public IAccountService getAccountService() {
		return (IAccountService) Proxy.newProxyInstance(accountService.getClass().getClassLoader(),
			accountService.getClass().getInterfaces(),
			new InvocationHandler() {
				/**
				 * 添加事务支持
				 *
				 * @param proxy
				 * @param method
				 * @param args
				 * @return
				 * @throws Throwable
				 */
				public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
					Object returnValue = null;
					try {
						//1. 开启事务
						txManager.beginTransaction();
						//2. 执行操作
						returnValue = method.invoke(accountService, args);
						//3. 提交事务
						txManager.commit();
						//4. 返回结果
						return returnValue;
					} catch (Exception e) {
						//5. 回滚操作
						txManager.rollback();
						throw new RuntimeException(e);
					} finally {
						//6. 释放连接
						txManager.release();
					}
				}
			});
	}

}

-------------------------------------------------------------------------
2. 修改配置文件
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">


    <!--配置代理的service-->
    <bean id="proxyAccountService" factory-bean="beanFactory" factory-method="getAccountService"></bean>
    <!--配置beanFactory-->
    <bean id="beanFactory" class="com.sqtian.factory.BeanFactory">
        <property name="accountService" ref="accountService"></property>
        <property name="txManager" ref="txManager"></property>
    </bean>



    <bean id="accountService" class="com.sqtian.service.impl.AccountServiceImpl">
        <!--注入dao对象-->
        <property name="accountDao" ref="accountDao"></property>
<!--        &lt;!&ndash;注入事务管理器&ndash;&gt;-->
<!--        <property name="txManager" ref="txManager"></property>-->
    </bean>

    <bean id="accountDao" class="com.sqtian.dao.impl.AccountDaoImpl">
        <property name="runner" ref="runner"></property>
        <property name="connectionUtils" ref="connectionUtils"></property>
    </bean>

    <!--配置QueryRunner,如果用单例时,一个要用,另一个可能还没用完,线程受到影响.因此要用多例-->
    <bean id="runner" class="org.apache.commons.dbutils.QueryRunner" scope="prototype">
        <!--注入数据源-->

    </bean>

    <!--配置数据源连接池-->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <!--配置连接数据库的必备信息-->
        <property name="driverClass" value="com.mysql.jdbc.Driver"></property>
        <property name="jdbcUrl" value="jdbc:mysql://localhost:3306/lovelycc?characterEncoding=utf8"></property>
        <property name="user" value="root"></property>
        <property name="password" value="root"></property>
    </bean>

    <!--配置Connection的工具类 ConnectionUtils-->
    <bean id="connectionUtils" class="com.sqtian.utils.ConnectionUtils">
        <!--注入数据源-->
        <property name="dataSource" ref="dataSource"></property>
    </bean>

    <!--配置事务管理器-->
    <bean id="txManager" class="com.sqtian.utils.TransactionManager">
        <property name="connectionUtils" ref="connectionUtils"></property>
    </bean>
</beans>

------------------------------------------------------------------------------------------------


## AOP的概念
Aspect Oriented Programming 面向切面编程
通过预编译方式和运行期动态代理实现程序功能的统一维护的一种技术
利用AOP可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的耦合度降低，提高程序的可重用性
优势：
	减少重复代码
	提高开发效率
	维护方便
	
相关术语
Joinpoint(连接点):
	所谓连接点是指那些被拦截的点.在spring中,这些点指方法,因为spring只支持方法类型的连接点
Pointcut(切入点):
	指我们要对哪些Joinpoint进行拦截的定义 (被动态代理的方法)

Advice(通知/增强):
	拦截后要做的事情
	前置通知,后置通知,异常通知,最终通知,环绕通知
	
Target(目标对象):
	代理的目标对象
	
Weaving(织入):
	把增强应用到目标对象来创建新的代理对象的过程
	
Proxy(代理):
	被增强后产生的结果代理类

Aspect(切面):
	是切入点与通知的结合


打印日志的AOP测试

public class AccountServiceImpl implements IAccountService {
	public void saveAccount() {
		System.out.println("执行了保存");
	}

	public void updateAccount(int i) {
		System.out.println("执行了更新");
	}

	public int deleteAccount() {
		System.out.println("执行了删除");
		return 0;
	}
}
------------------------------------------
package com.sqtian.utils;

/**
 * 用于记录日志的工具类,它里面提供了公共的代码
 */

public class Logger {

	/**
	 * 用于打印日志,计划让其在切入点方法执行之前执行(切入点方法就是业务层方法)
	 *
	 */

	public void printLog(){
		System.out.println("Logger类中的printLog方法开始记录日志");
	}

}
---------------------------------------------------------
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        https://www.springframework.org/schema/aop/spring-aop.xsd">

    <!-- 配置spring的IOC, 把Service对象配置进来 -->
    <bean id="accountService" class="com.sqtian.service.impl.AccountServiceImpl">
    </bean>

    <!-- spring中基于XML的AOP配置步骤
        1. 把通知的Bean也交给spring来管理
        2. 使用aop:config标签表明开始AOP的配置
        3. 使用aop:aspect标签表面配置切面
                id属性: 是给切面提供一个唯一标识
                ref属性: 是指定通知类的bean的ID
        4. 在aop:aspect标签的内部使用对应标签来配置通知的类型
                我们现在的示例是让printLog方法在切入点方法执行之前执行,所以是前置通知
                aop:before 表示配置前置通知
                    method属性:用于指定Logger类中哪个方法是前置通知
                    pointcut属性:用于指定切入点表达式,该表达式的含义指的是对业务层中哪些方法增强

            切入点表达式的写法:
                关键字: execution(表达式)
                表达式:
                    访问修饰符  返回值  包名.类名.方法名(参数列表)
                标准写法:
                    public void com.sqtian.service.impl.AccountServiceImpl.saveAccount()
                访问修饰符可以省略
                    void com.sqtian.service.impl.AccountServiceImpl.saveAccount()
                返回值可以使用通配符表示任意返回值
                    * com.sqtian.service.impl.AccountServiceImpl.saveAccount()    
                包名可以使用通配符表示任意包，但是有几级包就业写几个*
                    * *.*.*.*.AccountServiceImpl.saveAccount()   
                包名可以使用..表示当前包及其子包
                    * *..AccountServiceImpl.saveAccount()
                类名和方法名都可以使用*表示通配
                    * *..*.*()
                参数列表:
                    可以直接写数据类型:
                        基本类型直接写名称         int
                        引用类型写包名.类名的方式   java.lang.String
                        可以使用通配符表示任意类型,但是必须有参数 * *..*.*(*)
                        可以使用..表示有无参数均可              * *..*.*(..)
                全通配写法:
                    * *..*.*(..)
                    
                实际开发中,切入点表达式的通常写法:
                    切到业务层实现类下的所有方法
                    * com.sqtian.service.impl.*.*(..)

    -->

    <!--配置Logger类-->
    <bean id="logger" class="com.sqtian.utils.Logger"></bean>

    <!--配置AOP-->
    <aop:config>
        <!--配置切面-->
        <aop:aspect id="logAdvice" ref="logger">
            <!--配置通知的类型,并且建立通知方法和切入点方法的关联-->
            <aop:before method="printLog" pointcut="execution(* com.sqtian.service.impl.*.*(..))"></aop:before>
        </aop:aspect>
    </aop:config>


</beans>

-------------------------------------------------------------------------------------
/**
 * 测试AOP的配置
 */
public class AOPTest {
	public static void main(String[] args) {
		//1. 获取容器
		ApplicationContext ac = new ClassPathXmlApplicationContext("bean.xml");
		//2. 获取对象
		IAccountService as = (IAccountService) ac.getBean("accountService");
		//3. 执行方法
		as.saveAccount();
	}
}

------------------------------------------------------------------------------------------------------



通知的类型
package com.sqtian.utils;

import org.springframework.aop.aspectj.MethodInvocationProceedingJoinPoint;

/**
 * 用于记录日志的工具类,它里面提供了公共的代码
 */

public class Logger {

	/**
	 * 前置通知
	 *
	 */

	public void beforePrintLog(){
		System.out.println("Logger类中的前置通知方法开始记录日志");
	}

	/**
	 * 后置通知
	 *
	 */

	public void afterReturningPrintLog(){
		System.out.println("Logger类中的后置通知方法开始记录日志");
	}

	/**
	 * 异常通知
	 *
	 */

	public void afterThrowingPrintLog(){
		System.out.println("Logger类中的异常通知方法开始记录日志");
	}

	/**
	 * 最终通知
	 *
	 */

	public void afterPrintLog(){
		System.out.println("Logger类中的最终通知方法开始记录日志");
	}

	/**
	 * 环绕通知
	 * 问题:
	 * 		当我们配置类环绕通知之后,切入点方法没有执行,而通知方法执行了
	 * 分析:
	 * 		通过对比动态代理中的环绕通知代码,发现动态代理的环绕通知有明确的切入点方法调用,
	 * 解决:
	 * 	spring框架为我们提供了一个接口: ProceedingJoinPoint. 该接口有一个方法proceed(). 此方法就相当于明确调用切入点方法
	 * 	该接口可以作为环绕通知的方法参数,在程序执行时,spring框架会提供实现类给我们使用
	 *
	 * 	spring中的环绕通知:
	 * 		它是spring框架为我们提供的一种可以在代码中手动控制增强方法何时执行的方式. 写在哪就在什么位置执行
	 */
	public Object aroundPrintLog(MethodInvocationProceedingJoinPoint pjp){
		Object rtValue = null;

		try {
			Object[] args = pjp.getArgs(); //得到方法执行所需的参数

			System.out.println("Logger类中的环绕通知方法开始记录日志");
			rtValue = pjp.proceed(); //明确调用业务层方法(切入点方法)

			System.out.println("Logger类中的环绕通知方法开始记录日志");

			return rtValue;
		}catch (Throwable t){
			System.out.println("Logger类中的环绕通知方法开始记录日志");
			throw new RuntimeException(t);
		}finally {
			System.out.println("Logger类中的环绕通知方法开始记录日志");
		}
	}
}

--------------------------------------------------------------------------------------
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        https://www.springframework.org/schema/aop/spring-aop.xsd">

    <!-- 配置spring的IOC, 把Service对象配置进来 -->
    <bean id="accountService" class="com.sqtian.service.impl.AccountServiceImpl">
    </bean>

    <!--配置Logger类-->
    <bean id="logger" class="com.sqtian.utils.Logger"></bean>

    <!--配置AOP-->
    <aop:config>


        <!--配置切入点表达式 id属性用于指定表达式的唯一标识. expression属性用于指定表达式内容
            此标签写在aop:aspect标签内部只能当前切面使用.
            它还可以写在aop:aspect的外卖,此时就变成了所有切面可用,但是按约束要求要放在切面配置之前
        -->
        <aop:pointcut id="pt1" expression="execution(* com.sqtian.service.impl.*.*(..))"/>


        <!--配置切面-->
        <aop:aspect id="logAdvice" ref="logger">
<!--            &lt;!&ndash;配置前置通知的类型,在切入点方法执行之前执行&ndash;&gt;-->
<!--            <aop:before method="beforePrintLog" pointcut-ref="pt1"></aop:before>-->

<!--            &lt;!&ndash;配置后置通知的类型,在切入点方法正常执行之后执行,与异常通知只有一个执行&ndash;&gt;-->
<!--            <aop:after-returning method="afterReturningPrintLog" pointcut-ref="pt1"></aop:after-returning>-->

<!--            &lt;!&ndash;配置异常通知的类型,在切入点方法执行产生异常之后执行,与后置通知只有一个执行&ndash;&gt;-->
<!--            <aop:after-throwing method="afterThrowingPrintLog" pointcut-ref="pt1"></aop:after-throwing>-->

<!--            &lt;!&ndash;配置最终通知的类型,无论切入点方法是否正常执行都会在其后面执行&ndash;&gt;-->
<!--            <aop:after method="afterPrintLog" pointcut-ref="pt1"></aop:after>-->


            <!--配置环绕通知-->
            <aop:around method="aroundPrintLog" pointcut-ref="pt1"></aop:around>

        </aop:aspect>
    </aop:config>


</beans>

-------------------------------------------------------------------------------------------------------


## 注解AOP


bean.xml


<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:context="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        https://www.springframework.org/schema/aop/spring-aop.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">


    <!--配置要扫描的包-->
    <context:component-scan base-package="com.sqtian"></context:component-scan>

    <!--配置spring开启注解AOP的支持-->
    <aop:aspectj-autoproxy></aop:aspectj-autoproxy>


</beans>

----------------------------------------------------------------------------------------


@Service("accountService")
public class AccountServiceImpl implements IAccountService {
...
}
----------------------------------------------------------------------------


package com.sqtian.utils;

import org.springframework.aop.aspectj.MethodInvocationProceedingJoinPoint;

/**
 * 用于记录日志的工具类,它里面提供了公共的代码
 */


@Component("logger")
@Aspect //表示当前类是一个切面类
public class Logger {

	/**
	 * 注解配置切入点表达式
	 */
	@Pointcut("execution(* com.sqtian.service.impl.*.*(..))")
	private void pt1(){}

	/**
	 * 前置通知
	 *
	 */
	@Before("pt1()")
	public void beforePrintLog(){
		System.out.println("Logger类中的前置通知方法开始记录日志");
	}

	/**
	 * 后置通知
	 *
	 */
	@AfterReturning("pt1()")
	public void afterReturningPrintLog(){
		System.out.println("Logger类中的后置通知方法开始记录日志");
	}

	/**
	 * 异常通知
	 *
	 */
	@AfterThrowing("pt1()")
	public void afterThrowingPrintLog(){
		System.out.println("Logger类中的异常通知方法开始记录日志");
	}

	/**
	 * 最终通知
	 *
	 */
	@After("pt1()")
	public void afterPrintLog(){
		System.out.println("Logger类中的最终通知方法开始记录日志");
	}

	/**
	 * 环绕通知
	 */

	@Arround("pt1()")
	public Object aroundPrintLog(MethodInvocationProceedingJoinPoint pjp){
		Object rtValue = null;

		try {
			Object[] args = pjp.getArgs(); //得到方法执行所需的参数

			System.out.println("Logger类中的环绕通知方法开始记录日志");
			rtValue = pjp.proceed(); //明确调用业务层方法(切入点方法)

			System.out.println("Logger类中的环绕通知方法开始记录日志");

			return rtValue;
		}catch (Throwable t){
			System.out.println("Logger类中的环绕通知方法开始记录日志");
			throw new RuntimeException(t);
		}finally {
			System.out.println("Logger类中的环绕通知方法开始记录日志");
		}
	}
}

------------------------------------------------------------------------------------------------------






//不用xml的方式,在配置类加上注解@EnableAspectJAutoProxy

package config;

import org.springframework.context.annotation.*;

@Configuration
@ComponentScan("com.sqtian")
@EnableAspectJAutoProxy
@Import(JdbcConfig.class)
@PropertySource("classpath:JdbcConfig.properties")
public class SpringConfiguration {

	//用于写公共配置

}
