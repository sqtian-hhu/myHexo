---
title: JavaNote_Spring_Day04
date: 2020-06-29 09:57:06
tags: Java框架
categories: Java
---
## day4
spring中的JDBCTemlate以及Spring事务控制

1. spring中的JdbcTemplate
		JdbcTemplate的作用：
			他就是用于和数据库交互的，实现对表的CRUD操作
		如何创建该对象：
		对象中的常用方法
		
	
<!--more-->	
	
导入依赖坐标
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.example</groupId>
    <artifactId>com.sqtian.springDay04_JdbcTemplate</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>jar</packaging>


    <dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>5.0.2.RELEASE</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>5.0.2.RELEASE</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-tx</artifactId>
            <version>5.0.2.RELEASE</version>
        </dependency>

        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.6</version>
        </dependency>

    </dependencies>
</project>
--------------------------------------------------------------------------------

package com.sqtian.jdbctemplate;

import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.jdbc.datasource.DriverManagerDataSource;


/**
 * JdbcTemplate的最基本用法
 */
public class JdbcTemplateDemo1 {
	public static void main(String[] args) {
		//准备数据源,spring的内置数据源
		DriverManagerDataSource ds = new DriverManagerDataSource();
		ds.setDriverClassName("com.mysql.jdbc.Driver");
		ds.setUrl("jdbc:mysql://localhost:3306/lovelycc?characterEncoding=utf8");
		ds.setUsername("root");
		ds.setPassword("root");

		//1. 创建JdbcTemplate对象
		JdbcTemplate jdbcTemplate = new JdbcTemplate();
		//给jt设置数据源
		jdbcTemplate.setDataSource(ds);
		//2. 执行操作
		jdbcTemplate.execute("insert into account(name,balance,uid)values('陆游',1000,30)");


	}

}
---------------------------------------------------------------------------------------------------------

其中含有new和大量set方法
可以通过spring的ioc进行配置

	配置bean.xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--配置jdbcTemplate-->
    <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
        <!-- collaborators and configuration for this bean go here -->
        <property name="dataSource" ref="dataSource"></property>

    </bean>


    <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <!-- collaborators and configuration for this bean go here -->
        <property name="driverClassName" value="com.mysql.jdbc.Driver"></property>
        <property name="url" value="jdbc:mysql://localhost:3306/lovelycc?characterEncoding=utf8"></property>
        <property name="username" value="root"></property>
        <property name="password" value="root"></property>

    </bean>

    <!-- more bean definitions go here -->

</beans>

---------------------------------------------------------------------------------------------------------------------

package com.sqtian.jdbctemplate;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import org.springframework.jdbc.core.JdbcTemplate;

/**
 * JdbcTemplate的最基本用法
 */
public class JdbcTemplateDemo2 {
	public static void main(String[] args) {
		//1. 获取容器
		ApplicationContext ac = new ClassPathXmlApplicationContext("bean.xml");
		//2. 获取对象
		JdbcTemplate jt = ac.getBean("jdbcTemplate",JdbcTemplate.class);
		//3. 执行操作
		jt.execute("insert into account(name,balance,uid)values('凌雪',2000,31)");

	}

}
---------------------------------------------------------------------------------------------------		
		
		
增删改查
		
package com.sqtian.jdbctemplate;

import com.sqtian.domain.Account;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import org.springframework.jdbc.core.BeanPropertyRowMapper;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.jdbc.core.RowMapper;

import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.List;

/**
 * JdbcTemplate的CRUD操作
 */
public class JdbcTemplateDemo3 {
	public static void main(String[] args) {
		//1. 获取容器
		ApplicationContext ac = new ClassPathXmlApplicationContext("bean.xml");
		//2. 获取对象
		JdbcTemplate jt = ac.getBean("jdbcTemplate",JdbcTemplate.class);
		//3. 执行操作
//		//保存
//		jt.update("insert into account(name,balance,uid)values(?,?,?)","屈心",10000,32);
//		//更新
//		jt.update("update account set name = ?,balance = ?, uid = ? where id = ?","楚留香",100000,5,4);
//		//删除
//		jt.update("delete from account where id = ?",5);


		//查询所有
		//需要一个参数RowMapper(),定义封装策略


//		List<Account> accounts=jt.query("select * from account where balance > ?",new AccountRowMapper(),5000);
		//使用spring中提供的实现类
		List<Account> accounts=jt.query("select * from account where balance > ?",new BeanPropertyRowMapper<Account>(Account.class),5000);

		for (Account account : accounts) {
			System.out.println(account);
		}
		//查询一个
		List<Account> acc=jt.query("select * from account where id = ?",new BeanPropertyRowMapper<Account>(Account.class),2);
		System.out.println(acc.isEmpty()?"没有内容":acc.get(0));

		//查询返回一行一列(使用聚合函数,但不增加group by 子句)
		Long count = jt.queryForObject("select count(*) from account where balance > ? ",Long.class,5000);
		System.out.println(count);
	}

}

