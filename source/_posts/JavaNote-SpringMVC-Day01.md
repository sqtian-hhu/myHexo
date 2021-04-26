---
title: '''JavaNote_SpringMVC_Day01'''
date: 2020-07-08 18:59:08
tags: Java框架
categories: Java
---
## day1

第一章：SpringMVC的基本概念
开发架构一般基于两种形式：C/S架构和B/S架构。JavaEE开发中几乎全都是基于B/S架构的开发。
B/S架构中，标准的三层架构包括：表现层、业务层、持久层

服务器端分成三层框架

三层架构(3-tier architecture) 通常意义上的三层架构就是将整个业务应用划分为：表现层（UI）、业务逻辑层（BLL）、数据访问层（DAL）。区分层次的目的即为了“高内聚，低耦合”的思想。

表现层（user interface layer）：通俗讲就是展现给用户的界面，即用户在使用一个系统的时候他的所见所得。主要对用户的请求接受，以及数据的返回，为客户端提供应用程序的访问。

业务逻辑层（business logic layer）:针对具体问题的操作，也可以说是对数据层的操作，对数据业务逻辑处理。

数据访问层（data access layer）:该层直接操作数据库，针对数据的增添、删除、修改、查找等。

<!--more-->

在Java web 项目中：

dao层：数据访问层,操作数据库，对数据进行增删改查
service层：业务逻辑层,对数据进行处理
web层：表示层，给页面传递数据


表现层：
SpringMVC框架

业务层：
Spring框架

持久层：
Mybatis框架


SpringMVC和Struts2的优略分析




## 第二章：springMVC的入门
需求：
index.jsp 超链接标签->发送请求->编写类,编写方法,转发到成功jsp页面

1. 搭建开发环境

选择webapp项目
创建时添加键值对
archetypeCatalog
internal

导入依赖坐标
  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
    <!-- 版本锁定  锁定版本后，一改全改非常高效-->
    <spring.version>5.0.2.RELEASE</spring.version>
  </properties>



  <dependencies>
    <dependency>

      <groupId>org.springframework</groupId>

      <artifactId>spring-context</artifactId>

      <version>${spring.version}</version>

    </dependency>


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

  </dependencies>

在web.xml中配置前端控制器
<web-app>
  <display-name>Archetype Created Web Application</display-name>
  
  <servlet>
    <servlet-name>dispatcherServlet</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
  </servlet>
  
  <servlet-mapping>
    <servlet-name>dispatcherServlet</servlet-name>
    <url-pattern>/</url-pattern>
  </servlet-mapping>
</web-app>

在resources中添加配置文件


2. 编写入门程序

	1. 编写index.jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <h3>入门程序</h3>

	//超链接标签- 连接到sayhello方法
    <a href="hello">入门程序</a>
</body>
</html>
----------------------------------------------------------------------

	2. 编写hello类

package com.sqtian.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

/**
 * 控制器的类
 * @author sqtian
 * @create 2020-07-07-11:10
 */

@Controller
public class HelloController {

    //请求映射,添加请求路径
    @RequestMapping(path="/hello")
    public String sayHello(){
        System.out.println("Hello StringMVC");
		//跳转到success页面,在webapp.pages下
        return "success";
    }
}	

-------------------------------------------------------------

	3. 在springmvc.xml中配置（相当于之前的bean.xml）
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


    <!--开启注解扫描-->
    <context:component-scan base-package="com.sqtian"/>



</beans>
------------------------------------------------------------------------
	
	4. 在web.xml中前端控制器里配置加载springmvc.xml配置文件
  <servlet>
    <servlet-name>dispatcherServlet</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    
    <init-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>classpath:springmvc.xml</param-value>
    </init-param>
    
    <load-on-startup>1</load-on-startup>
    
  </servlet>	
------------------------------------------------------------------------------

	5. webapp.pages中存放其他页面
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <h3>成功</h3>
</body>
</html>


-----------------------------------------------------------------
	5. 部署到tomcat
	+artifact ->war



流程总结:
1. 启动服务器, 加载一些配置文件
	* DispatcherServlet对象被创建, 前端控制器
	* springmvc.xml被加载
	* HelloController创建对象
	
2. 发送请求,后台处理

