---
title: '''JavaWebNote02_CSS'''
date: 2020-04-27 17:15:25
tags: JavaWeb
categories: Java
---
## CSS
1. 概念：Cascading Style Sheets 层叠样式表

<!--more-->

2. CSS好处
	1. 功能强大
	2. 将内容展示和样式控制分离
		* 降低耦合度,解耦
		* 让分工协作更容易
		* 提高开发效率
	
3. CSS的使用:CSS与html结合方式
	1. 内联样式
	在标签内使用style属性指定css代码,如
	<div style="color:red;">hello css</div>
	并没有把内容和样式分开,因此不推荐
	
	2. 内部样式
	在head标签内,定义style标签,style标签的标签体内容就是css代码
	<head>
		<meta charset="UTF-8">
		<title>Title</title>
		<style>
			div{
				color:blue; //被div包裹的内容就会变成蓝色
			}
		</style>
	
	</head>
	
	<body>
	
	<div>hello css</div>
	</body>
	
	只能设置当前页面被div标记的部分,想把多个页面设置成这样就不适用了
	3. 外部样式
	第一步:定义CSS资源文件
	第二步:在head标签内,定义link标签,引入外部的资源文件
	
	一般都放在一个css的文件夹里,后缀名.css
	
	a.css
	div{
		color:green;
	}
	----------------------------------------------
	
		<head>
		<meta charset="UTF-8">
		<title>Title</title>
		<link rel="stylesheet" href="css/a.css">
	
	</head>
	
	<body>
	
	<div>hello css</div>
	</body>
	----------------------------------------------
	也可以写成
	<style>
		@import"css/a.css";
	</style>
	----------------------------------------------
	
	
4. CSS语法:
	* 格式:
		选择器{
			属性名1:属性值1;
			属性名2:属性值2;
			......
		}
		
	* 选择器:筛选具有相似特征的元素
		如写上div,所有div就会应用样式,没有写div的就不会改变
	
	
5. 选择器:筛选具有相似特征的元素
	* 分类:
		1. 	基本选择器
			1. id选择器:选择具体的id属性值的元素
				语法:#id属性值()
				

			2. 元素选择器:选择具有相同标签名称的元素
				语法:标签名称()
				注意:id选择器优先级高于元素选择器
			3. 类选择器:选择具有相同的class属性值的元素
				语法:.class属性值{}
				注意:优先级高于元素选择器,低于id选择器
			<head>
				<meta charset="UTF-8">
				<title>Title</title>
				<style>
					#div1{
						color:red; //只有id为div1的部分才会改变
					}
					
					div{
						color:green;   //应用于所有div标记的部分,但id选择器优先级高于元素选择器,所以id选择器部分不会被覆盖
					}
					
					.cls1{
						color:blue;
					}
				</style>
	
			</head>
	
			<body>
	
			<div id="div1">hello css</div>
			<div>hello</div>
			
			<p class="cls1">无名剑</p>
			
			</body>
				
		2. 扩展选择器
			1. 选择所有元素:
				*语法: *{}
			2. 并集选择器:
				*语法:选择器1,选择器2{}
				
			3. 子选择器:筛选选择器1元素下的选择器2元素
				*语法:选择器1 选择器2{}
		
			4. 父选择器：筛选选择器2的父选择器1元素
				*语法：选择器1 > 选择器2{}
		
			5. 属性选择器: 选择元素名称, 属性名=属性值的元素
				*语法: 元素名称[属性名='属性值']{}
			
			6. 伪类选择器: 选择一些元素具有的状态
				*语法: 元素:状态{}
				*如: <a>
					*状态:
						link: 初始化的状态
						visited: 被访问过的状态
						active: 正在访问状态
						hover: 鼠标悬浮状态
						
			<head>
				<meta charset="UTF-8">
				<title>扩展选择器</title>
				<style>
					子选择器, 只有在div下的p标签
					div p{
						color:red;
					}
					
					父选择器, 只有p上的div会变
					div > p{
						border: 1px solid;
					}
					
					属性选择器,只有type符合的才会变
					input[type='text']{
						border: 5px solid;
					}
					
					改变超链接各种不同状态的属性
					a:link{
						color:pink
					}
					a:hover{
						color:green
					}
					a:active{
						color:yellow
					}
					a:visited{
						color:red
					}
				</style>
			</head>
			<body>

				<div>
					<p>无名剑主</p>
				</div>

				<p>虎啸飞剑式</p>

				<div>恒山松岳</div>

				<input type = "text">
				<input type = "password">

				<br>  <br>

				<a href="#">洗髓经</a>

			</body>
6. 属性
	1. 字体, 文本
		font-size: 字体大小
		color: 文本颜色
		text-align: 对齐方式
		line-height: 行高
		......
	2. 背景
		background
	3. 边框
		boder: 边框
	4. 尺寸
		width
		height
	5. 盒子模型: 控制布局
		margin: 外边距
		padding: 内边距
			默认情况下内边距会影响整个盒子大小
			需要设置box-sizing: border-box;
			
		float: 浮动
			left
			right
		
