---
title: '''JavaWebNote01_HTML'''
date: 2020-04-19 15:21:26
tags: JavaWeb
categories: Java
---
## web概念概述
	* JaveWeb：
		使用Java语言开发基于互联网的项目
	
	* 软件架构
		1. C/S：客户端/服务器端
			在用户本地有一个客户端，在远程有一个服务器端
			如qq，迅雷
			优点: 用户体验好
			缺点: 开发,安装,部署,维护 麻烦
		2. B/S：浏览器/服务器端
			只需要一个浏览器，用户通过不同的网址(URL), 客户访问不同的服务器端程序
			优点:开发,安装,部署,维护 简单
			缺点: 如果应用过大,用户体验会受到影响,对硬件要求过高
			
	* B/S架构
		资源分类:
			1. 静态资源:
				使用静态网页开发技术发布的资源
				所用用户访问得到的结果是一样的
				如: 文本,图片,音频,视频,HTML,CSS,JavaScript
				如果用户请求的是静态资源,那么服务器会直接将静态资源发送给浏览器,
				浏览器中内置了静态资源的解析引擎,可以展示这些资源
			
			2. 动态资源:
				使用动态网页及时发布的资源
				所用用户访问得到的结果可能不一样
				如: jsp/servlet,php,asp...
				如果用户请求的是动态资源,服务器会执行动态资源,转换为静态资源,再发送给浏览器				
因此,要学习动态资源,必须先学习静态资源

<!--more-->

静态资源:
	* HTML: 用于搭建基础网页,展示页面内容
	* CSS: 用于美化页面,布局页面
	* JavaScript: 控制页面的元素,让页面有一些动态效果
	
	
## HTML

1. 概念: 最基础的网页开发语言
	Hyper Text Markup Language 超文本标记语言
		超文本: 超文本是用超链接的方法，将各种不同空间的文字信息组织在一起的网状文本.
		标记语言: 由标签构成的语言. <标签名称> 如 html,xml. 标记语言不是编程语言
		
2. 快速入门:
	语法:
		1. 后缀名必须为html或者htm
		2. 标签分为两类
			1. 围堵标签: 有开始标签和结束标签. 如<html></html>
			2. 自闭和标签: 开始标签和结束标签在一起. 如<br/>			
		3. 标签可以嵌套:
			需要正确嵌套,不能你中有我,我中有你
		4. 在开始标签中可以定义属性,属性是由键值对构成,值需要用引号(单双都可以)引起来
		5. html的标签不区分大小写,建议使用小写

<html>

	<head>
		<title>title</title>
	</head>
	
	<body>
		<font color = 'blue'>Hello World</font><br/>
		<font color = 'red'>Hello World</font>
	</body>
</html>

		
3. 标签
	1. 文件标签: 构成html最基本的标签
		* html: html文档的根标签
		* head: 头标签,用于指定html文档的一些属性,引入外部资源
		* title: 标题标签
		* body: 体标签
	
	2. 文本标签: 和文本有关的标签
		<h1> to <h6> 标题标签，字体加大加粗
		<p> 段落标签
		<br> 换行
		<hr> 线是一条水平线
			属性：
				*color：颜色
				*width：宽度
				*size：粗细
				*align：对齐方式
		<b>：字体加粗
		<i>：字体斜体
		<font>: 字体标签，html5已淘汰
			*color：颜色 
				rgb(值1,值2,值3): 值范围:0-255
				#值1值2值3:值的范围:00-FF之间
			*size：大小
			
			*face：字体
		<center>: 居中标签,已过时
		特殊字符查看特殊字符表
		
	3. 图片标签: 展示图片的标签
		*img: 展示图片
	4. 列表标签:
		有序列表
			*ol
			*li
		无序列表
			*ul
			*li
	5. 链接标签
			*a: 定义一个超链接
				属性:
				*href:指定访问资源的url,也可以访问本地资源
				*target:指定打开资源的方式
				
	6. div和span:默认没有效果,共css设计
		    div: 会换行   块级标签
			span: 文本信息在一行展示,行内标签 内联标签
	7. 语义化标签: html5中为了提高程序可读性,提供了一些标签
		<header>
		<footer>	
	8. 表格标签:
		*table: 定义表格
			width：宽度
			border：边框
			cellpadding：定义内容和单元格距离
			cellspacing：定义单元格之间的距离，如果指定为0，则单元格的线会合为一条
			bgcolor：背景色
			align：对齐方式
		*tr: 定义行
			bgcolor：背景色
			align：对齐方式
		*td: 定义单元格
			colspan: 合并列,占几列
			rowspan: 合并行
		*th: 定义表头单元格
		
		<caption>: 表格标题标签
		<thead>:表示表格头部分,没有效果,增加可读性
		<tbody>:表示表格体部分
		<tfoot>:表示表格脚部分,永远在最下面展示