/**
 *
 * 定义Account的封装策略
 *
 */
class AccountRowMapper implements RowMapper<Account>{
	/**
	 * 把结果集中的数据封装到Account中,然后由spring把每个Account加到集合中
	 * @param resultSet
	 * @param i
	 * @return
	 * @throws SQLException
	 */

	@Override
	public Account mapRow(ResultSet resultSet, int i) throws SQLException {
		Account account = new Account();
		account.setId(resultSet.getInt("id"));
		account.setName(resultSet.getString("name"));
		account.setBalance(resultSet.getDouble("balance"));
		account.setUid(resultSet.getInt("uid"));
		return account;
	}
}
-------------------------------------------------------------------------


DAO实现


		
package com.sqtian.dao.impl;

import com.sqtian.dao.IAccountDao;
import com.sqtian.domain.Account;
import org.springframework.jdbc.core.BeanPropertyRowMapper;
import org.springframework.jdbc.core.JdbcTemplate;

import java.util.List;

public class AccountDaoImpl implements IAccountDao {
	private JdbcTemplate jdbcTemplate;

	public void setJdbcTemplate(JdbcTemplate jdbcTemplate) {
		this.jdbcTemplate = jdbcTemplate;
	}



	@Override
	public Account findAccountById(Integer accountId) {
		List<Account> accounts = jdbcTemplate.query("select * from Account where id = ?", new BeanPropertyRowMapper<Account>(Account.class), accountId);

		return accounts.isEmpty() ? null:accounts.get(0);
	}

	@Override
	public Account findAccountByName(String accountName) {
		//写法不同,因为名字可能相同
		List<Account> accounts = jdbcTemplate.query("select * from Account where name = ?", new BeanPropertyRowMapper<Account>(Account.class), accountName);
		if (accounts.isEmpty()){
			return null;
		}else if(accounts.size()>1){
			throw new RuntimeException("结果集不唯一");
		}else {
			return accounts.get(0);
		}

	}

	@Override
	public void updateAccount(Account account) {
		jdbcTemplate.update("update account set name=?,balance=?,uid=? where id=?",account.getName(),account.getBalance(),account.getUid(),account.getId());
	}
}
--------------------------------------------------------------------------------------------------------------------------------

package com.sqtian.jdbctemplate;

import com.sqtian.dao.IAccountDao;
import com.sqtian.domain.Account;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

/**
 * JdbcTemplate的最基本用法
 */
public class JdbcTemplateDemo4 {
	public static void main(String[] args) {
		//1. 获取容器
		ApplicationContext ac = new ClassPathXmlApplicationContext("bean.xml");
		//2. 获取对象
		IAccountDao accountDao = ac.getBean("accountDao", IAccountDao.class);
		//3. 执行操作
		System.out.println(accountDao.findAccountById(7));
		System.out.println(accountDao.findAccountByName("西门吹雪"));

		Account account = new Account();
		account.setUid(33);
		account.setName("南宫问天");
		account.setBalance(50000);
		account.setId(8);
		accountDao.updateAccount(account);

	}

}

---------------------------------------------------------------------------------------------------------------
    <!--配置账户的持久层-->
    <bean id="accountDao" class="com.sqtian.dao.impl.AccountDaoImpl">
        <property name="jdbcTemplate" ref="jdbcTemplate"></property>
    </bean>


--------------------------------------------------------------------------------------

如果有多个DAO实现类,则存在许多重复代码
比如:
	private JdbcTemplate jdbcTemplate;

	public void setJdbcTemplate(JdbcTemplate jdbcTemplate) {
		this.jdbcTemplate = jdbcTemplate;
	}



需要抽取重复代码

public class JdbcDaoSupport {
	private JdbcTemplate jdbcTemplate;

	public void setJdbcTemplate(JdbcTemplate jdbcTemplate) {
		this.jdbcTemplate = jdbcTemplate;
	}