<head>
    <meta charset="UTF-8">
    <title>Title</title>

    <style>
        div{
            border: 1px solid red;
            width: 100px
        }

        .div1{
            width: 100px;
            height: 100px;

            /*
            对于内盒,就可以直接设置外边距让其居中
            margin: 50px; */
        }

        .div2{
            width: 200px;
            height: 200px;

            /*
                如果设置外盒内边距,其大小容易改变
                设置合资的属性,让长宽就是最终大小
            */
            padding: 50px;
            box-sizing: border-box;
        }

        .div3{
            float: left;
        }
        .div4{
            float: left;
        }

        .div5{
            float: right;
        }
    </style>

</head>
<body>

    <div class="div2">
        <div class="div1"></div>
    </div>

    <div class="div3">aaaa</div>
    <div class="div4">bbbb</div>
    <div class="div5">cccc</div>

</body>
	
	
## CSS案例 注册页面
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>注册页面</title>

    <style>

        *{
            margin: 0px;
            padding: 0px;
            box-sizing: border-box
        }
        body{
            background: url('../image/background.jpg') no-repeat center;
        }


        .rg_layout{
            width: 900px;
            height: 500px;
            border: 5px solid #EEEEEE;
            background-color: white;
            /* 让div水平居中*/
            margin: auto;
            margin-top: 15px;
        }

        .rg_left{

            float: left;
            margin: 15px;
        }

        .rg_left p:first-child{
            color:#FFD026;
            font-size:20px;
        }

        .rg_left p:last-child{
            color:#A6A6A6;
            font-size:20px;
        }

        .rg_center{

            float: left;

            width: 450px;
        }

        .rg_right{

            float: right;
            margin: 15px;
        }

        .rg_right p{
            font-size:15px;
        }

        .rg_right p a{
            color:pink;
        }

        .td_left{
            width: 100px;
            text-align: right;
            height: 45px;
        }

        .td_right{
            padding-left: 50px;

        }


        #user,#password,#email,#name,#tel,#birthday,#checkcode{
            width: 251px;
            height: 32px;
            border: 1px solid #A6A6A6;
            /*设置边框圆角*/
            border-radius: 5px;
            padding-left: 10px

        }

        #checkcode{width:100px;}

        #img_checked{
            width: 100px;
            vertical-align: middle;
        }

        #btn_sub{
            width: 150px;
            height: 40px;
            background-color: #FFD026;
            border: 1px solid #FFD026;
        }

    </style>



</head>
<body>

    <div class="rg_layout">
        <div class="rg_left">
            <p>新用户注册</p>
            <p>USER REGISTER</p>


        </div>

        <div class="rg_center">
            <div class="rg_form">
                <form action="#" method = "post">
                    <table>
                        <tr>
                            <td class="td_left"><label for="user">用户名</label></td>
                            <td class="td_right"><input type="text" name="username" id="user" placeholder="请输入用户名"></td>
                        </tr>

                        <tr>
                            <td class="td_left"><label for="password">密码</label></td>
                            <td class="td_right"><input type="password" name="password" id="password" placeholder="请输入密码"></td>
                        </tr>

                        <tr>
                            <td class="td_left"><label for="email">Email</label></td>
                            <td class="td_right"><input type="email" name="email" id="email" placeholder="请输入邮箱"></td>
                        </tr>

                        <tr>
                            <td class="td_left"><label for="name">姓名</label></td>
                            <td class="td_right"><input type="text" name="name" id="name" placeholder="请输入姓名"></td>
                        </tr>

                        <tr>
                            <td class="td_left"><label for="tel">手机号</label></td>
                            <td class="td_right"><input type="text" name="tel" id="tel" placeholder="请输入手机号"></td>
                        </tr>

                        <tr>
                            <td class="td_left">性别</td>
                            <td class="td_right"><input type="radio" name="gender" value="male">男
                                <input type="radio" name="gender" value="female">女 </td>
                        </tr>

                        <tr>
                            <td class="td_left"><label for="birthday">出生日期</label></td>
                            <td class="td_right"><input type="date" name="birthday" id="birthday" placeholder="请输入出生日期"></td>
                        </tr>

                        <tr>
                            <td class="td_left"><label for="checkcode">验证码</label></td>
                            <td class="td_right"><input type="text" name="checkcode" id="checkcode" placeholder="请输入验证码">
                                <img id="img_checked" src="img/">
                            </td>
                        </tr>

                        <tr>
                            <td colspan="2" align="center"><input type="submit" value="注册" id="btn_sub"> </td>
                        </tr>

                    </table>
                </form>

            </div>



        </div>



        <div class="rg_right">
            <p>已有账号<a href="#">立即登录</a> </p>
        </div>




    </div>


</body>
</html>