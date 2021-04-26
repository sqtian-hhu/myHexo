---
title: '''JavaWebNote05_Tomcat'''
date: 2020-05-13 11:26:01
tags: JavaWeb
categories: Java
---
Tomcat相关知识
<!--more-->

## Tomcat

1.  web相关概念回顾

	1. 软件架构
		1. C/S：客户端/服务器端
		
		2. B/S：浏览器/服务器端
		
	2. 资源分类
		1. 静态资源：所有用户访问后，得到的结果都是一样的，称为静态资源，静态资源可以直接被浏览器解析
			如：html，css，JavaScript

		2. 动态资源：每个用户访问相同资源后，得到的结果可能不一样。称为动态资源。动态资源被访问后，需要先转换为静态资源，再返回给浏览器
			如：servlet/jsp，php，asp.....
	3. 网络通信三要素
		1. IP: 电子设备(计算机)在网络中的唯一标识. 
		2. 端口: 应用程序在计算机中的唯一标识. 0-65536
		3. 传输协议: 规定了数据传输的规则
			1. 基础协议:
				1. tcp: 安全协议,三次握手. 速度稍慢
				2. udp: 不安全协议, 速度快
				
2. web服务器软件
	* 服务器: 安装了服务器软件的计算机
	* 服务器软件: 接受用户的请求,处理请求,做出响应
	* web服务器软件: 可以部署web项目,让用户通过浏览器来访问这些项目
		也称作web容器,动态资源一定要依赖web服务器软件运行
	* 常见的java相关的web服务器软件:
		* webLogic: oracle公司,大型JavaEE服务器,支持所有的JavaEE规范,收费
		* webSphere: IBM公司,大型JavaEE服务器,支持所有的JavaEE规范,收费
		* JBOSS: JBOSS公司,大型JavaEE服务器,支持所有的JavaEE规范,收费
		* TomCat: Apache基金组织, 中小型的JavaEE服务器,仅仅支持少量的JavaEE规范. 开源免费
		
	
	* JavaEE: java语言在企业级开发中使用的技术规范的总和,一共规定了13项大的规范

	* Tomcat: web服务器软件
		目录结构
			bin：可执行文件
			conf： 配置文件
			lib：依赖jar包
			logs：日志文件
			temp：临时文件
			webapps：存放web项目
			work：存放运行时的数据
			
		启动：
			可能遇到的问题
				1. 一闪而过：
					环境变量没有正确配置
				2. 启动报错：
					1. 暴力：找到占用的端口号，并且找到对应的进程，杀死该进程
						cmd netstat -ano
					2. 温柔：修改自身端口号
						D:\Tomcat\apache-tomcat-8.5.54\conf\server.xml
						
						一般会将tomcat的默认端口号修改为80,因为80端口号是http协议的默认端口号
							好处: 在访问时,就不用输入端口号了
		关闭:
			1. 正常关闭:
				1. bin/shutdown
				2. ctrl+c
			2. 强制关闭:
				x

		部署:
			1. 直接将项目放到webapps目录下即可
				http://localhost/hello/HelloWorld.html
				* 简化部署: 
					将项目打包成war包,war包拷贝过去会自动解压缩
					
			2. 配置conf/server.xml文件
				在<Host>标签体中配置
				<Context docBase="D:\hello" path="/hehe" />
				* docBase: 项目存放路径
				* path: 虚拟目录
				http://localhost/hehe/HelloWorld.html
				
				缺点: 任意破坏全局的tomcat配置文件,不推荐
				
			3. 在D:\Tomcat\apache-tomcat-8.5.54\conf\Catalina\localhost创建任意名称的xml文件,在文件中编写
				<Context docBase="D:\hello" />
				* 虚拟目录就是xml文件的名称
				http://localhost/bbb/HelloWorld.html
			
		
			* 静态项目和动态项目
				*目录结构
					*java动态项目的目录结构
						-- 项目的根目录
							-- WEB-INF目录:
								-- web.xml: web项目的核心配置文件
								-- classes目录: 放置字节码文件的目录
								-- lib目录:防止依赖的jar包
								
			* 将tomcat集成到IDEA中,并且创建javaEE的项目,部署项目
			
			
