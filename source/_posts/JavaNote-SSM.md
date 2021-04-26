---
title: '''JavaNote_SpringMVC_Day03'''
date: 2020-07-08 19:01:00
tags: Java框架
categories: Java
---
## Day3 SSM三大框架整合


第一步：搭建整合环境
SSM整合可以使用多种方式，我们选用XML+注解的方式

整合思路：
	1. 搭建环境
	2. 搭建Spring配置
	3. 使用Spring整合SpringMVC框架
	4. 使用Spring整合MyBatis框架
	
<!--more-->

服务器开发，分成3层


表现层
	SpringMVC框架

业务层
	Spring框架

持久层
	MyBatis框架
	
	
一定是用Spring去整合另外两个框架




--------------------------------------------------------------------------------------------
	1.导入依赖坐标
	
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
        <!-- 版本锁定  锁定版本后，一改全改非常高效-->
        <spring.version>5.0.2.RELEASE</spring.version>
        <slf4j.version>1.6.6</slf4j.version>
        <log4j.version>1.2.12</log4j.version>
        <mysql.version>5.1.6</mysql.version>
        <mybatis.version>3.4.5</mybatis.version>

    </properties>


    <dependencies>
    <!--Spring-->

        <!--AOP相关jar包-->
        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
            <version>1.8.7</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-aop</artifactId>
            <version>${spring.version}</version>
        </dependency>


        <!--context容器-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>${spring.version}</version>
        </dependency>


        <!--SpringMVC需要的jar包-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-web</artifactId>
            <version>${spring.version}</version>
        </dependency>


        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>${spring.version}</version>
        </dependency>


        <!--测试-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>${spring.version}</version>
        </dependency>


        <!--事务相关jar包-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-tx</artifactId>
            <version>${spring.version}</version>
        </dependency>


        <!--jdbc-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>${spring.version}</version>
        </dependency>



<!--servlet-->
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>servlet-api</artifactId>
            <version>2.5</version>
            <scope>provided</scope>
        </dependency>

        <dependency>
            <groupId>javax.servlet.jsp</groupId>
            <artifactId>jsp-api</artifactId>
            <version>2.0</version>
            <scope>provided</scope>
        </dependency>


<!--连接数据库-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>${mysql.version}</version>
        </dependency>

        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>compile</scope>
        </dependency>


<!--写页面表达式需要用-->
        <dependency>
            <groupId>jstl</groupId>
            <artifactId>jstl</artifactId>
            <version>1.2</version>
        </dependency>


<!--日志打印-->
        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>${log4j.version}</version>
        </dependency>

        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>${slf4j.version}</version>
        </dependency>

<!--mybatis框架jar包-->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>${mybatis.version}</version>
        </dependency>

        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
            <version>1.3.0</version>
        </dependency>

<!--c3p0连接池-->
        <dependency>
            <groupId>c3p0</groupId>
            <artifactId>c3p0</artifactId>
            <version>0.9.1.2</version>
            <type>jar</type>
            <scope>compile</scope>
        </dependency>


    </dependencies>
	
--------------------------------------------------------------------------------------------------


	2. 创建封装类
public class Account implements Serializable {

    private Integer id;
    private String name;
    private Double money;


    @Override
    public String toString() {
        return "Account{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", money=" + money +
                '}';
    }

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

    public Double getMoney() {
        return money;
    }

    public void setMoney(Double money) {
        this.money = money;
    }
}
----------------------------------------------

	3. 创建dao接口
	应用mybatis框架，只需要写接口，框架会帮我们生成代理对象去执行，不需要自己写实现类
	
	
/**
 * @author sqtian
 * @create 2020-07-13-10:09
 */
public interface IAccountDao {

    //查询所有
    public List<Account> findAll();


    //保存账户信息
    public void saveAccount(Account account);
}

----------------------------------------------------------------------------
	4. 创建Service接口与实现类

public interface IAccountService {

    //查询所有
    public List<Account> findAll();


    //保存账户信息
    public void saveAccount(Account account);

}
-----------------------------------------------	
public class AccountServiceImpl implements IAccountService {
    @Override
    public List<Account> findAll() {
        System.out.println("业务层: 查询所有");
        return null;
    }