<a href="hello">入门程序</a> --> DispatcherServlet 控制作用,指挥中心 --> sayhello方法,控制台打印Hello StringMVC,返回"success" -->DispatcherServlet

--> 找到视图解析器 InternalResourceViewResolver -->跳转到success.jsp页面 --> DispatcherServlet --> 结果返回


@RequestMapping(path="/hello")注解的作用
用于建立请求URL和处理请求方法之间的对应关系
可以加在类上也可以在方法上
	属性:
		value: 用于指定请求的URL,他和path属性作用是一样的
		method: 用于指定请求的方式 
			method={RequestMethod.POST}: 只能响应post请求方式
		params: 用于指定限制请求参数的条件.它支持简单的表达式.要求请求参数的key和value必须和配置一模一样. 
			params={"username"} 必须要传入username的属性
			<a href="user/testRequestMapping?username=hehe">RequestMapping注解</a>
			params={"username=heihei"} 必须传入username=heihei才能请求
		headers:发送的请求当中必须包含请求头
			headers={"Accept"} 
		
加在类上时,要改路径
@Controller
@RequestMapping("/user")
public class HelloController {

    //请求映射,添加请求路径
    @RequestMapping(path="/hello")
    public String sayHello(){
        System.out.println("Hello StringMVC");
        return "success";
    }

    @RequestMapping("/testRequestMapping")
    public String teatRequestMapping(){
        System.out.println("测试RequestMapping注解");
        return "success";
    }
}
------------------------------------------

<a href="user/hello">入门程序</a>

<a href="user/testRequestMapping">RequestMapping注解</a>

-------------------------------------------------------------






## 第三章：请求参数的绑定

<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <%--请求参数绑定--%>

    <a href="param/testParam?username=hehe&password=123">请求参数绑定</a>

</body>
</html>

-------------------------------------------------

/**
 * 请求参数绑定
 * @author sqtian
 * @create 2020-07-08-9:35
 */

@Controller
@RequestMapping("/param")
public class ParamController {


    @RequestMapping("/testParam")
    public String testParam(String username,String password){
        System.out.println("执行了");
        System.out.println("用户名:"+username);
        System.out.println("密码:"+password);
        return "success";
    }
}

-------------------------------------------------------------

封装到java bean中
封装类：
public class Account implements Serializable {
    private String username;
    private String password;
    private Double balance;

    private User user;
    private List<User> list;
    private Map<String,User> map;


	getter&setter
	
	toString
}
------------------------------------------
public class User implements Serializable {
    private String uname;
    private Integer age;

	getter&setter
	
	toString
}

-----------------------------------------------

<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <%--请求参数绑定--%>

    <a href="param/testParam?username=hehe&password=123">请求参数绑定</a>

    <form action="param/saveAccount" method="post">
        <%--数据的name要和封装类的属性名一样--%>
        姓名:<input type="text" name="username" placeholder="请输入用户名"/> <br/>
        密码:<input type="text" name="password" placeholder="请输入密码"/> <br/>
        金额:<input type="text" name="balance" placeholder="请输入金额"/> <br/>
        <%--封装account中的user属性--%>
        user姓名:<input type="text" name="user.uname" placeholder="请输入用户姓名"/> <br/>
        user年龄:<input type="text" name="user.age" placeholder="请输入用户年龄"/> <br/>
        <%--类中存在List和Map集合--%>
        list中user姓名:<input type="text" name="list[0].uname" placeholder="请输入用户姓名"/> <br/>
        list中user年龄:<input type="text" name="list[0].age" placeholder="请输入用户年龄"/> <br/>

        map中user姓名:<input type="text" name="map['one'].uname" placeholder="请输入用户姓名"/> <br/>
        map中user年龄:<input type="text" name="map['one'].age" placeholder="请输入用户年龄"/> <br/>
        <input type="submit" value="提交"/>


    </form>

</body>
</html>

--------------------------------------------------------------------------------------------

解决中文乱码问题：在web.xml中配置过滤器

  <!--配置解决中文乱码的过滤器-->
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


## 自定义类型转换器演示异常

例：输入日期写yyyy/mm/dd支持，写yyyy-mm-dd时报错
需要添加自定义类型转换器


    <%--自定义类型转换器--%>
    <form action="param/saveUser" method="post">
        用户姓名:<input type="text" name="uname"><br/>
        用户年龄:<input type="text" name="age"><br/>
        用户生日:<input type="text" name="date"><br/>

        <input type="submit" value="提交"/>

    </form>
