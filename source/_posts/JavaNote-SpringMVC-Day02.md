---
title: '''JavaNote_SpringMVC_Day02'''
date: 2020-07-08 19:00:55
tags: Java框架
categories: Java
---
## Day2


1. 搭建环境


第一章： 响应数据与结果视图

1. 响应的返回值是String类型
发送请求->查出对象->model对象将数据存入request域中->页面显示


    <a href="user/testString">testString</a><br/>
	
    @RequestMapping("/testString")
    public String testString(Model model){
        System.out.println("testString方法执行了");

        //模拟从数据库中查询出user对象
		User user = new User();
        user.setUsername("呈呈");
        user.setAge(18);
        user.setPassword("1201");

        //model对象存数据
        model.addAttribute("user",user);


        return "success";
    }

<!--more-->

2. 响应返回值是void类型
什么不写默认会跳转到pages里的testVoid.jsp

    <a href="user/testVoid">testVoid</a><br/>



    //没有返回值的情况
    //请求转发是一次请求,不用编写项目的名称
    //重定向是两次请求,需要写项目名称
    @RequestMapping("/testVoid")
    public void testVoid(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        System.out.println("testVoid方法执行了");

//        //第一种:手动写转发时系统不会再自动经过视图解析器找到路径,需要自己写路径
//        request.getRequestDispatcher("/WEB-INF/pages/success.jsp").forward(request,response);

//        //第二种:如果要重定向,要加上项目名字.
//        //重定向是重发了一次请求,是不能直接访问web-inf下的页面的
//        response.sendRedirect(request.getContextPath()+"/index.jsp");


        //设置中文乱码
        response.setCharacterEncoding("UTF-8");
        response.setContentType("text/html;charset=UTF-8");
        //第三种:直接进行响应,通过流打印
        response.getWriter().print("你好");

        return;

    }


3. 响应返回值是ModelAndView类型

    <a href="user/testModelAndView">testModelAndView</a><br/>


    @RequestMapping("/testModelAndView")
    public ModelAndView testModelAndView(){
        System.out.println("testModelAndView方法执行了");

        //创建一个ModelAndView对象
        ModelAndView mv = new ModelAndView();

        //模拟从数据库中查询出user对象
        User user = new User();
        user.setUsername("呈呈");
        user.setAge(18);
        user.setPassword("1021");

        //把user对象存储到mv对象中,其底层也会把user对象存入到request对象中
        mv.addObject("user",user);

        //跳转页面
        mv.setViewName("success");

        return mv;
    }
	


4. 使用forward和redirect进行页面跳转
用得较少

    <a href="user/testForwardOrRedirect">testForwardOrRedirect</a><br/>


    //使用关键字的方式进行转发或者重定向

    @RequestMapping("/testForwardOrRedirect")
    public String testForwardOrRedirect(Model model){
        System.out.println("testForwardOrRedirect方法执行了");


//        //请求的转发
//        return "forward:/WEB-INF/pages/success.jsp";

        //重定向,使用关键字的方式可以不用加项目名称,关键字方法自动做好了
        return "redirect:/index.jsp";
    }




5. 响应json数据之过滤静态资源

ResponseBody响应json数据


	1. 在webapp.js下配置jquery.min.js文件

	2. 在response.jsp中引用
	<head>
		<title>response</title>
		<script src="js/jquery.min.js"></script>
	</head>

	3. button事件
	
    <button id="btn">发送ajax的请求</button>
---------------------------------------------------------
	
	4. 设置点击事件
<head>
    <title>response</title>
    <script src="js/jquery.min.js"></script>

    <script>
        //页面加载,绑定单机事件
       $(function(){
          $("#btn").click(function () {
                // alert("hello btn");

              //发送ajax请求
              $.ajax({
                 //编写json格式,设置属性和值
                  url:"user/testAjax",
                  contentType:"application/json;charset=UTF-8",
                  data:'{"username":"呈呈","password":"1201","age":18}',
                  dataType:"json",
                  type:"post",
                  success:function (data) {
                        //data是服务器端响应的json的数据,进行解析
                  }
              });
          });
       });

    </script>
</head>

-----------------------------------------------------------------------------
	5. 在sprinfmvc.xml中设置不要拦截静态资源jquery.min.js
	
    <!--告诉前端配置器,哪些静态资源不拦截-->
    <mvc:resources mapping="/js/**" location="/js/"/>
    <mvc:resources mapping="/images/**" location="/images/"/>
    <mvc:resources mapping="/css/**" location="/css/"/>
	
	6. 编写响应方法testAjax
    //模拟异步请求响应
    @RequestMapping("/testAjax")
    public void testAjax(@RequestBody String body){
        System.out.println("testAjax方法执行了");
        System.out.println(body);
    }
--------------------------------------------------------------------