    @Override
    public void saveAccount(Account account) {
        System.out.println("业务层: 保存账户");
    }
}

----------------------------------------------------------------------------

	5. 创建controller类

/**
 *
 * 账户web层
 * @author sqtian
 * @create 2020-07-13-10:17
 */
public class AccountController {



}
----------------------------------------------------------------------------------


环境和骨架搭建完成


要用spring去整合其他框架
第二步：搭建spring环境
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="
       http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/tx
       http://www.springframework.org/schema/tx/spring-tx.xsd
       http://www.springframework.org/schema/aop
       http://www.springframework.org/schema/aop/spring-aop.xsd">


    <!--开启扫描,希望处理service和dao. controller不需要spring框架去处理-->
    <context:component-scan base-package="com.sqtian">
        <!--配置哪些注解不扫描-->
        <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
    </context:component-scan>

</beans>

----------------------------------------------------------------------------------------------------------------------
通过测设方法检查配置
package com.sqtian.test;

import com.sqtian.service.IAccountService;
import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

/**
 * @author sqtian
 * @create 2020-07-13-11:15
 */

public class TestSpring {

    @Test
    public void run1() {
        //加载配置文件
        ApplicationContext ac = new ClassPathXmlApplicationContext("spring.xml");

        //获取对象

        IAccountService accountService = (IAccountService) ac.getBean("accountService");

        //调用方法

        accountService.findAll();
    }

}
-------------------------------------------------------------------------------------------------
在resources里添加log4j.properties让日志正常打印
log4j.rootLogger=INFO,CONSOLE

log4j.addivity.org.apache=true



# console

log4j.appender.CONSOLE=org.apache.log4j.ConsoleAppender

log4j.appender.CONSOLE.Threshold=INFO

log4j.appender.CONSOLE.Target=System.out

log4j.appender.CONSOLE.Encoding=UTF-8

log4j.appender.CONSOLE.layout=org.apache.log4j.PatternLayout

log4j.appender.CONSOLE.layout.ConversionPattern=[demo] %-5p %d{yyyy-MM-dd HH\:mm\:ss} - %C.%M(%L)[%t] - %m%n



# all

log4j.logger.com.demo=INFO, DEMO

log4j.appender.DEMO=org.apache.log4j.RollingFileAppender

log4j.appender.DEMO.File=${catalina.base}/logs/demo.log

log4j.appender.DEMO.MaxFileSize=50MB

log4j.appender.DEMO.MaxBackupIndex=3

log4j.appender.DEMO.Encoding=UTF-8

log4j.appender.DEMO.layout=org.apache.log4j.PatternLayout

log4j.appender.DEMO.layout.ConversionPattern=[demo] %-5p %d{yyyy-MM-dd HH\:mm\:ss} - %C.%M(%L)[%t] - %m%n

------------------------------------------------------------------------------------------------------------------------------

第三步：编写SpringMVC框架

	在web.xml中配置前端控制器

<?xml version="1.0" encoding="UTF-8"?>

<web-app>
    <display-name>Archetype Created Web Application</display-name>


    <!--配置前端控制器-->

    <servlet>
        <servlet-name>dispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <!--加载springmvc.xml配置文件-->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springmvc.xml</param-value>
        </init-param>
        <!--启动服务器就创建该servlet-->
        <load-on-startup>1</load-on-startup>
    </servlet>

    <servlet-mapping>
        <servlet-name>dispatcherServlet</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
    
    
    <!--解决中文乱码的过滤器-->
    <filter>
        <filter-name>characterEncodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>UTF-8</param-value>
        </init-param>
    </filter>
    
    <filter-mapping>
        <filter-name>characterEncodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
    
</web-app>
        
----------------------------------------------------------------------------------------

pringmvc.xml

