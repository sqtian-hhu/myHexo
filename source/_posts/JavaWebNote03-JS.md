---
title: '''JavaWebNote03_JS'''
date: 2020-05-02 09:20:33
tags: JavaWeb
categories: Java
---
## JavaScript

概念：一门客户端脚本语言
	运行在客户端浏览器中的。每一个浏览器都有JavaScript
	脚本语言：不需要编译，直接就可以被浏览器解析执行
	
<!--more-->	
功能：
	可以来增强用户和html页面的交互过程，可以来控制html元素，
	让页面有一些动态的效果，增强用户的体验

JavaScript = ECMAScript + JavaScript特有的(BOM+DOM)


ECMAScript: 客户端脚本语言的标准
	1. 基本语法
		1. 与html结合方式
			1. 内部JS
				定义<script>标签
			    <script>
					alert("Hello World");
				</script>
			2. 外部JS
				通过src属性引入
			    <script src="../js/a.js"></script>
				
			注意: 
				1. <script>可以定义在html页面的任何地方,但是定义的位置会影响执行顺序
				2. <script>可以定义多个
		2. 注释
			单行: //
			多行: /**/
		3. 数据类型
			1. 原始数据类型(基本数据类型)
				1. number: 数字. 整数/小数/NaN(not a number 一个不是数字的数字类型)
				2. string: 字符串. 字符串 
				3. boolean: true false
				4. null: 一个对象为空的占位符
				5. undefined: 未定义.如果一个变量没有给初始化值,则会被默认赋值为undefined
			2. 引用数据类型:对象
		4. 变量
			变量: 一小块存储数据的内存空间
			Java语言是强类型语言,申请空间时规定了类型,int a = 3;a就只能存放int而JavaScript是一个弱类型语言
			而JavaScript是一个弱类型语言,var a = 3;a还可以存放其他类型的数据
			语法:
				var 变量名 = 初始化值;
				
		5. 运算符
			1. 一元运算符: 只有一个运算数的运算符
				++.-- ...
				
				+(-):正负号,注意:在JS中,如果运算数不是运算符所要求的类型,你们JS引擎会自动进行类型转换
					string转number: 按照字面值转换.如果字面值不是数字,则转为NaN(不是数字的数字类型)
					boolean转number: true转1,false转0
					
			2. 算术运算符:
				+-*/% ...
			3. 赋值运算符:
				=,+=, ...
			4. 比较运算符:
				<> (全等于)===...
				比较方式
				1. 类型相同,直接比较. 字符串按照字典顺序逐一比较
				2. 类型不同,先进行类型转换再比较. 
					===: 在比较之前先判断类型,如果类型不一样则直接返回false
			5. 逻辑运算符:
				&& || !
				其他类型转boolean
					1. number：0和NaN为假，其他为真
					2. string：除了空字符串，其他都是true
					3. null和undefined：false
					4. 所有对象都是true
			6. 三元运算符:
				? :
			
		6. 流程控制语句
			1. if...else...
			2. switch
				在java中,switch语句可以接收的数据类型: byte int shor char 枚举 String
				在js中,switch可以接收任意类型
				
			3. while
			4. do...while
			5. for
			
		
		7. js特殊语法
			变量定义使用var关键字，也可以不使用
			使用：定义变量是局部变量
			不使用：定义变量是全局变量
		
	2. 基本对象
		Function
		
		    <script>

				/*
					Function: 函数方法对象
						1. 创建:
							1. var fun = new Function(形式参数列表,方法体); 不推荐
							2. function 方法名称（形式参数列表）{
									方法体
								}
							3. var 方法名 = function(形式参数列表){
									方法体
								}
						2. 方法:
						3. 属性:
							length: 参数个数

						4. 特点:
							1. 方法定义时，形参的类型不用写
							2. 方法是一个对象,如果定义名称相同的方法,会覆盖
							3. 在js中,方法的调用只与方法的名称有关,和参数列表无关
							4. 在方法声明中隐藏的内置对象(数组),arguments,封装所有的实际参数

						5. 调用:



				*/
				//创建方式1
				//var fun1 = new Function("a","b","alert(a);");
				//fun1(3,4);

				//创建方式2
				function fun2(a,b){
					alert(a+b);
				}
				fun2(3,4);

				//求任意个数的和
				function add(){
					var sum = 0;
					for(var i = 0; i< arguments.length; i++){
						sum += arguments[i];
					}
					return sum;
				}

				var sum = add(1,2,3,4);
				alert(sum);
			</script>
	
	
		
		Array
			创建方式
			var arr1 = new Array(1,2,3);
			var arr2 = new Array(默认长度);
			var arr3 = [1,2,3];
			特点: JS中,数组的类型是可变的
				  JS中,数组的长度可以变化
			属性: length 数组的长度
			方法:
				join(参数): 将数组中的元素按照指定的分隔符拼接为字符串
				push(参数): 向数组末尾添加
		
		Boolean
		Date
		Math
		Number
		String
		RegExp: 正则表达式对象
			1. 正则表达式: 定义字符串的组成规则
				1. 单个字符: []
					如: [a] [ab] [a-zA-Z0-9]
					*特殊符号代表特殊含义的单个字符:
						\d: 单个数字字符 [0-9]
						\w: 单个单词字符 [a-zA-Z0-9]
						
				2. 量词符号:
					?: 表示出现0次或1次
					*: 表示出现0次或多次
					+: 出现1次或多次
					{m,n}: 表示 m<=数量<=n
			2. 创建
				ver reg = new RegExp("正则表达式");
				ver reg = new RegExp("^\\w{6,12}$");//6-12个字符的单词
				ver reg2 = /正则表达式/;
			3. 方法
				test(参数): 验证指定的字符串是否符合正则定义的规范
				
		Global
			1. 特点: 全局对象,这个Global中封装的方法不需要对象就可以直接调用
				方法名()
			
			2. 方法:
				encodeURI(): url编码
				decodeURI(): url解码
				
				encodeURIComponent(): url编码
				decodeURIComponent(): url解码
				
				parseInt(): 将字符串转为数字
					逐一判断每一个字符是否为数字,直到不是数字为止,将前边数字部分转为number
				isNaN(): 判断一个值是否是NaN
					NaN六亲不认,连自己都不认.NaN参与的==比较全是false,只好用isNaN来判断
				
				eval(): 将JavaScript 字符串,并把它作为脚本代码来执行
					
		

## 九九乘法表练习
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>99乘法表</title>

    <style>
        td{
            border: 1px solid;
        }
    </style>

    <script>

        document.write("<table align='center'>");

        //1. 完成基本的for循环嵌套
        for (var i = 1; i <= 9; i++) {
            document.write("<tr>");
            for(var j = 1;j <= i; j++){
            document.write("<td>");
                document.write(i+"*"+j+"="+(i*j)+"&nbsp;&nbsp;&nbsp;");
                document.write("</td>");
            }
            document.write("</tr>");
            //输出换行
            document.write("<br>");
        }

    //完成表格嵌套
    document.write("</table>");
    </script>
</head>
<body>

</body>
</html>





## BOM



## DOM