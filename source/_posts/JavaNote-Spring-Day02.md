---
title: JavaNote_Spring_Day02
date: 2020-06-29 09:54:51
tags: Java框架
categories: Java
---
## day2
spring中基于注解的IOC和ioc的案例

1. spring中ioc的常用注解
2. 案例使用xml方式和注解方式实现单表的CRUD操作
	持久层技术选择：dbutils
	
3. 改造基于注解的ioc案例，使用纯注解的方式实现
	spring的一些新注解使用

4. spring和Junit整合


<!--more-->

Account案例

1. 导入依赖
pom.xml

<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.example</groupId>
    <artifactId>com.sqtian.springExample</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>jar</packaging>

    <dependencies>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>5.0.2.RELEASE</version>
        </dependency>

        <dependency>
            <groupId>commons-dbutils</groupId>
            <artifactId>commons-dbutils</artifactId>
            <version>1.4</version>
        </dependency>

        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.6</version>
        </dependency>
        <dependency>
            <groupId>c3p0</groupId>
            <artifactId>c3p0</artifactId>
            <version>0.9.1.2</version>
        </dependency>

        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.13</version>
        </dependency>
    </dependencies>
</project>

--------------------------------------------------------------------------------------------------------

2. 创建业务层接口与实现类

package com.sqtian.service;

import com.sqtian.domain.Account;

import java.util.List;

/**
 * 账户的业务层接口
 *
 */
public interface IAccountService {

	/**
	 * 查询所有
	 * @return
	 */
	List<Account> findAllAccount();

	/**
	 * 找一个
	 * @return
	 */
	Account findAccountById(Integer accountId);

	/**
	 * 保存
	 * @param account
	 */
	void saveAccount(Account account);

	/**
	 * 改
	 * @param account
	 */
	void updateAccount(Account account);

	/**
	 * 删
	 * @param accountId
	 */
	void deleteAccount(Integer accountId);
}

---------------------------------------------------------------------
package com.sqtian.service.impl;

import com.sqtian.dao.IAccountDao;
import com.sqtian.domain.Account;
import com.sqtian.service.IAccountService;

import java.util.List;

/**
 * 账户的业务层实现类
 */
public class AccountServiceImpl implements IAccountService {

	private IAccountDao accountDao;

	public void setAccountDao(IAccountDao accountDao) {
		this.accountDao = accountDao;
	}

	public List<Account> findAllAccount() {
		return accountDao.findAllAccount();
	}

	public Account findAccountById(Integer accountId) {
		return accountDao.findAccountById(accountId);
	}

	public void saveAccount(Account account) {
		accountDao.saveAccount(account);
	}

	public void updateAccount(Account account) {
		accountDao.updateAccount(account);
	}

	public void deleteAccount(Integer accountId) {
		accountDao.deleteAccount(accountId);
	}
}
-------------------------------------------------------------------

3. 创建Account实体类
package com.sqtian.domain;

import java.io.Serializable;

/**
 * 账户的实体类
 *
 */

public class Account implements Serializable {
	private Integer id;
	private String name;
	private double balance;

	public Integer getId() {
		return id;
	}

	public void setId(Integer id) {
		this.id = id;
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public double getBalance() {
		return balance;
	}

	public void setBalance(double balance) {
		this.balance = balance;
	}

	@Override
	public String toString() {
		return "Account{" +
			"id=" + id +
			", name='" + name + '\'' +
			", balance=" + balance +
			'}';
	}
}
------------------------------------------------------------------
4. 创建持久层接口和实现类
package com.sqtian.dao;

import com.sqtian.domain.Account;

import java.util.List;

/**
 * 持久层接口
 */

public interface IAccountDao {

	/**
	 * 查询所有
	 * @return
	 */
	List<Account> findAllAccount();

	/**
	 * 找一个
	 * @return
	 */
	Account findAccountById(Integer accountId);

	/**
	 * 保存
	 * @param account
	 */
	void saveAccount(Account account);

	/**
	 * 改
	 * @param account
	 */
	void updateAccount(Account account);