案例 旅游网首页
早期没有css时,使用table布局
某一行只有一个单元格,就用<tr><td></td></tr>		
有多个单元格则嵌套表格<tr><td><table></table></td></tr>	

	9. 表单标签
		表单:
			概念: 用于采集用户输入的数据
			form
				*action: 指定提交数据的url
				*method: 指定提交方式
					分类: 一个有七种,常见两种
						get:
							1. 请求参数会在地址栏中显示
							2. 请求参数长度有限制
							3. 不太安全
						post:
							1. 请求参数不会在地址栏中显示,会封装在请求体中(HTTP协议后讲解)
							2. 请求参数长度没有限制
							3. 比较安全
			表单项中的数据要想被提交: 必须指定name属性
		
		表单项标签:
			input: 可以通过type属性值,改变元素展示的样式
				type属性:
					text: 文本输入框
						value="" 默认文本,输入时不会自动消失
						placeholder = "请输入用户名" 提示文本,输入时会自动消失
					password: 密码输入框
					radio: 单选框
						注意
						1.要想多个单选框实现单选的效果,则多个单选框的name属性值必须一样
						2. 一般会给每一个单选框提供value属性,指定其被选中后提交的值
						3. checked属性可以指定默认选项
					checkbox: 复选框,每一个选项也要提供value属性
					file: 文件选择框
					hidden: 隐藏域,用于提交一些信息
					submit: 提交按钮,将数据上传到服务器
					button: 就一个按钮
					image: src= "" 图片按钮
			lable:指定输入项的文字描述信息
				注意:
					label的for属性与input的id属性值对应,点击label区域会让input输入框获取焦点
					
			select: 下拉列表
				子元素: option, 指定列表项
			
			textarea: 文本域
				cols: 指定列数,每一行多少字符
				rows: 指定行数
			
			
## html代码
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>你好世界</title>
</head>
<body>
    <!--注释-->
    <!--    br 换行-->
    我有一壶酒，<br>
    可以慰风尘。<br>
    <b>尽倾江湖中，</b><br>
    <i>赠饮天下人。</i><br>

    <br>
    <font color="red" size="5" face = "楷体">恒山飞剑</font>

    <!--    标题标签 h1-h6-->

    <h1>恒山飞剑</h1>
    <h2>恒山飞剑</h2>
    <h3>恒山飞剑</h3>
    <h4>恒山飞剑</h4>
    <h5>恒山飞剑</h5>
    <h6>恒山飞剑</h6>

    <!--    段落标签 p-->
    <p>
    著名武侠小说家，新派武侠小说泰斗，与金庸、梁羽生、温瑞安并称为中国武侠小说四大宗师。代表作有《多情剑客无情剑》、《绝代双骄》、《英雄无泪》等。古龙把武侠小说引入了经典文学的殿堂，将戏剧、推理、诗歌等元素带入传统武侠，又将自己独特的人生哲学融入其中，使中外经典镕铸一炉，开创了近代武侠小说新纪元，将武侠文学推上了一个新的高峰。
    </p>

    <!--显示一条水平线 hr-->
    <hr color = "red" width = "200" size = "5" align="left" />

    <!--    img 展示一张图片-->
    <!-- ./表示当前目录, ../ 表示上一级目录    -->
    <img src="../image/1394699790.jpg" alt="mc" width="900" height="600"/>

    <!-- 有序列表 ol start 从第几开始 -->
    <ol type="a" start="3">
        <li>早上起床干的事情</li>
        <li>睁眼</li>
        <li>看手机</li>
        <li>穿衣服</li>
        <li>洗漱</li>
    </ol>

    <!-- 超链接 a href:跳转页面-->
    <a href="https://www.baidu.com/">点我</a>
    <!-- target默认self,在本页面打开链接, blank则新建空白页面打开,也可以设置点击图片打开-->
    <a href="https://www.baidu.com/" target="_blank"><img src="../image/1.png"></a>


    <!--
    div: 会换行   块级标签
    span: 文本信息在一行展示,行内标签 内联标签
    -->
    <span>东海仙灵</span>
    <span>吐纳法</span>

    <div>东海仙灵</div>
    <div>吐纳法</div>

    <!--    定义表格-->

    <table border="1" width="50%" cellpadding="0" cellspacing="0" bgcolor="#facbd7" align="center">
        <thead>
            <caption>学生信息表</caption>
            <tr>
                <th>编号</th>
                <th>姓名</th>
                <th>成绩</th>
            </tr>
        </thead>
        <tbody>
            <tr>
                <td>1</td>
                <td>小龙女</td>
                <td>100</td>
            </tr>

            <tr>
                <td>2</td>
                <td>杨过</td>
                <td>50</td>
            </tr>
        </tbody>

        <tfoot>
            <tr>
                <td>3</td>
                <td>尹志平</td>
                <td>10</td>
            </tr>
        </tfoot>

    </table>


    <!--
        表单标签
        form: 用于定义表单的,可以定义一个范围,范围代表采集用户数据的范围
        包裹起来的部分才会提交
        -->
