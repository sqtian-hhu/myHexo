---
title: '''JavaNote_Mybatis_Day02'''
date: 2020-05-09 11:11:41
tags: Java框架
categories: Java
---
## Day2 

mybatis基本使用
mybatis的单表crud操作
mybatis的参数和返回值
mybatis的dao编写
mybatis配置细节

<!--more-->

## CRUD增删改查

1. IUserDao

/**
 *
 * 用户的持久层接口
 */
public interface IUserDao {
	/**
	 * 查询所有操作
	 */

//	@Select("select * from hero")
	List<User> findAll();

	/**
	 * 保存用户
	 * 在映射文件中写入对应sql语句
	 */
	void saveUser(User user);


	/**
	 * 更新用户
	 * 在映射文件中写入对应sql语句
	 */
	void updateUser(User user);

	/**
	 * 删除用户
	 */
	void deleteUser(Integer userID);

	/**
	 * 根据id查询用户
	 * @return
	 */
	User findById(int userID);

	/**
	 * 根据名称模糊查询用户
	 * @return
	 */
	List<User> findByName(String userName);

	/**
	 * 查询总用户数
	 * @return
	 */
	int findTotal();
}
----------------------------------------------------------------------------------------------------------

2. 添加IuserDao.xml中对应方法

<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.sqtian.dao.IUserDao">

    <!--配置查询所有-->
        <select id="findAll" resultType="com.sqtian.domain.User">
            select * from hero;
        </select>

    <!--保存用户 指定传入参数类型 getset方法都是自动生成的,可以用#{}直接获取属性-->
    <insert id="saveUser" parameterType="com.sqtian.domain.User">
        <!--配置插入操作后,获取插入数据的id-->
        <selectKey keyProperty="id" keyColumn="id" resultType="int" order="AFTER">
            select last_insert_id();
        </selectKey>
        insert into hero(name,age,weight,height)values(#{name},#{age},#{weight},#{height});
    </insert>

    <!--更新用户 指定传入参数类型 getset方法都是自动生成的,可以用#{}直接获取属性-->
    <update id="updateUser" parameterType="com.sqtian.domain.User">
        update hero set name=#{name},age=#{age},weight=#{weight},height=#{height} where id=#{id};
    </update>

    <!--删除用户-->
    <delete id="deleteUser" parameterType="java.lang.Integer">
        delete from hero where id = #{userID};
    </delete>

    <!--根据id查询用户-->
    <select id="findById" parameterType="INT" resultType="com.sqtian.domain.User">
        select * from hero where id = #{userID};
    </select>

    <!--根据名称模糊查询用户-->
    <select id="findByName" parameterType="string" resultType="com.sqtian.domain.User">
        select * from hero where name like #{userName};
    </select>

    <!--查询用户总记录条数-->
    <select id="findTotal" resultType="int">
        select count(id) from hero;
    </select>

</mapper>

-----------------------------------------------------------------------------------------------

3. 测试方法
/**
 * 测试mybatisCRUD操作
 * 增删改查
 */

public class CRUDTest {

	private InputStream in;
	private SqlSession sqlSession;
	private IUserDao userDao;

	/**
	 * 测试查询所有
	 */
	@Before //用于在测试方法执行之前执行
	public void init() throws Exception{
		//1.读取配置文件,生成字节输入流
		in = Resources.getResourceAsStream("SqlMapConfig.xml");
		//2.获取SqlSessionFactory
		SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(in);
		//3.获取sqlSession对象
		sqlSession = factory.openSession();
		//4.获取dao代理对象
		userDao = sqlSession.getMapper(IUserDao.class);
	}

	@After //用于在测试方法执行之后执行
	public void destroy() throws IOException {
		//提交事务
		sqlSession.commit();


		//6.关闭资源
		sqlSession.close();
		in.close();

	}


	@Test
	public void testFindAll(){
		//5.执行查询所有的方法
		List<User> users = userDao.findAll();
		for (User user : users) {
			System.out.println(user);
		}


	}


	@Test
	public void testSave(){
		User user = new User();
		user.setName("新建武将");
		user.setAge(18);
		user.setWeight(120);
		user.setHeight(170);

		//执行保存用户
		userDao.saveUser(user);

	}

	@Test
	public void testUpdate(){

		User user = new User();
		user.setId(10);
		user.setName("update");
		user.setAge(18);
		user.setWeight(120);
		user.setHeight(170);

		//执行更新用户
		userDao.updateUser(user);

	}

	@Test
	public void testDelete(){

		userDao.deleteUser(10);
	}

	@Test
	public void testFindById(){
		User user = userDao.findById(1);
		System.out.println(user);
	}

	@Test
	public void testFindByName(){
		List<User> users = userDao.findByName("%update%");
		for (User user : users) {
			System.out.println(user);
		}
	}

	@Test
	public void testFindTotal(){
		int count = userDao.findTotal();
		System.out.println(count);

	}

}
--------------------------------------------------------------------------------------

## 参数深入

OGNL表达式：
	Object Graphic Navigation Language
	对象   图      导航       语言
	
	他是通过对象的取值方法来获取数据。在写法上把get给省略了
	比如：我们获取用户的名称
			类中的写法：user.getUsername();
			OGNL表达式写法：user.username
			
	mybatis中为什么能直接写username,而不用user.呢
		因为在parameterType中已经提供了属性所属的类,所以此时不需要写对象名
		
mybatis 使用OGNL表达式解析对象字段的值,#{}或者${}括号中的值为pojo属性名称
		
注: 各种对象含义
BO：Business Object，业务对象。
	主要是承载业务数据的实体。
	处理业务逻辑的时候使用，数据结构也是针对业务逻辑建立的。
PO：persistence Object，持久化对象。
	数据最终要存储，无论以何种形式存储，都必须要持久化。
	加入使用关系数据库存储，一个PO对应一条数据库的记录，
	或者是对象从数据库查询出来的结果集的一条记录。
DAO：Data Access Object，数据访问对象。
	包含一些数据库的基本操作，CRUD，和数据库打交道。
	负责将PO持久化到数据库，也负责将从数据库查询的结果集映射为PO。
DTO：Data Transfer Object，数据传输对象。
	一般用来在前段和后台的数据传输，数据结构的简历是基于网络传输的，
	减少传输的数据量，避免传输过多无用的数据。
VO：Value Object，值对象。
	主要用在前段数据和控件的绑定操作中，以键值对的形式存在。
	可以从DTO转化而来，这么做的好处就是减少对于DTO的依赖，
	进一步减少对应后端的依赖。还可以增加前段的可测试性，也就是没有DTO，
	也可以对前段逻辑进行自动化的单元测试，可以通过MockDTO来达到测试的目的。
POJO（Plain Old Java Object）简单的Java对象，实际就是普通JavaBeans。
	其中有一些属性及其getter setter方法的类,没有业务逻辑，
	有时可以作为VO(value -object)或dto(Data Transform Object)来使用.
	当然,如果你有一个简单的运算属性也是可以的,
	但不允许有业务方法,也不能携带有connection之类的方法。



-----------------------------------
	/**
	 * 根据queryVo中的条件查询用户
	 *
	 */
	List<User> findUserByVo(QueryVo vo);

-------------------------------------------------
    <!--根据QueryVo的条件查询用户-->
    <select id="findUserByVo" parameterType="com.sqtian.domain.QueryVo" resultType="com.sqtian.domain.User">
        select * from hero where name like #{user.name};
    </select>
----------------------------------------------------------------------------	
package com.sqtian.domain;

public class QueryVo {
	private User user;

	public User getUser() {
		return user;
	}

	public void setUser(User user) {
		this.user = user;
	}
}

--------------------------------------------------------------
	@Test
	public void testFindByVo(){
		//根据queryVo对象的条件来查询
		QueryVo queryVo = new QueryVo();
		User user = new User();
		user.setName("%update%");
		queryVo.setUser(user);
		List<User> users = userDao.findUserByVo(queryVo);
		for (User u : users) {
			System.out.println(u);
		}
	}
	
---------------------------------------------------------------------


## 结果类型封装
	如果封装结果的对象User的属性和数据库中字段不一样
	数据库中是id,name,age,weight,height
	创建User对象时定义的属性是userId,userName,userAge,userWeight,userHeight,如何对应
	
	方法一: 起别名在sql层面解决
	    <!--配置查询所有-->
		<select id="findAll" resultType="com.sqtian.domain.User">
			select id as userId,name as userName,age as userName,weight as userWeight,height as userHeight from hero;
		</select>
		
		执行效率高,但开发效率低

	方法二: 配置查询结果的列名和实体类的属性名对应关系
		<resultMap id="userMap" type="com.sqtian.domain.User">
			<!--主键字段的对应-->
			<id property="属性名userId" column="数据库中对应的列名id"></id>
			<!--非主键字段的对应-->
			<result property="userName" column="username"></result>
			<result property="userAge" column="age"></result>
			<result property="userWeight" column="weight"></result>
			<result property="userHeight" column="height"></result>

		</resultMap>
		
		同时,还需要将<select id="findAll" resultType="com.sqtian.domain.User">
		改为<select id="findAll" resultMap="userMap">
		
		开发效率高






## mybatis 配置文件的一些配置

jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/lovelycc?serverTimezone=UTC
jdbc.username=root
jdbc.password=root
-------------------------------------------------------------------
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<!--mybatis的主配置文件-->
<configuration>
    <!--配置properties
        可以在标签内部配置连接数据库的信息，也可以通过属性引用外部配置文件信息
        将property信息写在resources下的jdbcConfig.properties文件里,
        通过resource属性引用，<properties resource="jdbcConfig.properties"
        url属性: 是按照URL的写法来找properties文件
                URL:Uniform Resource Locator 统一资源定位符,可以唯一标识一个资源的位置
                写法:
                http://localhost:8080/mybatiesserver/demoqServlet
                协议 主机 端口 URI
                URI:Uniform Resource Identifier 统一资源标识符,是在应用中唯一标识一个资源的

        <properties url="file:///D:/IntelliJ IDEA Community Edition 2019.3.1/com.sqtian.mybatis/src/main/resources/jdbcConfig.properties/jdbcConfig.properties">
        >
    -->
    <properties resource="jdbcConfig.properties">
<!--        <property name="driver" value="com.mysql.jdbc.Driver"/>-->
<!--        <property name="url" value="jdbc:mysql://localhost:3306/lovelycc?serverTimezone=UTC"/>-->
<!--        <property name="username" value="root"/>-->
<!--        <property name="password" value="root"/>-->
    </properties>

    <!--配置环境-->
    <environments default="mysql">
        <!--配置mysql环境-->
        <environment id = "mysql">
            <!--配置事务类型 -->
            <transactionManager type="JDBC"></transactionManager>
            <!--配置数据源(连接池)-->
            <dataSource type="POOLED">
                <!--配置连接数据库的4个基本信息
                    ${}引用properties里的内容
                -->
                <property name="driver" value="${jdbc.driver}"/>
                <property name="url" value="${jdbc.url}"/>
                <property name="username" value="${jdbc.username}"/>
                <property name="password" value="${jdbc.password}"/>

            </dataSource>

        </environment>
    </environments>

    <!--指定映射配置文件的位置,映射配置文件指的是每个dao独立的配置文件<mapper resource="com/sqtian/dao/IUserDao.xml"/>
        如果使用注解的方式，来配置，此处应该使用class属性指定被注解的dao全限定类名<mapper class="com.sqtian.dao.IUserDao"/>,不需要com.sqtian.dao.IUserDao.xml-->
    <mappers>
        <mapper resource="com/sqtian/dao/IUserDao.xml"/>
    </mappers>

</configuration>

-----------------------------------------------------------------------------------

可以给路径取别名缩小路径长度
<typeAliases>
	<typeAlias type="实体类全限定类名" alias="指定的别名,不再区分大小写"></typeAlias>


	还可以用package属性,更加方便.用于指定别名,指定之后,该包下的实体类都会注册别名,并且类名就是别名,不再区分大小写
	<package name="com.sqtian.domain"></package>
</typeAliases>

同时mappers标签下的package也可以进行配置<package name="com.sqtian.dao"></package>