	/**
	 * 删
	 * @param accountId
	 */
	void deleteAccount(Integer accountId);

}
----------------------------------------------------------------------

package com.sqtian.dao.impl;

import com.sqtian.dao.IAccountDao;
import com.sqtian.domain.Account;
import org.apache.commons.dbutils.QueryRunner;
import org.apache.commons.dbutils.handlers.BeanHandler;
import org.apache.commons.dbutils.handlers.BeanListHandler;

import java.sql.SQLException;
import java.util.List;

/**
 * 持久层实现类
 *
 */


public class AccountDaoImpl implements IAccountDao {

	private QueryRunner runner;

	//交给spring注入
	public void setRunner(QueryRunner runner) {
		this.runner = runner;
	}



	public List<Account> findAllAccount() {
		try {
			return runner.query("select * from account",new BeanListHandler<Account>(Account.class));
		} catch (Exception e) {
			throw new RuntimeException(e);
		}

	}


	public Account findAccountById(Integer accountId) {
		try {
			return runner.query("select * from account where id = ?",new BeanHandler<Account>(Account.class),accountId);
		} catch (Exception e) {
			throw new RuntimeException(e);
		}

	}

	public void saveAccount(Account account) {
		try {
			runner.update("insert into account(name,balance)values(?,?)",account.getName(),account.getBalance());
		} catch (Exception e) {
			throw new RuntimeException(e);
		}
	}

	public void updateAccount(Account account) {
		try {
			runner.update("update account set name=?,balance=? where id = ?",account.getName(),account.getBalance(),account.getId());
		} catch (Exception e) {
			throw new RuntimeException(e);
		}
	}

	public void deleteAccount(Integer accountId) {
		try {
			runner.update("delete from account where id = ?",accountId);
		} catch (Exception e) {
			throw new RuntimeException(e);
		}
	}
}

-------------------------------------------------------------------------------------------------
5. 配置spring
bean.xml

<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="accountService" class="com.sqtian.service.impl.AccountServiceImpl">
        <!--注入dao对象-->
        <property name="accountDao" ref="accountDao"></property>
    </bean>

    <bean id="accountDao" class="com.sqtian.dao.impl.AccountDaoImpl">
        <property name="runner" ref="runner"></property>
    </bean>

    <!--配置QueryRunner,如果用单例时,一个要用,另一个可能还没用完,线程受到影响.因此要用多例-->
    <bean id="runner" class="org.apache.commons.dbutils.QueryRunner" scope="prototype">
        <!--注入数据源-->
        <constructor-arg name="ds" ref="dataSource"></constructor-arg>
    </bean>

    <!--配置数据源连接池-->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <!--配置连接数据库的必备信息-->
        <property name="driverClass" value="com.mysql.jdbc.Driver"></property>
        <property name="jdbcUrl" value="jdbc:mysql://localhost:3306/lovelycc?serverTimezone=UTC"></property>
        <property name="user" value="root"></property>
        <property name="password" value="root"></property>
    </bean>
</beans>

------------------------------------------------------------------------------------------------------

6. 创建测试类
package com.sqtian.test;

import com.sqtian.domain.Account;
import com.sqtian.service.IAccountService;
import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

import java.util.List;

/**
 * 使用junit单元测试
 *
 */

public class AccountServiceTest {

	@Test
	public void testFindAll() {
		//1. 获取容器
		ApplicationContext ac = new ClassPathXmlApplicationContext("bean.xml");
		//2. 调用业务层对象
		IAccountService as = ac.getBean("accountService",IAccountService.class);
		//3. 执行方法
		List<Account> accounts = as.findAllAccount();
		for (Account account : accounts) {
			System.out.println(account);
		}
	}

	@Test
	public void testFindAllOne() {
		ApplicationContext ac = new ClassPathXmlApplicationContext("bean.xml");
		IAccountService as = ac.getBean("accountService", IAccountService.class);

		Account accountById = as.findAccountById(1);
		System.out.println(accountById);
	}