<form action="#" method="get">
    <label for="username">用户名:</label> <input type="text" name="username" placeholder="请输入用户名" id="username"><br>
    密码: <input type="password" name="password" placeholder="请输入密码"><br>
    性别: <input type="radio" name="gender" value="male" checked> 男
          <input type="radio" name="gender" value="female"> 女
    <br>

    爱好: <input type="checkbox" name="hobby" value="sing"> 唱
        <input type="checkbox" name="hobby" value="dance"> 跳
        <input type="checkbox" name="hobby" value="rap" checked> Rap
        <input type="checkbox" name="hobby" value="basketball"> 篮球
    <br>

    图片: <input type="file" name="file">
    <br>

    隐藏域: <input type="hidden" name="id" value="aaa">
    <br>

    取色器: <input type="color" name="color"><br>

    生日: <input type="date" name="birthday"><br>

    邮箱: <input type="email" name="email"><br>
    年龄: <input type="number" name="age"><br>
    省份:<select name="province">
            <option value="">--请选择---</option>
            <option value="1">北京</option>
            <option value="2">上海</option>
            <option value="3">江苏</option>
        </select>
    自我描述:
        <textarea cols="20" rows="5"></textarea>

    <br>
    <input type="submit" value="登录">

</form>



    <!--    自定义表单-->

<form action="#" method = "post">
    <table border="1" align="center" width="500">
        <tr>
            <td><label for="user">用户名</label></td>
            <td><input type="text" name="username" id="user"></td>
        </tr>

        <tr>
            <td><label for="password">密码</label></td>
            <td><input type="password" name="password" id="password"></td>
        </tr>

        <tr>
            <td><label for="email">Email</label></td>
            <td><input type="email" name="email" id="email"></td>
        </tr>

        <tr>
            <td><label for="name">姓名</label></td>
            <td><input type="text" name="name" id="name"></td>
        </tr>

        <tr>
            <td><label for="tel">手机号</label></td>
            <td><input type="text" name="tel" id="tel"></td>
        </tr>

        <tr>
            <td>性别</td>
            <td><input type="radio" name="gender" value="male">男
                <input type="radio" name="gender" value="female">女 </td>
        </tr>

        <tr>
            <td><label for="birthday">出生日期</label></td>
            <td><input type="date" name="birthday" id="birthday"></td>
        </tr>

        <tr>
            <td><label for="checkcode">验证码</label></td>
            <td><input type="text" name="checkcode" id="checkcode">
                <img src="img/">
            </td>
        </tr>


    </table>
</form>

</body>
</html>
----------------------------------------------------------------------------------------