<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="
       http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/mvc
       http://www.springframework.org/schema/mvc/spring-mvc.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd">
    
    <!--开启注解扫描,只扫描controller注解-->
    <context:component-scan base-package="com.sqtian">
        <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
    </context:component-scan>
    
    <!--配置视图解析器对象-->
    <bean id="internalResourceViewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/pages/"/>
        <property name="suffix" value=".jsp"/>
    </bean>
    
    
    <!--过滤静态资源-->
    <mvc:resources mapping="/js/**" location="/js/"/>
    <mvc:resources mapping="/images/**" location="/images/"/>
    <mvc:resources mapping="/css/**" location="/css/"/>
    
    <!--开启SpringMVC注解的支持-->
    <mvc:annotation-driven/>
    
</beans>
--------------------------------------------------------------------------------------------------------------------


测试springmvc搭建环境

index.jsp

<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>

    <a href="account/findAll">测试</a>

</body>
</html>

---------------------------------------------------------

AccountController

/**
 * 账户web层
 *
 * @author sqtian
 * @create 2020-07-13-10:47
 */

@Controller
@RequestMapping("/account")
public class AccountController {

    @RequestMapping("/findAll")
    public String findAll(){
        System.out.println("表现层:查询所有账户");
        return "list";
    }
}

---------------------------------------------------

<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>list</title>
</head>
<body>

    <h3>查询所有账户</h3>
</body>
</html>

----------------------------------------------------------------------------

springmvc的环境搭建完成


第四步: Spring整合SpringMVC

	启动tomcat服务器的时候,需要加载spring的配置文件
	servletContext域对象
	服务器启动的时候创建ServletContext对象服务器关闭才销毁
	有一类监听器,监听ServletContext域对象的创建和销毁. 执行一次,服务器启动执行
	监听器去加载Spring的配置文件
	创建WEB版本的工厂,存储ServletContext对象
	
	1. 在web.xml中配置监听器
	
    <!--配置Spring的监听器,默认只加载WEB-INF目录下的applicationContext.xml配置文件-->

    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>

    </listener>
    <!--设置配置文件的路径-->
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:spring.xml</param-value>
    </context-param>
-----------------------------------------------------------------------
	
	2. 表现层调用业务层
@Controller
@RequestMapping("/account")
public class AccountController {

    @Autowired
    private IAccountService accountService;

    @RequestMapping("/findAll")
    public String findAll(){
        System.out.println("表现层:查询所有账户");

        //调用service方法
        accountService.findAll();

        return "list";
    }
}
----------------------------------------------------------------------------

整合成功
---------------------------------------------------------------------------------------------------------

第五步: 编写mybatis框架

	1. dao中添加注解

public interface IAccountDao {

    //查询所有
    @Select("select * from account")
    public List<Account> findAll();


    //保存账户信息
    @Insert("insert into account (name,money) values (#{name},#{money})")
    public void saveAccount(Account account);
}
------------------------------------------------------------------------

	2. 添加mybatis核心配置文件 SqlMapConfig.xml

<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<!--mybatis的主配置文件-->
<configuration>
    <!--配置环境-->
    <environments default="mysql">
        <!--配置mysql环境-->
        <environment id="mysql">
            <!--配置事务类型 -->
            <transactionManager type="JDBC"></transactionManager>
            <!--配置数据源(连接池)-->
            <dataSource type="POOLED">
                <!--配置连接数据库的4个基本信息-->
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/ssm?characterEncoding=utf8"/>
                <property name="username" value="root"/>
                <property name="password" value="root"/>

            </dataSource>
        </environment>
    </environments>

    <!--指定映射配置文件的位置,映射配置文件指的是每个dao独立的配置文件-->
    <mappers>
        <mapper class="com.sqtian.dao.IAccountDao"/>
    </mappers>

</configuration>

---------------------------------------------------------------------------------------------

测试框架环境
public class testMybatis {

    //测试查询方法
    @Test
    public void run1() throws Exception {
        //加载配置文件
        InputStream in = Resources.getResourceAsStream("SqlMapConfig.xml");
        //创建SqlSessionFactory对象
        SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(in);
        //创建SqlSession对象
        SqlSession sqlSession = factory.openSession();
        //获取代理对象
        IAccountDao dao = sqlSession.getMapper(IAccountDao.class);
        //查询所有数据
        List<Account> all = dao.findAll();

        for (Account account : all) {
            System.out.println(account);
        }

        //关闭资源
        sqlSession.close();
        in.close();


    }

