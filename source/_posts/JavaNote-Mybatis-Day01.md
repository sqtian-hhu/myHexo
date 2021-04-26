---
title: '''JavaNote_Mybatis_Day01'''
date: 2020-05-09 11:11:33
tags: Java框架
categories: Java
---
## Day1 
mybatis入门
mybatis概述
mybatis环境搭建
mybatis入门案例
自定义mybatis框架

<!--more-->

1. 什么是框架
	整个或部分系统的可重用设计,它是软件开发中的一套解决方案,不同框架解决的是不同的问题
	好处:
		框架封装了很多细节,使开发者可以使用极简的方式实现功能,提高效率
		
2. 三层架构
	表现层:
		用于展示数据
	业务层:
		处理业务要求
	持久层:
		和数据库交互
		
3. 持久层技术解决方案
	JDBC技术:
		Connection
		PreparedStatement
		ResultSet
	Spring的JdbaTemplate:
		Spring中对jdbc的简单封装
	Apache的DBUtils:
		也是对jdbc的简单封装
	
	以上这些都不是框架
		JDBC是规范
		Spring的JdbcTemplate和Apache的DBUtils都只是工具类
		
4. mybatis概述
	mybatis是一个java编写的持久层框架
	MyBatis内部封装了jdbc,使开发者只需要关注sql语句本身,
	而不是花费精力去加载驱动,创建链接,创建statement等繁杂的过程

5. 入门案例
	环境搭建
		第一步: 创建maven工程并导入坐标 pom.xml
		
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.example</groupId>
    <artifactId>com.sqtian.mybatis</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>jar</packaging>

    <dependencies>
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.4.5</version>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.6</version>
        </dependency>
        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.12</version>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.13</version>
        </dependency>


    </dependencies>
</project>
-----------------------------------------------------------------------------------
		
		第二步: 创建实体类和dao接口
		
package com.sqtian.domain;

import java.io.Serializable;

public class User implements Serializable {
	private Integer id;
	private String name;
	private Integer age;
	private double weight;
	private double height;

	@Override
	public String toString() {
		return "com.sqtian.domain.User{" +
			"id=" + id +
			", name='" + name + '\'' +
			", age=" + age +
			", weight=" + weight +
			", height=" + height +
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

	public Integer getAge() {
		return age;
	}

	public void setAge(Integer age) {
		this.age = age;
	}

	public double getWeight() {
		return weight;
	}

	public void setWeight(double weight) {
		this.weight = weight;
	}

	public double getHeight() {
		return height;
	}

	public void setHeight(double height) {
		this.height = height;
	}
}
--------------------------------------------------------------------------------

		第三步: 创建Mybatis的主配置文件 SqlMapConfig.xml

<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<!--mybatis的主配置文件-->
<configuration>
    <!--配置环境-->
    <environments default="mysql">
        <!--配置mysql环境-->
        <enviroment id = "mysql">
            <!--配置事务类型 -->
            <transactionManager type="JDBC"></transactionManager>
            <!--配置数据源(连接池)-->
            <dataSource type="POOLED">
                <!--配置连接数据库的4个基本信息-->
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/lovelycc?serverTimezone=UTC"/>
                <property name="username" value="root"/>
                <property name="password" value="root"/>

            </dataSource>

        </enviroment>
    </environments>

    <!--指定映射配置文件的位置,映射配置文件指的是每个dao独立的配置文件-->
    <mappers>
        <mapper resource="com/sqtian/dao/IUserDao.xml"/>
    </mappers>

</configuration>
----------------------------------------------------------------------------------------
		
		第四步: 创建映射配置文件 IUserDao.xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.sqtian.dao.IUserDao">

    <!--配置查询所有-->
        <select id="findAll">
            select * from hero
        </select>
</mapper>
----------------------------------------------------------------

		
		注意事项:
			mybatis的映射配置文件位置必须和dao接口的包结构相同
			映射配置文件的mapper标签namespace属性的取值必须是dao接口的全限定类名
			映射配置文件的操作配置(select),id属性的取值必须是dao接口的方法名
	
		


案例：
1. 读取配置文件
2. 创建SqlSessionFactory工厂
3. 创建SqlSession
4. 创建Dao接口的代理对象
5. 执行dao中的方法
6. 释放资源


public class MybatisTest {
	public static void main(String[] args) throws Exception {
		//1.读取配置文件
		InputStream inputStream = Resources.getResourceAsStream("SqlMapConfig.xml"); //钱
		//2.创建SqlSessionFactory工厂
		SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder(); //包工头
		SqlSessionFactory factory = builder.build(inputStream); //把钱给包工头建工厂
		//3.使用工厂模式生产SqlSession对象
		SqlSession session = factory.openSession();  //工厂模式的优势，解耦（降低类之间的依赖关系）
		//4.使用SqlSession创建Dao接口的代理对象
		IUserDao userDao = session.getMapper(IUserDao.class); //创建Dao类使用了代理模式，不修改源码的基础上对已有方法增强
		//5.使用代理对象执行方法
		List<User> users = userDao.findAll();
		for (User user : users) {
			System.out.println(user);
		}
		//6.释放资源
		session.close();
		inputStream.close();

	}
}

-------------------------------------------------------------------------
使用注解的方式更加方便
public interface IUserDao {
	/**
	 * 查询所有操作
	 */

	@Select("select * from hero")
	List<User> findAll();

}


sqlMapConfig里
    <!--指定映射配置文件的位置,映射配置文件指的是每个dao独立的配置文件<mapper resource="com/sqtian/dao/IUserDao.xml"/>
        如果使用注解的方式，来配置，此处应该使用class属性指定被注解的dao全限定类名,不需要com.sqtian.dao.IUserDao.xml-->
    <mappers>
        <mapper class="com.sqtian.dao.IUserDao"/>
    </mappers>



6. 自定义Mybatis的分析
	mybatis在使用代理dao的方式实现增删改查时做什么事
	1. 创建代理对象
	2. 在代理对象中调用selectList
	
	自定义mybatis涉及到的
	class Resources
	class SqlSessionFactoryBuilder
	interface SqlSessionFactory
	interface SqlSession
	
	自己创建以上类，参考mybatis源码
