---
title: '''tk.mybatis.mapper'''
date: 2020-11-16 10:45:54
tags: Java框架
categories: Java
---

Mybatis 与 Hibernate的一个很大的区别就是Mybatis所有的数据库操作语句都需要自己写，
对于简单的单表操作来说是比较烦琐的。因此有人就开发了tk.mybatis插件，通过这个插件，
可以省略许多简单的单表数据库操作语句而直接调用相对应的dao方法。

<!--more-->

在项目中配置和使用tk.mybatis插件的用法如下：
1. 在pom.xml文件中引入依赖
SSM：
       <dependency>
			<groupId>tk.mybatis</groupId>
			<artifactId>mapper</artifactId>
			<version>3.3.7</version>
		</dependency>
		<dependency>
			<groupId>javax.persistence</groupId>
			<artifactId>persistence-api</artifactId>
			<version>1.0</version>
		</dependency>
		
SpringBoot：
        <dependency>
            <groupId>tk.mybatis</groupId>
            <artifactId>mapper-spring-boot-starter</artifactId>
            <version>1.2.4</version>
        </dependency>
		

2. 	在spring的dao层配置中进行配置，将原本的配置扫描Dao接口包进行如下修改。(SpringBoot框架不需要此步)	
	修改前：
	<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
		<property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"></property>
		<property name="basePackage" value="xxx.xxx.xxx.dao"></property>
	</bean>
	修改后：
    <bean class="tk.mybatis.spring.mapper.MapperScannerConfigurer">
		<property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"></property>
		<property name="basePackage" value="xxx.xxx.xxx.dao"></property>
	</bean>	
	
3. 书写一个基本dao接口（这个类不要放在xxx.xxx.xxx.dao包中），作用是供以后的dao接口继承。
	继承了这个基本dao接口的接口就具有大多数单表操作方法供service层调用。
	import tk.mybatis.mapper.common.Mapper;
	import tk.mybatis.mapper.common.MySqlMapper;

	public interface BaseMapper<T> extends Mapper<T>,MySqlMapper<T>{
	}
	
4. 在xxx.xxx.xxx.pojo包中，进行数据库表和类的映射
（对于数据库表中不存在的变量要用@Transient注解进行忽略映射，否则会报在数据库表找不到对应字段的错误）

	@Table(name = "admin_users")
	public class Admin {

		/**
		 * 使用了tk.mybatis插件后
		 * 在xxx.xxx.xxx.pojo包中，进行数据库表和类的映射
		 * （对于数据库表中不存在的变量要用@Transient注解进行忽略映射，否则会报在数据库表找不到对应字段的错误）
		 */

		@Id
		private Integer id;

		@Column(name = "usertoken")
		private String usertoken;
		@Column(name = "username")
		private String username;
		@Column(name = "password")
		private String password;

		......
	}

5. 之后的xxx.xxx.xxx.mapper包中的mapper接口继承了上面的基本接口就可以拥有供service层调用的大多数单表操作方法了