	public JdbcTemplate getJdbcTemplate() {
		return jdbcTemplate;
	}

	public void setDataSource(DataSource dataSource){
		if(jdbcTemplate == null){
			jdbcTemplate = createJdbcTemplate(dataSource); //可以不用注入template了直接注入datasource
		}
	}

	private JdbcTemplate createJdbcTemplate(DataSource dataSource) {
		return new JdbcTemplate(dataSource);
	}
}

--------------------------------------------------------------------
实际上,spring中提供了JdbcDaosupport类

区别,用自己的类可以加注解自动注入, 用提供的类因为是只读的,不能加注解了



		
		
		
2. 作业：
		利用spring基于AOP的事务控制对account案例改进
		
	1. 基于xnl的方法
	
	在bean.xml中添加AOP配置
	
	<!--配置AOP-->
    <aop:config>
        <!--配置通用的切入点表达式-->
        <aop:pointcut id="pt1" expression="excution(* com.sqtian.service.impl.*.*(..))"></aop:pointcut>
        <aop:aspect id="txAdvice" ref="txManager">
            <!--配置前置通知 开启事务-->
            <aop:before method="beginTransaction" pointcut-ref="pt1"></aop:before>
            <!--配置后置通知 提交事务-->
            <aop:after-returning method="afterReturningTransaction" pointcut-ref="pt1"></aop:after-returning>

            <!--配置异常通知 回滚事务-->
            <aop:after-throwing method="afterThrowingTransaction" pointcut-ref="pt1"></aop:after-throwing>

            <!--配置最终通知 释放连接-->
            <aop:after method="release" pointcut-ref="pt1"></aop:after>
        </aop:aspect>
    </aop:config>


	2. 基于注解的方法
		1. 给Service加上注解,删除不必要的set方法,删除bean.xml中对应配置
@Service("accountService")
public class AccountServiceImpl implements IAccountService {

	//自动注入就不需要set方法了
	@Autowired
	private IAccountDao accountDao;

......
		
-------------------------------------------------------

		2. 给Dao加上注解,并删除多余配置

@Repository("accountDao")
public class AccountDaoImpl implements IAccountDao {

	@Autowired
	private QueryRunner runner;

	//不再希望通过queryRunner里取连接,而是通过connectionUtils取
	//删去原来的注入<constructor-arg name="ds" ref="dataSource"></constructor-arg>
	//自己写的类加上注解后可以在bean中删掉配置
	@Autowired
	private ConnectionUtils connectionUtils;
	
--------------------------------------------------------------------

		3. 连接工具类
@Component("connectionUtils")
public class ConnectionUtils {
	private ThreadLocal<Connection> tl = new ThreadLocal<Connection>();

	@Autowired
	private DataSource dataSource;
....
-------------------------------------------------

		4. 事务管理器
**
 * 和事务管理相关的工具类,它包含了,开启事务,提交事务,回滚事务和释放连接
 */
@Component("txManager")
//切面注解
@Aspect
public class TransactionManager {

	@Autowired
	private ConnectionUtils connectionUtils;

	//配置切入点
	@Pointcut("excution(* com.sqtian.service.impl.*.*(..))")
	private void pt1(){}


	/**
	 * 开启事务
	 */
	@Before("pt1()")
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
	@AfterReturning("pt1()")
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
	@AfterThrowing("pt1()")
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
	@After("pt1()")
	public void release(){
		try {
			connectionUtils.getThreadConnection().close();//换回连接池中
			connectionUtils.removeConnection();
		} catch (SQLException e) {
			e.printStackTrace();
		}
	}
}

---------------------------------------------------------------------------------


然而,aop注解的四种通知运行顺序是存在问题的,以上代码并不能满足需求
需要通过环绕通知来实现

	/**
	 * 通过环绕通知注解控制事务
	 * @param pjp
	 * @return
	 */
	@Around("pt1")
	public Object aroundAdvice(ProceedingJoinPoint pjp){
		Object retuenValue = null;

		try{
			//1. 获取参数
			Object[] args = pjp.getArgs();
			//2. 开启事务
			this.beginTransaction();
			//3. 执行方法
			retuenValue = pjp.proceed();
			//4. 提交事务
			this.commit();

			//返回结果
			return retuenValue;
		}catch (Throwable t){
			//5. 回滚事务
			this.rollback();
			throw new RuntimeException(t);
		}finally {
			//6. 释放资源
			this.release();
		}
	}	


3. spring中的事务控制
		基于xml
		基于注解
		







































---------------------------------------------------------------------------------------
进入 mysql -u -p;

show databases;  

show tables; 显示所有表

use 库名； 操作数据库

打开服务 cmd services.msc

事件查看器 eventvwr

创建库 create database mydb;

导入数据 use mydb;

