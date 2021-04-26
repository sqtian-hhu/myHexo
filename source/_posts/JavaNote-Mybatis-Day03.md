---
title: '''JavaNote_Mybatis_Day03'''
date: 2020-05-09 11:11:46
tags: Java框架
categories: Java
---
## day3
mybatis的深入和多表
mybatis连接池
mybatis事务控制及设计方法
mybatis的多表查询

<!--more-->

1. 实际开发中都会使用到连接池，它可以减少我们获取连接所消耗的时间
	连接池就是用于存储连接的一个容器，容器其实就是一个集合对象，待机和必须是线程安全的，不能两个线程拿到统一连接
	该集合还必须实现队列的特性：先进先出

2. mybatis中的连接池
	mybatis连接池提供了3种方式的配置：
		配置的位置：主配置文件SqlMapConfig.xml中的dataSource标签,type属性表示采用何种连接池方式
		type属性:
			POOLED  采用传统的javax.sql.DataSource规范中的连接池,mybatis中有针对规范的实现
			UNPOOLED  采用传统的获取连接方式,虽然也实现Javax.sql.DataSource接口,但是并没有使用池的思想
			JNDI    采用服务器提供的JNDI技术实现,来获取DataSource对象,不同的服务器所能拿到的DataSource是不一样的
					注意:如果不是web或者maven的war工程,是不能使用的
			


3. mybatis中的事务
	什么是事务
	事务的四大特性ACID
	不考虑隔离性会产生的3个问题
	解决方法:四种隔离级别
	
	它是通过sqlsession对象的comit方法和rollback方法实现事务的提交和回滚
	

	sqlSession = factory.openSession(true);//可以自动提交,不用再写sqlSession.commit()
	一个事务中要实现多次交互时,不推荐自动提交,控制不住
	
mybatis中的动态sql语句

	1. if标签

	/**
	 * 根据传入参数条件查询用户
	 * @param user 查询的条件，有可能有用户名，有可能有年龄等等
	 *
	 */
	List<User> findUserByCondition(User user);
	
---------------------------------------------------------------------------
    <!--根据条件查询用户-->
    <select id="findUserByCondition" parameterType="com.sqtian.domain.User" resultType="com.sqtian.domain.User">
        select * from hero where 1=1
        <if test="name != null">
            and name = #{name}
        </if>
        <if test="age != null">
            and age = #{age}
        </if>
    </select>

-----------------------------------------------------
或者
    <!--根据条件查询用户-->
    <select id="findUserByCondition" parameterType="com.sqtian.domain.User" resultType="com.sqtian.domain.User">
        select * from hero 
		<where>
			<if test="name != null">
				and name = #{name}
			</if>
			<if test="age != null">
				and age = #{age}
			</if>
		</where>
    </select>
----------------------------------------------------------------------------------------
	@Test
	public void testFindByCondition(){
		User user = new User();
		user.setName("new");
		user.setAge(18);
		List<User> users = userDao.findUserByCondition(user);
		for (User u : users) {
			System.out.println(u);
		}
	}
------------------------------------------------------------------------------------------

	2. 多个参数

如 select * from hero where id in(1,3,5); 如何实现
利用queryVo 添加	private List<Integer> ids;

	/**
	 * 根据QueryVo中的id集合查询用户
	 *
	 *
	 */
	List<User> findUserInIds(QueryVo vo);
----------------------------------------------------
    <!--根据queryVo中的id集合查询-->
    <select id="findUserInIds" resultType="User" parameterType="QueryVo">
        select * from hero
        <where>
            <if test="ids != null and ids.size()>0">
                <foreach collection="ids" open="and id in(" close=")" item="id" separator=",">
                    #{id}
                </foreach>
            </if>
        </where>

    </select>
-----------------------------------------------------------------------------------------------

	@Test
	public void testFindInIds(){
		//根据queryVo对象的条件来查询
		QueryVo queryVo = new QueryVo();
		List<Integer> list = new ArrayList<Integer>();
		list.add(1);
		list.add(3);
		list.add(5);
		queryVo.setIds(list);
		List<User> users = userDao.findUserInIds(queryVo);
		for (User u : users) {
			System.out.println(u);
		}
	}
-------------------------------------------------------------------------------



4.mybatis中的多表查询
	表之间的关系有几种:
		一对多
		多对一
		一对一
		多对多

	示例: 用户和账户
		一个用户可以有多个账户
		一个账户只能属于一个用户(多个账户也可以属于同一个用户)
		
		
	步骤: 
		1. 建立两个表: 用户表,账户表
			让用户表和账户表之间具备一对多的关系: 需要使用外键在账户表中添加
		2. 建立两个实体类: 用户实体类和账户实体类
			让用户和账户的实体类能体现出来一对多的关系
			
		3. 建立两个配置文件
			用户的配置文件
			账户的配置文件
		4. 实现配置:
			当我们查询用户时,可以同时得到用户下所包含的账户信息
			当我们查询账户时,可以同时得到账户的所属用户信息
			
		select h.*,a.id as aid,a.uid,a.balance from account a,hero h where h.id=a.uid;