	@Test
	public void testSave() {
		ApplicationContext ac = new ClassPathXmlApplicationContext("bean.xml");
		IAccountService as = ac.getBean("accountService", IAccountService.class);
		Account account = new Account();
		account.setName("CC");
		account.setBalance(7000);
		account.setUid(25);
		as.saveAccount(account);

	}

	@Test
	public void testUpdate() {
		Account account = new Account();
		account.setName("CCTT");
		account.setBalance(7000);
		account.setUid(20);
		account.setId(5);
		ApplicationContext ac = new ClassPathXmlApplicationContext("bean.xml");
		IAccountService as = ac.getBean("accountService", IAccountService.class);
		as.updateAccount(account);
	}

	@Test
	public void testDelete() {

		ApplicationContext ac = new ClassPathXmlApplicationContext("bean.xml");
		IAccountService as = ac.getBean("accountService", IAccountService.class);
		as.deleteAccount(5);
	}
}
---------------------------------------------------------------------------------------------



/**
 * 账户的业务层实现类
 * 业务层调用持久层
 *
 * 注解实现IOC
 * 用于创建对象的
 * 	他们的作用就和在xml配置文件中编写一个<bean></bean>标签实现的功能是一样的
 * 		Component
 * 			作用: 用于把当前类的对象存入spring容器中
 * 			属性:
 * 				value, 用于指定bean的id,不写时默认是当前类名,且首字母小写
 *
 * 		Controller
 * 			一般用在表现层
 * 		Service
 * 			一般用在业务层
 * 		Repository
 * 			一般用在持久层
 * 		以上三个注解的作用和属性与Component是一模一样的
 * 		他们三个是spring框架为我们提供的三层使用的注解，使我们的三层对象更清晰
 * 		
 *
 * 用于注入数据的
 * 	和在xml配置文件中的bean标签写一个<property></property>标签的作用是一样的
 * 		Autowired
 * 			作用：自动按照类型注入，只要容器中有唯一一个bean对象类型和要注入的变量类型匹配，就可以注入成功，没有则报错
 * 			出现位置可以是变量上，也可以是方法上
 *		Qualifier
 *			作用：在按照类型注入的基础上再按照名称注入。给类成员注入时不能单独使用。但是在给方法参数注入时可以。
 *		Resource
 *			作用：直接按照bean的id注入，可以独立使用
 *			属性：name，用于指定id
 *		以上三个注解都只能注入其他bean类型的数据，而基本类型和String类型无法使用上述注解实现
 *		另外，集合类型的注入只能通过xml来实现
 *
 * 用于改变作用范围
 * 	和在bean标签中使用scope属性实现的功能一样
 * 		Scope
 * 			用于指定bean的作用范围
 * 			属性： value指定范围的取值 常用取值：singleton、prototype
 *
 * 和生命周期相关 （了解）
 * 	和在bean标签中使用init-method和destroy-method作用一样
 *		PreDestroy
 *			用于指定销毁方法
 *		PostConstruct
 *			用于指定初始化方法
 */
 
 
## 通过注解的方式优化账户案例

1. 修改配置文件约束

bean.xml

<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

    <context:annotation-config/>

    <!--告知spring在创建容器时要扫描的包-->
    <context:component-scan base-package="com.sqtian"></context:component-scan>
    <!--配置QueryRunner,如果用单例时,一个要用,另一个可能还没用完,线程受到影响.因此要用多例-->
    <bean id="runner" class="org.apache.commons.dbutils.QueryRunner" scope="prototype">
        <!--注入数据源-->
        <constructor-arg name="ds" ref="dataSource"></constructor-arg>
    </bean>

    <!--配置数据源连接池-->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <!--配置连接数据库的必备信息-->
        <property name="driverClass" value="com.mysql.jdbc.Driver"></property>
        <property name="jdbcUrl" value="jdbc:mysql://localhost:3306/lovelycc?serverTimezone=UTC"></property>
        <property name="user" value="root"></property>
        <property name="password" value="root"></property>
    </bean>
</beans>

-------------------------------------------------------------------------------------------------------------------

2. 给实现类添加注解

/**
 * 账户的业务层实现类
 */

@Service("accountService")
public class AccountServiceImpl implements IAccountService {