         source d:/wc.sql;

修改数据库root密码

mysql -u root

　　mysql> use mysql;

　　mysql> UPDATE user SET Password = PASSWORD('newpass') WHERE user = 'root';

　　mysql> FLUSH PRIVILEGES;

在丢失root密码的时候，可以这样

　　mysqld_safe --skip-grant-tables&

　　mysql -u root mysql

　　mysql> UPDATE user SET password=PASSWORD("new password") WHERE user='root';

　　mysql> FLUSH PRIVILEGES;

---------------------------------------------------------------------------------------------------------



## spring基于xml的声明式事务控制

1. 导入依赖坐标
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.example</groupId>
    <artifactId>comsqtian.springDay04.tx</artifactId>
    <version>1.0-SNAPSHOT</version>
    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>6</source>
                    <target>6</target>
                </configuration>
            </plugin>
        </plugins>
    </build>
    <packaging>jar</packaging>

    <dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>5.0.2.RELEASE</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>5.0.2.RELEASE</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-tx</artifactId>
            <version>5.0.2.RELEASE</version>
        </dependency>

        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.6</version>
        </dependency>

        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
            <version>1.8.7</version>
        </dependency>

        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.13</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>5.0.2.RELEASE</version>
        </dependency>
    </dependencies>
</project>

---------------------------------------------------------------------------
2. 配置bean.xml

<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/tx
        http://www.springframework.org/schema/tx/spring-tx.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd">




    <!--配置账户的持久层-->
    <bean id="accountDao" class="com.sqtian.dao.impl.AccountDaoImpl">
        <property name="dataSource" ref="dataSource"></property>
    </bean>

    <bean id="accountService" class="com.sqtian.service.impl.AccountServiceImpl">
        <property name="accountDao" ref="accountDao"></property>
    </bean>

    

    <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <!-- collaborators and configuration for this bean go here -->
        <property name="driverClassName" value="com.mysql.jdbc.Driver"></property>
        <property name="url" value="jdbc:mysql://localhost:3306/lovelycc?characterEncoding=utf8"></property>
        <property name="username" value="root"></property>
        <property name="password" value="root"></property>

    </bean>

    <!-- spring基于xml的声明式事务控制
        1. 配置事务管理器
        2. 配置事务的通知
            导入约束 tx的名称空间和约束,同时也需要aop的约束
            使用tx:advice标签配置事务通知
                属性:
                    id: 给事务通知起一个唯一标识
                    transaction-manager: 给事务通知提供一个事务管理器引用
        3. 配置AOP中的通用切入点表达式
        4. 建立事务通知的切入点表达式的对应关系
        5. 配置事务的属性
                是在事务的通知tx:advice标签的内部
    -->

    <!--配置事务管理器-->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"></property>
    </bean>

    <!--配置事务的通知-->
    <tx:advice id="txAdvice" transaction-manager="transactionManager">
        <!--配置事务的属性
            isolation: 用于指定事务的隔离级别.DEFAULT使用数据库默认级别
            propagation: 用于指定事务的传播行为,默认是REQUIRED,表示一定会有事务,增删改的选择.查询可以选择SUPPORTS
            read-only: 用于指定事务是否只读.只有查询方法才能设置为true.
            timeout: 用于指定事务的超时时间,默认值-1,表示永不超时
            rollback-for: 用于指定一个异常,当产生该异常时事务回滚.产生其他异常时事务不回滚.没有值表示任何异常都回滚
            no-rollback-for: 用于指定一个异常,当产生该异常时事务不回滚.产生其他异常时事务回滚.没有值表示任何异常都不回滚

        -->
        <tx:attributes>
            <tx:method name="*" propagation="REQUIRED" read-only="false"/>
            <tx:method name="find*" propagation="SUPPORTS" read-only="true"/>
        </tx:attributes>

    </tx:advice>

    <!--配置AOP-->
    <aop:config>
        <!--配置切入点表达式-->
        <aop:pointcut id="pt1" expression="execution(* com.sqtian.service.impl.*.*(..))"/>