## 多对一

public class Account implements Serializable {

	private Integer id;
	private Integer uid;
	private String name;
	private Double balance;

	@Override
	public String toString() {
		return "Account{" +
			"id=" + id +
			", uid=" + uid +
			", name='" + name + '\'' +
			", balance=" + balance +
			'}';
	}

	public Integer getId() {
		return id;
	}

	public void setId(Integer id) {
		this.id = id;
	}

	public Integer getUid() {
		return uid;
	}

	public void setUid(Integer uid) {
		this.uid = uid;
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public Double getBalance() {
		return balance;
	}

	public void setBalance(Double balance) {
		this.balance = balance;
	}
}
---------------------------------------------------------------------------
public class AccountUser extends Account {
	private String name;
	private Integer age;

	@Override
	public String toString() {
		return super.toString()+"     AccountUser{" +
			"name='" + name + '\'' +
			", age=" + age +
			'}';
	}

	@Override
	public String getName() {
		return name;
	}

	@Override
	public void setName(String name) {
		this.name = name;
	}

	public Integer getAge() {
		return age;
	}

	public void setAge(Integer age) {
		this.age = age;
	}


}
-------------------------------------------------------------------------------
public interface IAccountDao {
	/**
	 * 查询所有账户,同时还有获取当前账户的所属用户信息
	 * @return
	 */
	List<Account> findAll();

	/**
	 * 查询所有账户,并且带有用户名称和地址信息
	 * @return
	 */
	List<AccountUser> findAllAccounts();


}
--------------------------------------------------------------------------------
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.sqtian.dao.IAccountDao">

    <!--配置查询所有  com.sqtian.domain.-->
    <select id="findAll" resultType="Account">
        select * from account;
    </select>

    <!--配置查询所有-->
    <select id="findAllAccounts" resultType="AccountUser">
        select a.*,h.name,h.age from account a ,hero h where h.id=a.uid;
    </select>


</mapper>
-------------------------------------------------------------------------------------


实际上常用的方法

从表实体应该包含一个主表实体的对象引用
在封装account时希望同时也将user封装

public class Account

	//从表实体应该包含一个主表实体的对象引用
	private User user;

	public User getUser() {
		return user;
	}

	public void setUser(User user) {
		this.user = user;
	}

----------------------------------------------------------------------
<mapper namespace="com.sqtian.dao.IAccountDao">

    <!--定义封装account和user的resultMap-->
    <resultMap id="accountUserMap" type="account">
        <id property="id" column="aid"></id>
        <result property="uid" column="uid"></result>
        <result property="balance" column="balance"></result>
        <!--一对一的关系映射,配置封装user的内容-->
        <association property="user" column="uid" javaType="com.sqtian.domain.User">
            <id property="id" column="id"></id>
            <result property="name" column="name"></result>
            <result property="age" column="age"></result>
            <result property="weight" column="weight"></result>
            <result property="height" column="height"></result>

        </association>
    </resultMap>

    <!--配置查询所有  resultMap引用映射-->
    <select id="findAll" resultMap="accountUserMap">
<!--        select * from account;-->
        select h.*,a.id as aid,a.uid,a.balance from account a,hero h where h.id=a.uid;
    </select>


</mapper>
--------------------------------------------------------

	@Test
	public void testFindAll(){
		//5.执行查询所有的方法
		List<Account> accounts = accountDao.findAll();
		for (Account account : accounts) {
			System.out.println("每一个账户的信息");
			System.out.println(account);
			System.out.println(account.getUser());
		}


	}
	
----------------------------------------------------------------------------------------



## 一对多查询操作
select * from hero h left outer join account a on h.id=a.uid

---------------------------------------------------------------------
public class User
	//一对多关系映射,主表实体应该包含从表实体的集合引用
	private List<Account> accounts;

	public List<Account> getAccounts() {
		return accounts;
	}

	public void setAccounts(List<Account> accounts) {
		this.accounts = accounts;
	}


--------------------------------------------------------------------
IUserDao

    <resultMap id="userAccountMap" type="user">
        <id property="id" column="id"></id>
        <result property="name" column="name"></result>
        <result property="age" column="age"></result>
        <result property="weight" column="weight"></result>
        <result property="height" column="height"></result>
        <!--配置user对象中accounts集合的映射-->
        <collection property="accounts" ofType="Account">
            <id property="id" column="aid"></id>
            <result property="uid" column="uid"></result>
            <result property="balance" column="balance"></result>

        </collection>

    </resultMap>

    <!--配置查询所有  com.sqtian.domain.-->
    <select id="findAll" resultMap="userAccountMap">
<!--        select * from hero;-->
        select * from hero h left outer join account a on h.id=a.uid
    </select>
	
-----------------------------------------------------------------------------

