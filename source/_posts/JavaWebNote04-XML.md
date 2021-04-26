---
title: '''JavaWebNote04_XML'''
date: 2020-05-11 17:21:00
tags: JavaWeb
categories: Java
---
## XML
1. 概念：Extensible Markup Language 可扩展标记语言
	可扩展：标签都是自定义的
	
	功能：
	存储数据
		1. 配置文件
		2. 在网络中传输

	与HTML的区别
	1. xml标签都是自定义的，html标签是预定义的
	2. xml的语法严格，html语法松散
	3. xml是存储数据的，html是展示数据的
<!--more-->	
2. 语法
	基本语法：
		1. xml文档的后缀名 .xml
		2. xml第一行必须定义为文档声明
		3. 有且仅有一个根标签
		4. 属性必须用引号引起来
		5. 标签必须正确关闭
		6. xml标签名称区分大小写
		
	快速入门：
	
<?xml version='1.0' ?>

<users>
	<user id='1'>
		<name>zhangsan</name>
		<age>23<age>
		<gender>male</gender>
	</user>
	
	<user id='2'>
		<name>lisi</name>
		<age>24<age>
		<gender>male</gender>
	</user>

</users>

	组成部分：
		1. 文档声明:
			1. 格式:<?xml 属性列表 ?>
			2. 属性列表:
				*version: 版本号, 必须的属性
				*encoding: 编码格式, 告知解析引擎当前文档使用的字符集
				*standalone: 是否独立,yes 不依赖其他文件 no 依赖其他文件
				
		2. 指令: 结合css的
			*<?xml-stylesheet type="text/css" href="a.css" ?>
		3. 标签: 标签名称自定义的
		4. 属性:
			id属性值唯一
		5. 文本:
			CDATA区:在该区域中的数据会被原样展示
				格式:<![CDATA[ 数据 ]]>
				
	约束:
		约束文档:规定xml文档的书写规则
		作为框架的使用者,能够在xml中引入约束文档,能够简单的读懂约束文档
		
		分类:
			1. DTD: 一种简单的约束技术
				引入DTD文档到xml文档中
				内部dtd: 将约束规则定义在xml文档中
				外部dtd: 将约束的规则定义在外部dtd文档中
					本地: <!DOCTYPE 根标签名 SYSTEM "dtd文件的位置">
					网络: <!DOCTYPE 根标签名 PUBLIC "dtd文件名字" "dtd文件的位置URL">
			2. Schema: 一种复杂的约束技术
				引入:
					1. 填写xml文档的根元素
					2. 引入xsi前缀
					3. 引入xsd文件命名空间
					4. 为每一个xsd约束声明一个前缀,作为标识
					
3. 解析
操作xml文档,将文档中的数据读取到内存中

*操作xml文档
	1. 解析(读取): 将文档中的数据读取到内存中
	2. 写入: 将内存中的数据保存到xml文档中,持久化的存储
	
*解析xml的方式
	1. DOM: 将标记语言文档一次性加载进内存,在内存中会形成一棵DOM树
		优点: 操作方便,可以对文档进行CRUD所有操作
		缺点: 占内存
	2. SAX: 逐行读取,基于事件驱动
		优点: 不占内存
		缺点: 只能读取,不能增删改
		
*xml常见解析器
	1. JAXP: sun公司提供,支持dom和sax两种思想,基本没人用
	2. DOM4J: 一款优秀的解析器
	3. Jsoup: html解析器,也可以解析xml
	4. PULL: Android操作系统内置解析器,sax方式的
	
	
*Jsoup示例
步骤:
	1. 导入jar包
	2. 获取Document对象,根据xml文档获取
		获取student.xml的path
		String path = JsoupDemo1.class.getClassLoader().getResource("student.xml").getPath();
		Document document = Jsoup.parse(new File(path),"utf-8");
	3. 获取对象标签Element对象
		Elements elements = document.getElementsByTag("name")
		获取第一个name的Element对象
		Element element = elements.get(0);
	4. 获取数据
		String name = element.text();
		sout(name);
		
对象的使用:
	1. jSOUP: 工具类,可以解析html或xml文档,返回Document
	2. Document: 文档对象. 代表内存中的dom树
	3. Elements: 元素Element对象的集合,可以当作ArrayList<Element> 来使用
	4. Element: 元素对象
	5. Node: 节点对象