        <aop:advisor pointcut-ref="pt1" advice-ref="txAdvice"/>

    </aop:config>
</beans>

-------------------------------------------------------------------------------------------------------------------







## 基于注解的声明式事务控制

	1. 配置事务管理器
	2. 开启spring对注解事务的支持
		<tx:annotation-driven transaction-manager="transactionManager"/>
	3. 在需要事务支持的地方使用@Transactional注解
		@service("accountService")
		@Transactional


## 基于纯注解的声明式事务控制

1. ServiceImpl

/**
 * 账户的业务层实现类
 *
 * 事务控制应该都是在业务层
 */
@Service("accountService")
//支持事务的注解
@Transactional(propagation = Propagation.SUPPORTS,readOnly = true)
public class AccountServiceImpl implements IAccountService {

	@Autowired
	private IAccountDao accountDao;



	public Account findAccountById(Integer accountId) {
		return accountDao.findAccountById(accountId);
	}

	@Transactional(propagation = Propagation.REQUIRED,readOnly = false)
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
---------------------------------------------------------

2. DAOIImpl
@Repository("accountDao")
public class AccountDaoImpl implements IAccountDao {

	@Autowired
	private JdbcTemplate jdbcTemplate;


	@Override
	public Account findAccountById(Integer accountId) {
		List<Account> accounts = jdbcTemplate.query("select * from Account where id = ?", new BeanPropertyRowMapper<Account>(Account.class), accountId);

		return accounts.isEmpty() ? null:accounts.get(0);
	}

	@Override
	public Account findAccountByName(String accountName) {
		//写法不同,因为名字可能相同
		List<Account> accounts = jdbcTemplate.query("select * from Account where name = ?", new BeanPropertyRowMapper<Account>(Account.class), accountName);
		if (accounts.isEmpty()){
			return null;
		}else if(accounts.size()>1){
			throw new RuntimeException("结果集不唯一");
		}else {
			return accounts.get(0);
		}

	}

	@Override
	public void updateAccount(Account account) {
		jdbcTemplate.update("update account set name=?,balance=?,uid=? where id=?",account.getName(),account.getBalance(),account.getUid(),account.getId());
	}
}
------------------------------------------------------------------------------------

3. 配置类

/**
 * Spring的配置类,相当于bean.xml
 */
@Configuration
@ComponentScan("com.sqtian")
@Import({JdbcConfig.class,TransactionConfig.class})
@PropertySource("jdbcConfig.properties")
//开启事务注解的支持
@EnableTransactionManagement
public class SpringConfiguration {
}
-----------------------------------------------------
/**
 * 和连接数据库相关的配置类
 */


public class JdbcConfig {

	@Value("${jdbc.driver}")
	private String driver;

	@Value("${jdbc.url}")
	private String url;

	@Value("${jdbc.username}")
	private String username;

	@Value("${jdbc.password}")
	private String password;



	/**
	 * 创建jdbcTemplate
	 * @param dataSource
	 * @return
	 */
	@Bean(name="jdbcTemplate")
	public JdbcTemplate createJdbcTemplate(DataSource dataSource){
		return new JdbcTemplate(dataSource);
	}

	@Bean("dataSource")
	public DataSource createDataSource(){
		DriverManagerDataSource ds = new DriverManagerDataSource();

		ds.setDriverClassName(driver);
		ds.setUrl(url);
		ds.setUsername(username);
		ds.setPassword(password);
		return ds;
	}

}

--------------------------------------------------------------------------
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/lovelycc?characterEncoding=utf8
jdbc.username=root
jdbc.password=root

--------------------------------------------------------------------------------

/**
 * 和事务相关的配置类
 */

public class TransactionConfig {

	/**
	 * 用于创建事务管理器对象
	 * @param dataSource
	 * @return
	 */
	@Bean("transactionManager")
	public PlatformTransactionManager createTransactionManager(DataSource dataSource){
		return new DataSourceTransactionManager(dataSource);
	}
}

----------------------------------------------------------------------------------------------

4. 测试类

@RunWith(SpringJUnit4ClassRunner.class)

@ContextConfiguration(classes = SpringConfiguration.class)
public class AccountServiceTest {

	@Autowired
	private IAccountService accountService;
	@Test
	public void testTransfer() {
		accountService.transfer("楚留香","西门吹雪",5000d);
	}

}

--------------------------------------------------------------------------------------