-----------------------------------------------------------------

    @RequestMapping("/saveUser")
    public String saveUser(User user){
        System.out.println("执行了");
        System.out.println(user);
        return "success";
    }
	
------------------------------------------------------------------

在springmvc.xml中配置
    <!--配置自定义类型转换器-->
    <bean id="conversionService" class="org.springframework.context.support.ConversionServiceFactoryBean">
        <property name="converters">
            <set>
                <bean class="com.sqtian.utils.StringToDateConverter"></bean>
            </set>
        </property>

    </bean>

    <!--开启springmvc框架注解支持-->
    <mvc:annotation-driven conversion-service="conversionService"/>


-----------------------------------------------------------------------------
public class StringToDateConverter implements Converter<String, Date> {


    /**
     *
     * @param s 传入进来的字符串
     * @return
     */
    @Override
    public Date convert(String s) {
        //判断
        if(s == null){
            throw new RuntimeException("请您传入数据");
        }
        DateFormat df = new SimpleDateFormat("yyyy-MM-dd");
        //把字符串转换成日期
        try {
            return df.parse(s);
        } catch (Exception e) {
            throw new RuntimeException("数据类型转换出现了错误");
        }
        }
}
--------------------------------------------------------------------



## 获取原生的api

    <a href="param/testServlet">servlet原生的api</a>



    //想要获取什么,就传入什么参数
    @RequestMapping("/testServlet")
    public String testServlet(HttpServletRequest request, HttpServletResponse response){
        System.out.println("执行了");

        System.out.println(request);
        HttpSession session = request.getSession();
        System.out.println(session);
        ServletContext servletContext = session.getServletContext();
        System.out.println(servletContext);

        System.out.println(response);

        return "success";
    }


## 第四章：常用注解
1. RequestParam
	作用：把请求中指定名称的参数给控制器中的形参赋值
	属性：
		value：请求参数中的名称
		required：请求参数中是否必须提供此参数，默认值true