    //测试保存方法
    @Test
    public void run2() throws Exception {
        Account account = new Account();
        account.setName("西门吹雪");
        account.setMoney(10000d);



        //加载配置文件
        InputStream in = Resources.getResourceAsStream("SqlMapConfig.xml");
        //创建SqlSessionFactory对象
        SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(in);
        //创建SqlSession对象
        SqlSession sqlSession = factory.openSession();
        //获取代理对象
        IAccountDao dao = sqlSession.getMapper(IAccountDao.class);

        //保存
        dao.saveAccount(account);

        //增删改需要自己管理事务
        sqlSession.commit();

        //关闭资源
        sqlSession.close();
        in.close();
    }
}

-------------------------------------------------------------------------------------------
出现中文添加失败的问题,需要改表的编码: ALTER TABLE account CONVERT TO CHARACTER SET utf8;

mybatis环境搭建完成


第六步: spring整合mybatis

把dao的代理对象存到容器中,利用spring IOC注入

在spring.xml中进行配置

<!--spring整合Mybatis框架-->

    <!--配置连接池-->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="com.mysql.jdbc.Driver"/>
        <property name="jdbcUrl" value="jdbc:mysql:///ssm?characterEncoding=utf8"/>
        <property name="user" value="root"/>
        <property name="password" value="root"/>
    </bean>
    <!--配置SqlSessionFactory工厂-->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource"/>
    </bean>
    <!--配置AccountDao接口所在包-->
    <bean id="mapperScanner" class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="com.sqtian.dao"/>
    </bean>
	
---------------------------------------------------------------------------------------------
有了以上配置,SqlMapConfig就可以删除了

mybatis整合完成






IAccountDao添加注解@Repository

AccountServiceImpl 添加dao注入

@Service("accountService")
public class AccountServiceImpl implements IAccountService {

    @Autowired
    private IAccountDao accountDao;

    @Override
    public List<Account> findAll() {
        System.out.println("业务层: 查询所有");
        return accountDao.findAll();
    }

    @Override
    public void saveAccount(Account account) {
        System.out.println("业务层: 保存账户");
        accountDao.saveAccount(account);
    }
}
--------------------------------------------------------------


存到request域输出到页面

@Controller
@RequestMapping("/account")
public class AccountController {

    @Autowired
    private IAccountService accountService;

    @RequestMapping("/findAll")
    public String findAll(Model model){
        System.out.println("表现层:查询所有账户");

        //调用service方法
        List<Account> list = accountService.findAll();

        //存入model
        model.addAttribute("list",list);

        return "list";
    }
}

-----------------------------------------------------------------------
<%@ page contentType="text/html;charset=UTF-8" language="java" isELIgnored="false" %>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<html>
<head>
    <title>list</title>
</head>
<body>

    <h3>查询所有账户</h3>


    <c:forEach items="${list}" var="account">
        ${account.name}
    </c:forEach>

</body>
</html>


----------------------------------------------------------------------------------------------------


保存账户
增删改需要自己管理事务

在spring.xml中配置声明式事务管理

    <!--配置spring框架声明式事务管理-->
    <!--配置事务管理-->
    <bean id="transactionManger" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"/>
    </bean>

    <!--配置事务通知-->
    <tx:advice id="txAdvice" transaction-manager="transactionManger">
        <tx:attributes>
            <tx:method name="find*" read-only="true"/>
            <tx:method name="*" read-only="false"/>
        </tx:attributes>
    </tx:advice>


    <!--配置AOP增强-->
    <aop:config>
        <!--配置切入点表达式-->
        <aop:pointcut id="pt1" expression="execution(* com.sqtian.service.impl.*.*(..))"/>

        <aop:advisor pointcut-ref="pt1" advice-ref="txAdvice"/>

    </aop:config>
	
---------------------------------------------------------------------------------------------
    @RequestMapping("/save")
    public void save(Account account, HttpServletRequest request, HttpServletResponse response) throws Exception {
        System.out.println("表现层:保存账户");
        accountService.saveAccount(account);

        //重定向去查询
        response.sendRedirect(request.getContextPath() +"/account/findAll");

        return;
    }
}

-------------------------------------------------------------------------------------------------------------