	//只有一个需要注入，可以用autowired注解，通过注解的方式注入可以不需要set方法
	@Autowired
	private IAccountDao accountDao;

	public List<Account> findAllAccount() {
		return accountDao.findAllAccount();
	}

	public Account findAccountById(Integer accountId) {
		return accountDao.findAccountById(accountId);
	}

	public void saveAccount(Account account) {
		accountDao.saveAccount(account);
	}

	public void updateAccount(Account account) {
		accountDao.updateAccount(account);
	}

	public void deleteAccount(Integer accountId) {
		accountDao.deleteAccount(accountId);
	}
}
-----------------------------------------------------------------

/**
 * 持久层实现类
 *
 */

@Repository("accountDao")
public class AccountDaoImpl implements IAccountDao {

	@Autowired
	private QueryRunner runner;




	public List<Account> findAllAccount() {
		try {
			return runner.query("select * from account",new BeanListHandler<Account>(Account.class));
		} catch (Exception e) {
			throw new RuntimeException(e);
		}

	}


	public Account findAccountById(Integer accountId) {
		try {
			return runner.query("select * from account where id = ?",new BeanHandler<Account>(Account.class),accountId);
		} catch (Exception e) {
			throw new RuntimeException(e);
		}

	}

	public void saveAccount(Account account) {
		try {
			runner.update("insert into account(name,balance,uid)values(?,?,?)",account.getName(),account.getBalance(),account.getUid());
		} catch (Exception e) {
			throw new RuntimeException(e);
		}
	}

	public void updateAccount(Account account) {
		try {
			runner.update("update account set name=?,balance=?,uid = ? where id = ?",account.getName(),account.getBalance(),account.getUid(),account.getId());
		} catch (Exception e) {
			throw new RuntimeException(e);
		}
	}

	public void deleteAccount(Integer accountId) {
		try {
			runner.update("delete from account where id = ?",accountId);
		} catch (Exception e) {
			throw new RuntimeException(e);
		}
	}
}
----------------------------------------------------------------------------



## 全注解的方式
不需要bean.xml了
新建配置类


package config;

import org.springframework.context.annotation.*;



/**
 * 该类是一个配置类，其作用和bean.xml是一样的
 * spring中的新注解
 * Configuration
 * 	作用: 指定当前类是一个配置类
 * 	细节: 当配置类作为AnnotationConfigApplicationContext对象创建的参数时,该注解可以不写
 * 			ApplicationContext ac = new AnnotationConfigApplicationContext(SpringConfiguration.class);
 *
 * ComponentScan
 * 	作用:用于通过注解指定spring在创建容器时要扫描的包
 * 	属性:
 * 		value: 它和basePackages的作用是一样的,都是用于指定创建容器时要扫描的包
 * 			   我们使用此注解就等同于在xml中配置了     <context:component-scan base-package="com.sqtian"></context:component-scan>
 *
 * Bean
 * 	作用: 用于把当前方法的返回值作为bean对象存入spring的ioc容器中
 * 	属性:
 * 		name: 用于指定bean的id,当不写时,
 * 	细节:
 * 		当我们使用注解配置方法时,如果方法有参数,spring框架会去容器中查找有没有可用的bean对象.
 * 		查找方式和和Autowired注解的作用一样.
 *
 * 	Import
 * 		作用: 用于导入其他的配置类
 * 		属性:
 * 			value: 用于指定其他配置类的字节码
 * 					当我们使用Import的注解之后,有Import注解的类叫父配置类,而导入的都是子配置类
 *
 * PropertySource
 * 		作用: 用于指定properties文件的位置
 * 		属性:
 * 			value: 指定文件的名称和路径
 * 					关键字: classpath. 表示到类路径下
 */

@Configuration
@ComponentScan("com.sqtian")
@Import(JdbcConfig.class)
@PropertySource("classpath:JdbcConfig.properties")
public class SpringConfiguration {