把客户端发来的json数据封装到user对象
	7. 添加导入坐标
	
    <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-databind</artifactId>
      <version>2.9.0</version>
    </dependency>

    <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-core</artifactId>
      <version>2.9.0</version>
    </dependency>

    <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-annotations</artifactId>
      <version>2.9.0</version>
    </dependency>
	
	8. 编写方法
	
	//模拟异步请求响应
    @RequestMapping("/testAjax")
    //前端发送的是json数据,RequestBody注解将json串转成了user
    //后端返回的是user类型,ResponseBody注解将user转成了json串
    public @ResponseBody User testAjax(@RequestBody User user){
        System.out.println("testAjax方法执行了");
        //客户端发送ajax的请求,传的是json字符串,后端把json字符串封装到user对象中
        System.out.println(user);
        //做响应,模拟查询数据库
        user.setUsername("大呈呈");
        user.setAge(20);
        //响应
        return user;
    }
	
	9. 弹出窗口
	
	                  success:function (data) {
                        //data是服务器端响应的json的数据,进行解析
                      alert(data);
                      alert(data.username);
                      alert(data.password);
                      alert(data.age);
					  
					


## 第二章: SpringMVC实现文件上传

文件上传的必要前提
A form表单的enctype取值必须是: multipart/form-data
						enctype: 是表单请求正文的类型

B method 属性必须是 Post

C 提供一个文件选择域<input type="file/>



1. 传统方式


    <form action="user/fileUpload1" method="post" enctype="multipart/form-data">
        选择文件:<input type="file" name="upload"/><br/>
        <input type="submit" value="上传">
    </form>




-------------------------------------------------------------------------------

2. 添加导入坐标

        <dependency>
            <groupId>commons-fileupload</groupId>
            <artifactId>commons-fileupload</artifactId>
            <version>1.3.1</version>
        </dependency>

        <dependency>
            <groupId>commons-io</groupId>
            <artifactId>commons-io</artifactId>
            <version>2.4</version>
        </dependency>
		
3. 保存

提交表单所有内容都会被封装到request里

    /**
     * 文件上传
     */

    @RequestMapping("/fileUpload1")
    public String fileUpload(HttpServletRequest request) throws Exception {
        System.out.println("传统方式文件上传");

        //使用fileupload组件完成文件上传
        //上传的位置
        String path = request.getSession().getServletContext().getRealPath("/uploads/");
        //判断该路径是否存在
        File file = new File(path);
        if (!file.exists()){
            //创建文件夹
            file.mkdirs();
        }

        //解析request对象,获取上传文件项
        DiskFileItemFactory factory = new DiskFileItemFactory();
        ServletFileUpload upload = new ServletFileUpload(factory);
        //解析request
        List<FileItem> items = upload.parseRequest(request);
        //遍历
        for (FileItem item : items) {
            //进行判断,当前item对象是否是上传文件项
            if (item.isFormField()){
                //说明是普通表单项

            }else {
                //说明是上传文件项
                //获取上传文件的名称
                String fileName = item.getName(); //得到的是全路径C:\Users\HASEE\Desktop\2.jpg,保存有问题

                //切分字符串,以\切分

                String[] strings = fileName.split("\\\\");
                fileName = strings[strings.length-1];

                //把文件名称设置为唯一值,uuid
                String uuid = UUID.randomUUID().toString().replace("-", "");
                fileName = uuid+"_"+fileName;

                //完成文件上传
                item.write(new File(path,fileName));
                //删除临时文件
                item.delete();

            }
        }

        return "success";

    }
	
--------------------------------------------------------------


最终保存在D:\Tomcat\apache-tomcat-8.5.54\webapps\com_sqtian_SpringMVCDay02_02fileUpload_war\uploads


============================================================================================================



## SpringMVC上传文件方式

原理:
上传文件-->request-->前端控制器-->配置文件解析器 解析request-->返回上传文件对象给前端控制器-->把上传文件项传给controller方法

上传方法中的参数名字必须和表单中的一样

配置文件解析器对象的id必须是multipartResolver



1. 添加表单
    <h3>SpringMVC文件上传</h3>

    <form action="user/fileUpload2" method="post" enctype="multipart/form-data">
        选择文件:<input type="file" name="upload"/><br/>
        <input type="submit" value="上传">
    </form>

2. 在springMVC.xml里配置文件解析器
    <!--配置文件解析器-->
    <bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
        <!--设置上传文件大小-->
        <property name="maxUploadSize" value="10485760"/>
    </bean>
	
3. 编写方法
/**
     * SpringMVC文件上传
     */

    @RequestMapping("/fileUpload2")
    //MultipartFile 的名字必须和表单中upload一样
    public String SpringMVCFileUpload(HttpServletRequest request, MultipartFile upload) throws Exception {
        System.out.println("springMVC方式文件上传");

        //使用fileupload组件完成文件上传
        //上传的位置
        String path = request.getSession().getServletContext().getRealPath("/uploads/");
        //判断该路径是否存在
        File file = new File(path);
        if (!file.exists()){
            //创建文件夹
            file.mkdirs();
        }


        //获取上传文件的名称
        String filename = upload.getOriginalFilename();//得到的是全路径C:\Users\HASEE\Desktop\2.jpg,保存有问题

        //把文件名称设置为唯一值,uuid
        String uuid = UUID.randomUUID().toString().replace("-", "");
        filename = uuid+"_"+filename;

        //完成文件上传
        upload.transferTo(new File(path,filename));

        return "success";

    }

-------------------------------------------------------------------------------------


## springmvc跨服务器方式的文件上传

有一个应用服务器springmvc, 一个文件上传服务器fileuploadserver
用户发送了上传文件请求给springmvc服务器,应该把他存到fileupload服务器里

1. 新建一个module,部署在fileupload服务器里

2. 第三种上传表单

    <h3>跨服务器文件上传</h3>

    <form action="user/fileUpload3" method="post" enctype="multipart/form-data">
        选择文件:<input type="file" name="upload"/><br/>
        <input type="submit" value="上传">
    </form>

3. 添加新的坐标
        <dependency>
            <groupId>com.sun.jersey</groupId>
            <artifactId>jersey-core</artifactId>
            <version>1.18.1</version>
        </dependency>

        <dependency>
            <groupId>com.sun.jersey</groupId>
            <artifactId>jersey-client</artifactId>
            <version>1.18.1</version>
        </dependency>

4. 编写方法



    /**
     * SpringMVC文件上传
     */

    @RequestMapping("/fileUpload3")
    //MultipartFile 的名字必须和表单中upload一样
    public String fileUpload3(MultipartFile upload) throws Exception {
        System.out.println("跨服务器文件上传");


        //定义上传文件服务器路径
        String path = "http://localhost:8080/com_sqtian_SpringMVC_fileuploadserver_war/uploads/";

        //获取上传文件的名称
        String filename = upload.getOriginalFilename();//得到的是全路径C:\Users\HASEE\Desktop\2.jpg,保存有问题

        //把文件名称设置为唯一值,uuid
        String uuid = UUID.randomUUID().toString().replace("-", "");
        filename = uuid+"_"+filename;

        //完成文件上传,跨服务器上传
        //创建客户端对象
        Client client = Client.create();
        //和上传服务器进行连接
        WebResource webResource = client.resource(path + filename);
        //上传文件,要字节数组
        webResource.put(upload.getBytes());

        return "success";

    }
	
----------------------------------------------------------------------------



总是报错,发现是tomcat配置文件里拒绝了http的put,要把tomcat/conf/web.xml里的readonly改成false
添加代码
<!-- 使得服务器允许文件写入。-->
		<init-param>
            <param-name>readonly</param-name>
            <param-value>false</param-value>
        </init-param>

----------------------------------------------------------------

另外,要在D:\Tomcat\apache-tomcat-8.5.54\webapps\com_sqtian_SpringMVC_fileuploadserver_war下自己建一个uploads文件夹



## 第三章 SpringMVC的异常处理
1. 异常处理思路
Controller调用service，service调用dao，异常但是向上抛出的，最终由DispatcherServlet找异常处理器进行异常的处理

2. SpringMVC的异常处理

浏览器 --请求--> 前端控制器 --请求--> web --请求--> service --请求--> dao

dao --抛出异常--> service --抛出异常--> web --抛出异常--> 前端控制器异常处理器组件 --> 异常处理器处理异常 --> 跳转到错误提示页面


	1. 编写自定义异常类(做提示信息的)
package com.sqtian.exception;

/**
 *
 * 自定义的一个异常类
 * @author sqtian
 * @create 2020-07-10-10:50
 */
public class SysException extends Exception {

    //存储提示信息
    private String message;

    public SysException(String message) {
        this.message = message;
    }

    public String getMessage() {
        return message;
    }

    public void setMessage(String message) {
        this.message = message;
    }
}
----------------------------------------------------------	
	
	2. 编写异常处理器
public class SysExceptionResolver implements HandlerExceptionResolver {


    /**
     * 处理异常业务逻辑
     * @param httpServletRequest
     * @param httpServletResponse
     * @param o
     * @param e
     * @return
     */
    @Override
    public ModelAndView resolveException(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, Exception e) {
        //获取到异常对象
        SysException se = null;
        if (e instanceof SysException){
            se = (SysException) e;
        }else{
            se = new SysException("系统正在维护");
        }

        //创建ModelAndView对象
        ModelAndView mv = new ModelAndView();
        mv.addObject("errorMsg",e.getMessage());
        mv.setViewName("error");
        return mv;
    }
}
-------------------------------------------------------------------------------------------	
	
	3. 配置一次处理器(跳转到提示页面)

	在springmvc.xml中配置
    <!--配置异常处理器,正常bean对象的配置方法-->
    <bean id="sysExceptionResolver" class="com.sqtian.exception.SysExceptionResolver"></bean>
----------------------------------------------------------------------------------------------------
	error.jsp
	
<%@ page contentType="text/html;charset=UTF-8" language="java" isELIgnored="false" %>
<html>
<head>
    <title>error</title>
</head>
<body>

    ${errorMsg}
</body>
</html>	

-----------------------------------------------------------------------------------------------


    <h3>异常处理</h3>

    <a href="user/testException">异常处理</a>

--------------------------------------------------------------
测试异常的方法

@Controller
@RequestMapping("/user")
public class UserController {

    @RequestMapping("/testException")
    public String testException() throws SysException {
        System.out.println("testException执行了");

        try {
            //模拟异常
            int a = 10/0;
        } catch (Exception e) {

            //打印异常信息
            e.printStackTrace();
            //抛出自定义异常信息
            throw new SysException("查询所有的用户出现了错误...");

        }

        return "success";
    }

}

---------------------------------------------------------------------------------------------------







## 第四章 springMVC拦截器

spring MVC 的处理器拦截器类似于Servlet开发中的过滤器Filter，用于对处理器进行预处理和后处理
用户可以自己定义一些拦截器来实现特定的功能


过滤器是servlet规范中的一部分，任何java web 工程都可以用
拦截器是SpringMVC框架自己的。只有使用了SpringMVC框架的工程才能用

过滤器在url-pattern中配置了/*之后，可以对所有要访问的资源拦截
拦截器只会拦截访问的控制器方法，如果访问的是jsp，html，css，image或js是不会进行拦截的




<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>interceptor</title>
</head>
<body>

    <h3>拦截器</h3>
    <a href="user/testInterceptor">拦截器</a>
</body>
</html>
--------------------------------------------------------------

/**
 * @author sqtian
 * @create 2020-07-10-14:21
 */

@Controller
@RequestMapping("/user")
public class UserController {

    @RequestMapping("/testInterceptor")
    public String testInterceptor(){

        System.out.println("testInterceptor方法执行了");

        return "success";
    }

}
---------------------------------------------------------------





1. 编写拦截器类, 实现HandlerInterceptor接口
package com.sqtian.interceptor;

import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * 自定义拦截器
 * @author sqtian
 * @create 2020-07-10-14:34
 */


public class MyInterceptor1 implements HandlerInterceptor {


    /**
     * 预处理,controller方法执行前
     * @param request
     * @param response
     * @param handler
     * @return true 放行,执行下一个拦截器.如果没有下一个,则执行controller中的方法
     *          false 不放行
     * @throws Exception
     */
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("MyInterceptor1执行了----前1111");

//        return false; 拦截请求,可以手动跳转
//        request.getRequestDispatcher("/WEB-INF/pages/error.jsp").forward(request,response);
        return true;
    }


    /**
     * 后处理方法, controller方法执行后,success.jsp执行之前
     * @param request
     * @param response
     * @param handler
     * @param modelAndView
     * @throws Exception
     */
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("MyInterceptor1执行了----后1111");
    }


    /**
     * success.jsp页面执行后,该方法会执行
     * 释放资源,关闭流
     * @param request
     * @param response
     * @param handler
     * @param ex
     * @throws Exception
     */
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("MyInterceptor1执行了----最后1111");

    }
}
------------------------------------------------------------------------------------------------------------------------------------------------------


2. springmvc.xml中配置拦截器

    <!--配置拦截器-->
    <mvc:interceptors>
        <!--配置第一个-->
        <mvc:interceptor>
            <!--
                mapping是要拦截的方法
                exclude-mapping是不要拦截的方法
                -->
            <mvc:mapping path="/user/*"/>
            <!--配置拦截器对象-->
            <bean class="com.sqtian.interceptor.MyInterceptor1"/>
        </mvc:interceptor>

        <!--配置第二个-->
        <mvc:interceptor>
            <!--
                mapping是要拦截的方法
                exclude-mapping是不要拦截的方法
                -->
            <mvc:mapping path="/**"/>
            <!--配置拦截器对象-->
            <bean class="com.sqtian.interceptor.MyInterceptor2"/>
        </mvc:interceptor>
    </mvc:interceptors>

------------------------------------------------------------------------

执行顺序

MyInterceptor1执行了----前1111
MyInterceptor1执行了----前2222
testInterceptor方法执行了
MyInterceptor1执行了----后2222
MyInterceptor1执行了----后1111
success.jsp执行了
MyInterceptor1执行了----最后2222
MyInterceptor1执行了----最后1111




----------------------------------------------------------------------------