	@Test
	public void testFindAll(){
		//5.执行查询所有的方法
		List<User> users = userDao.findAll();
		for (User user : users) {
			System.out.println("---每个用户的信息---");
			System.out.println(user);
			System.out.println(user.getAccounts());
		}
	}
	
-------------------------------------------------------------------------------

## 多对多
示例: 用户和角色
	一个用户可以有多个角色
	一个角色可以赋予多个用户


	步骤: 
		1. 建立两个表: 用户表,角色表
			让用户表和角色表之间具备多对多的关系: 需要使用中间表，中间表中包含各自的主键，在中间表中是外键
			
		2. 建立两个实体类: 用户实体类和角色实体类
			让用户和角色的实体类能体现出来多对多的关系
			各自包含对方一个集合的引用
			
		3. 建立两个配置文件
			用户的配置文件
			角色的配置文件
		4. 实现配置:
			当我们查询用户时,可以同时得到用户下所包含的角色信息
			当我们查询账户时,可以同时得到角色的所属用户信息



CREATE TABLE role(
	id INT(11) NOT NULL COMMENT'编号',
	ROLE_NAME VARCHAR(32)DEFAULT NULL COMMENT'角色名称',
	ROLE_DESC VARCHAR(60)DEFAULT NULL COMMENT'角色描述'
);

INSERT INTO role VALUES(1,'无名剑主','划破时代黑暗的破势之剑'),(2,'东灵剑主','飘逸灵巧的睿智之剑'),(3,'月白剑主','优雅高贵的魅力之剑'),(4,'红云剑主','桀骜狂放的狂道之剑'),(5,'紫霄剑主','顺应时势的天道之剑');

SELECT * FROM role;

CREATE TABLE user_role(
	uid INT(11) NOT NULL COMMENT'用户编号',
	rid INT(11) NOT NULL COMMENT'角色编号',
	PRIMARY KEY (uid,rid),
	KEY FK_Reference_10 (rid),
	CONSTRAINT FK_Reference_10 FOREIGN KEY (rid) REFERENCES role(id),
	CONSTRAINT FK_Reference_9 FOREIGN KEY (uid) REFERENCES hero(i`role``user_role``role``hero``user_role``hero`d)
);
INSERT INTO user_role(uid,rid) VALUES (1,5),(2,1),(3,1),(4,2),(5,4),(6,3),(13,4);

SELECT * FROM user_role;

---------------------------------------------------------------

多对多
查询角色的同时获取用户信息
select h.*,r.id as rid,r.role_name,r.role_desc from role r 
left outer join user_role ur on r.id=ur.rid
left outer join hero h on ur.uid=h.id 



	//多对多的关系映射
	private List<User> users;

	public List<User> getUsers() {
		return users;
	}

	public void setUsers(List<User> users) {
		this.users = users;
	}

---------------------------------------------------------------------------
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.sqtian.dao.IRoleDao">
    <resultMap id="roleMap" type="com.sqtian.domain.Role">
        <id property="roleId" column="rid"></id>
        <result property="roleName" column="role_name"></result>
        <result property="roleDesc" column="role_desc"></result>
        <collection property="users" ofType="User">
            <id property="id" column="id"></id>
            <result property="name" column="name"></result>
            <result property="age" column="age"></result>
            <result property="weight" column="weight"></result>
            <result property="height" column="height"></result>
        </collection>
    </resultMap>

    <select id="findAll" resultMap="roleMap">
        select h.*,r.id as rid,r.role_name,r.role_desc from role r
        left outer join user_role ur on r.id=ur.rid
        left outer join hero h on ur.uid=h.id
    </select>
</mapper>

--------------------------------------------------------------------------------
	@Test
	public void testFindAll(){
		//5.执行查询所有的方法
		List<Role> roles = roleDao.findAll();
		for (Role role : roles) {
			System.out.println("每一个角色的信息");
			System.out.println(role);
			System.out.println(role.getUsers());
		}
	}
	
----------------------------------------------------------------------------

多对多
查询用户获取角色信息
select h.*,r.id as rid,r.role_name,r.role_desc from hero h 
left outer join user_role ur on h.id=ur.uid
left outer join role r on ur.rid=r.id 





	//多对多关系映射
	private List<Role> roles;

	public List<Role> getRoles() {
		return roles;
	}

	public void setRoles(List<Role> roles) {
		this.roles = roles;
	}
	
--------------------------------------------------------------------------

        <collection property="roles" ofType="Role">
            <id property="roleId" column="rid"></id>
            <result property="roleName" column="role_name"></result>
            <result property="roleDesc" column="role_desc"></result>
        </collection>
		
------------------------------------------------------------------------

	@Test
	public void testFindAllRoles(){
		//5.执行查询所有的方法
		List<User> users = userDao.findAllRoles();
		for (User user : users) {
			System.out.println("---每个用户的信息---");
			System.out.println(user);
			System.out.println(user.getRoles());
		}
	}

--------------------------------------------------------------------------




## JNDI 数据源