	//用于写公共配置

}

-----------------------------------------------------------------------------------------------------------------------------------------------------

package config;

import com.mchange.v2.c3p0.ComboPooledDataSource;
import org.apache.commons.dbutils.QueryRunner;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Scope;

import javax.sql.DataSource;

public class JdbcConfig {

	//注上配置文件里的key
	@Value("${jdbc.driver}")
	private String driver;
	@Value("${jdbc.url}")
	private String url;
	@Value("${jdbc.username}")
	private String username;
	@Value("${jdbc.password}")
	private String password;



	/**
	 * 用于创建一个QueryRunner对象
	 * 需要实现多例
	 * @param dataSource
	 * @return
	 */
	@Bean(name = "runner")
	@Scope("prototype")
	public QueryRunner createQueryRunner(@Qualifier("dataSource") DataSource dataSource){  //当有多个数据源时,可以用Qualifier指定id
		return new QueryRunner(dataSource);
	}

	/**
	 * 创建数据源对象
	 * @return
	 */
	@Bean("dataSource")
	public DataSource creatDatasource(){
		try {
			ComboPooledDataSource ds = new ComboPooledDataSource();
			ds.setDriverClass(driver);
			ds.setJdbcUrl(url);
			ds.setUser(username);
			ds.setPassword(password);

			return ds;
		} catch (Exception e) {
			throw new RuntimeException(e);
		}

	}
}
------------------------------------------------------------------------------------------------------------------------------------------
JdbcConfig.properties


jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/lovelycc?serverTimezone=UTC
jdbc.username=root
jdbc.password=root

------------------------------------------------------------------------------------------

	@Test
	public void testFindAll() {
		//1. 获取容器
//		ApplicationContext ac = new ClassPathXmlApplicationContext("bean.xml");

		ApplicationContext ac = new AnnotationConfigApplicationContext(SpringConfiguration.class);
		//2. 调用业务层对象
		IAccountService as = ac.getBean("accountService",IAccountService.class);
		//3. 执行方法
		List<Account> accounts = as.findAllAccount();
		for (Account account : accounts) {
			System.out.println(account);
		}
	}




## 结合junit
 
/**
 * 使用junit单元测试
 *	Spring整合junit的配置
 *		1. 导入spring整合junit的jar
 *		2. 使用junit提供的一个注解把原有的main方法替换了,替换成spring提供的@Runwith
 *		3. 告知spring的运行器,spring和ioc创建是基于xml还是注解的,并且说明位置
 * 			@ContextConfiguration
 * 				location: 指定xml文件的位置,加上classpath关键字,表示在类路径下
 * 				classes: 指定注解类所在位置
 *		当我们使用spring 5.x版本时, junit必须对应4.12及以上
 *		
 */

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes = SpringConfiguration.class)

//如果用bean.xml配置
//@ContextConfiguration(location= "classpath:bean.xml")
public class AccountServiceTest {

	@Autowired
	private IAccountService as;

	//创建容器的代码交给spring实现,直接用as
	@Test
	public void testFindAll() {
		//3. 执行方法
		List<Account> accounts = as.findAllAccount();
		for (Account account : accounts) {
			System.out.println(account);
		}
	}
}
--------------------------------------------------------------------------
         <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>5.0.2.RELEASE</version>
        </dependency>
 
## 开发人员
public class AccountServiceTest {

	@Autowired
	private ApplicationContext ac;
	private IAccountService as;

	@Before
	public void init(){
		//1. 获取容器
		ac = new AnnotationConfigApplicationContext(SpringConfiguration.class);
		//2. 调用业务层对象
		as = ac.getBean("accountService",IAccountService.class);
	}

	@Test
	public void testFindAll() {
		//3. 执行方法
		List<Account> accounts = as.findAllAccount();
		for (Account account : accounts) {
			System.out.println(account);
		}
	}
	
}
------------------------------------------------------------------
 