## Servlet				
	1. 概念: server applet, 运行在服务器端的小程序
	
		* servlet就是一个接口,定义了java类被浏览器访问到(tomcat识别)的规则
		* 自定义一个类,实现Servlet接口,复写方法.
	
	2. 快速入门:
		1. 创建JavaEE项目
		2. 定义一个类,实现Servlet接口
			public class ServletDemo01 implements Servlet
		3. 实现接口中的抽象方法
		4. 配置Servlet
			web.xml
			

	3. 执行原理
		1. 当服务器接收到客户端浏览器的请求后,会解析请求URL路径,获取访问的Servlet的资源路径
		2. 查找web.xml文件,是否有对应的<url-pattern>标签体内容
		3. 如果有,则再找到对应的<servlet-class>全类名
		4. tomcat会将字节码文件加载进内存,并且创建其对象
		5. 调用其方法
		
	4. Servlet中的方法
		* init()
			初始化方法,在Servlet被创建时执行,只会执行一次
			
		* service()
			提供服务的方法,每一次Servlet被访问时执行. 访问一次执行一次

		* destroy()
			销毁方法,在服务器正常关闭时执行,执行一次. 
		

		
		* getServletConfig()
			获取Servlet的配置对象
			
		* getServletInfo()
			获取Servlet的一些信息,版本,作者等等
			


		生命周期
			1. 被创建: 执行init方法, 只执行一次
			一般用于加载资源
				默认情况下,第一次被访问时,Servlet被创建
				可以配置web.xml改变执行Servlet的创建时机
					在<servlet>标签下配置
					1. 第一次被访问时创建
						<load-on-startup>的值为负数
					2. 在服务器启动时创建
						<load-on-startup>的值为0或正整数
				
				Servlet的init方法只执行一次,说明一个Servlet在内存中只存在一个对象,Servlet是单例的
					多个用户同时访问时,可能存在线程安全问题
					解决方法: 尽量不要再servlet中定义成员变量, 使用局部变量. 即使定义了成员变量,也不要对其修改值
						
			2. 提供服务: 执行service方法, 可执行多次
				每次访问Servlet时都会被调用一次
			3. 被销毁: 执行destroy方法, 只执行一次
				Servlet被销毁时执行,服务器关闭时Servlet被销毁
				只有服务器正常关闭,才会执行destroy,在被销毁之前执行,一般用于释放资源

	5. Servlet3.0
		好处
			* 支持注解配置,可以不需要web.xml了
			
			步骤:
			1. 创建JavaEE项目, 选择Servlet的版本3.0以上, 可以不创建web.xml
			2. 定义一个类实现Servlet接口
			3. 复写方法
			4. 在类上使用@WebServlet注解,进行配置
				WebServlet("虚拟资源路径")
	
	6. IDEA与tomcat的相关配置
		1. IDEA会为每一个tomcat部署的项目建立一份配置文件
			查看控制台的log: Using CATALINA_BASE: " "
			后面的目录打开
		2. 工作空间项目和tomcat部署的web项目
			* tomcat真正访问的是"tomcat部署的web项目",对应着"工作空间项目"的web目录下的所有资源
			* WEB-INFO目录下的资源不能被浏览器直接访问, 所以文件写在web目录下,不要写在WEB-INFO下
			* 断点调试: 打上断点不能直接run,要使用debug启动
			



Servlet 体系结构
	Servlet --接口
	
	GenericServlet --实现类Servlet接口的抽象类
		将Servlet接口中其他的方法做了默认空实现，只将service()方法作为抽象方法
		将来定义Servlet类时,可以继承GenericServlet,实现service()方法即可
		
	HttpServlet --抽象类
		直接实现servlet的service需要先判断请求方式
		String method = req.getMethod();
		if("GET".equals(method)){
			//get方式获取数据
		}else if ("POST".equals(method)){
			//post方式获取数据
		}
		
		HttpServlet是对http协议的一种封装,简化操作
		不再复写service,而是复写doGet/doPost方法
		
	推荐使用HttpServlet

Servlet相关配置
	1. urlpartten:Servlet访问路径
		1. 一个Servlet可以定义多个访问路径: @WebServlet({"/d4","/dd4","/ddd4"})
		2. 路径定义规则:
			1. /xxx
			2. /xxx/xxx: 多层路径,目录结构
			3. *.do  :localhost/xxx.do
			