<a href="anno/testRequestParam?name=哈哈">RequestParam</a>
请求中的参数是name，方法中的形参是username，可以通过(@RequestParam(name = "name")赋值

@Controller
@RequestMapping("/anno")
public class AnnoController {

    @RequestMapping("/testRequestParam")
    public String testRequestParam(@RequestParam(name = "name") String username){
        System.out.println("执行了");
        System.out.println(username);
        return "success";
    }
}


2. RequestBody
	作用：用于获取请求体内容。直接使用得到是key=value&key=value。。。结构的数据
	get请求方式不适用
	属性：	
		required：是否必须有请求体 默认true。true时get请求方式会报错，false时，get请求得到的是null
		


    <form action="anno/testRequestBody" method="post">
        用户姓名:<input type="text" name="username"/><br/>
        用户年龄:<input type="text" name="age"/><br/>

        <input type="submit" value="提交"><br/>
    </form>
--------------------------------------------------------------

    @RequestMapping("/testRequestBody")
    public String testRequestBody(@RequestBody String body){
        System.out.println("执行了");
        System.out.println(body);
        return "success";
    }
--------------------------------------------------------------


3. PathVaribale
	作用：用于绑定url中的占位符。例如：请求url中 /delete/{id}，这个{id}就是url占位符

	属性：	
		value：用于指定url中占位符名称
		required：是否必须提供占位符
		
    <a href="anno/testPathVariable/10">PathVariable</a>

    @RequestMapping("/testPathVariable/{sid}")
    public String testPathVariable(@PathVariable(name = "sid") String id) {
        System.out.println("执行了");
        System.out.println(id);
        return "success";
    }
---------------------------------------------------------
	
		
restful编程风格	
	原来方式                                            
	UserController类
	
	path="/user/save"
	save
	
	path="/user/update"
	update
	
	path="/user/findAll"
	findAll
	
	restful方式
	UserController类
	
	path="/user"  post
	save
	
	path="/user"  put
	update
	
	path="/user"  get
	findAll

	path="/user/{id}"   get
	findById(id)

--------------------------------------------------


4. RequestHeader
用的较少
	作用: 用于获取请求消息头
	属性:
		value: 提供消息头名称
		required: 是否必须有此消息头
		

    <a href="anno/testRequestHeader">RequestHeader</a>
	
-----------------------------------------------------------

    @RequestMapping("/testRequestHeader")
    public String testRequestHeader(@RequestHeader(value = "Accept") String header) {
        System.out.println("执行了");
        System.out.println(header);
        return "success";
    }	

-----------------------------------------------------------

5. CookieValue注解
	作用: 把指定cookie名称的值传入控制器方法参数
	属性: 
		value: 指定cookie的名称
		required: 是否必须有此cookie
用的较少
    <a href="anno/testCookieValue">CookieValue</a>

----------------------------------------------------------------


    @RequestMapping("/testCookieValue")
    public String testCookieValue(@CookieValue(value = "JSESSIONID") String cookieValue) {
        System.out.println("执行了");
        System.out.println(cookieValue);
        return "success";
    }
	
------------------------------------------------------------------

6. ModelAttribute
	作用: 出现在方法上,表示当前方法会在控制器的方法执行前先执行
		  出现在参数上,获取指定的数据给参数赋值
		  
	属性:
		value: 用于获取数据的key. key可以是POJO的属性名称,也可以是map结构的key
		
	应用场景:
		当表单提交数据不是完整的实体类数据时,保证没有提交数据的字段使用数据库对象原来的数据
		
    <form action="anno/testModelAttribute" method="post">
        用户姓名:<input type="text" name="uname"/><br/>
        用户年龄:<input type="text" name="age"/><br/>

        <input type="submit" value="提交"><br/>
    </form>
	
------------------------------------------------------------------
	有返回值,放在方法上时
	
    @RequestMapping("/testModelAttribute")
    public String testModelAttribute(User user) {
        System.out.println("testModelAttribute执行了");
        System.out.println(user);
        return "success";
    }

    //放方法上优先执行,showUser打印在testModelAttribute前


    //有返回值
    @ModelAttribute
    public User showUser(String uname){
        System.out.println("showUser执行了");
        //先通过uname查到数据库中的数据
        User user = new User();
        user.setUname(uname);
        user.setAge(20);
        user.setDate(new Date());
        return user;
    }

-------------------------------------------------------------	
	没有返回值时

    @RequestMapping("/testModelAttribute")
    public String testModelAttribute(@ModelAttribute("abc") User user) {
        System.out.println("testModelAttribute执行了");
        System.out.println(user);
        return "success";
    }
    //没有返回值
    @ModelAttribute
    public void showUser(String uname, Map<String,User> map){
        System.out.println("showUser执行了");
        //先通过uname查到数据库中的数据
        User user = new User();
        user.setUname(uname);
        user.setAge(20);
        user.setDate(new Date());
        map.put("abc",user);

    }
	
----------------------------------------------------------

7. SessionAttributes
	作用: 用于多次执行控制器方法间的参数共享
	属性:
		value: 用于指定存入的属性名称
		type: 用于指定存入的数据类型
		

---------------------------------------------------------------
    <a href="anno/testSessionAttributes">SessionAttributes</a>

    <br/>
    <a href="anno/getSessionAttributes">getSessionAttributes</a><br/>
    <a href="anno/delSessionAttributes">delSessionAttributes</a><br/>
	
------------------------------------------------------------------------

    @RequestMapping("/testSessionAttributes")
    public String testSessionAttributes(Model model) {
        System.out.println("执行了");
        //底层会存储到request域对象中
        model.addAttribute("msg","妹妹");
        return "success";
    }


    //从session域中取值
    @RequestMapping("/getSessionAttributes")
    public String getSessionAttributes(ModelMap modelMap) {
        System.out.println("执行了");

        String msg = (String) modelMap.get("msg");
        System.out.println(msg);
        return "success";
    }

    //清除session域中的数据
    @RequestMapping("/delSessionAttributes")
    public String delSessionAttributes(SessionStatus status) {
        System.out.println("执行了");
        status.setComplete();
        return "success";
    }
-----------------------------------------
	只能作用在类上
@Controller
@RequestMapping("/anno")
@SessionAttributes(value="msg") //把msg=妹妹存入到session域对中
public class AnnoController {	
...
}
--------------------------------------------