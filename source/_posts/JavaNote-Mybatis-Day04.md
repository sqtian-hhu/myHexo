---
title: '''JavaNote_Mybatis_Day04'''
date: 2020-05-09 11:11:51
tags: Java框架
categories: Java
---
## day4
mybatis的缓存和注解开发
mybatis中的加载时机(查询的时机)
mybatis中的一级缓存和二级缓存
mybatis的注解开发
单表CRUD
多表查询

<!--more-->

1. mybatis延迟加载
	问题：在一对多中，当我们有一个用户，他有100个账户
		  在查询用户的时候要不要把关联的账户查出来
		  在查询账户的时候要不要把关联的用户查出来
		  
		  在查询用户时，用户下的账户信息应该是什么时候使用什么时候查询
		  在查询账户时，账户的所属用户信息应该是随着账户查询时一起查询出来
		  
什么是延迟加载
	在真正使用数据时才发起查询，不用的时候不查询，按需加载
什么是立即加载
	不管用不用，只要一调用方法马上发起查询
	
在对应的四种表关系里
	一对多，多对多：通常情况下我们都是采用延迟加载
	
	    <collection property="accounts" ofType="account" select="com.sqtian.dao.IAccountDao.findAccountByUid" column="id"></collection>

	
	配置findAccountByUid方法，findAll方法里只写select * from account，延迟开关打开时才会继续查询findAccountByUid
	<select id="findAccountByUid" resultType="account">
        select * from account where uid = #{uid}
    </select>
	
	<!--配置延迟开关的参数-->
    <settings>
        <setting name="lazyLoadingEnabled" value="true"/>
        <setting name="aggressiveLazyLoading" value="false"/>
    </settings>
	
	多对一，一对一：通常情况下我们都是采用立即加载
	


2. Mybatis中的缓存
	什么是缓存：存在于内存中的临时数据
	为什么使用缓存：减少与数据库的交互次数，提高执行效率
	适用于缓存：
		经常查询并且不经常改变
		数据正确与否对最终结果影响不大
	不使用于缓存：
		经常改变的数据
		数据得正确与否对最终结果影响很大
		例如：商品的库存，银行的汇率，股市的牌价
	
	一级缓存
		它指的是Mybatis中SqlSession对象的缓存
		当我们执行查询之后，查询的结果会同时存入到SqlSession为我们提供一块区域中
		该区域的结构是一个Map。当我们再次查询同样的数据，mybatis会先去sqlsession中查询是否有，有的话直接拿出来用
		当SqlSession对象消失时，mybatis的一级缓存也就消失了
	
	二级缓存
		它指的是Mybatis中SqlSessionFactory对象的缓存，由同一个SqlSessionFactory对象创建的SqlSession共享其缓存
		
		使用二级缓存：
			第一步：让Mybatis框架支持二级缓存（在SqlMapConfig.xml中配置）<setting name = "cacheEnabled" value="true"/>
			第二步: 让当前的映射文件支持二级缓存(在IUserDao.xml中配置)<cache/>
			第三步: 让当前的操作支持二级缓存(在select标签中配置)<select useCache = "true"/>
			
			存放在二级缓存的内容是数据,而不是对象.谁要用,就把数据拿来新建一个用户对象



## Mybatis注解开发

注解与IUserDao.xml不能同时使用
jdbcConfig.properties

jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/lovelycc?serverTimezone=UTC&characterEncoding=utf8
jdbc.username=root
jdbc.password=root
---------------------------------------------------------

CRUD注解：不用再去xml里操作

/**
 * 在mybatis中CRUD一共有四个注解
 * @Select @Insert @Update @Delete
 */
public interface IUserDao {

	/**
	 * 查询所有用户
	 * @return
	 */
	@Select("select * from hero")
	List<User> findAll();

	/**
	 * 保存用户
	 */
	@Insert("insert into hero values(#{id},#{name},#{age},#{weight},#{height})")
	void saveUser(User user);

	/**
	 * 更新用户
	 */
	@Update("update hero set name=#{name},age=#{age},weight=#{weight},height=#{height} where id=#{id}")
	void updateUser(User user);

	/**
	 * 删除用户
	 */
	@Delete("delete from hero where id=#{id}")
	void deleteUser(Integer uid);
}
----------------------------------------------------------------------------


public class AnnotationCRUDTest {

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
	public void testSave(){
		User user = new User();
		user.setId(14);
		user.setName("xx");
		user.setAge(18);
		user.setWeight(90);
		user.setHeight(165);
		userDao.saveUser(user);

	}

	@Test
	public void testUpdate(){
		User user = new User();
		user.setId(14);
		user.setName("叶雨时");
		user.setAge(21);
		user.setWeight(126);
		user.setHeight(180);
		userDao.updateUser(user);

	}

	@Test
	public void testDelete(){
		userDao.deleteUser(14);
		List<User> users = userDao.findAll();
		for (User u : users) {
			System.out.println(u);
		}
	}
}


---------------------------------------------------------------------------------

result注解：

	@Select("select * from hero")
	@Results(id = "userMap",value={
		@Result(id=true,column = "id",property = "id"),
		@Result(id=false,column = "name",property = "name"),
		@Result(id=false,column = "age",property = "age"),
		@Result(id=false,column = "weight",property = "weight"),
		@Result(id=false,column = "height",property = "height")
	})
	List<User> findAll();
	
	
	@Insert("insert into hero values(#{id},#{name},#{age},#{weight},#{height})")
	@ResultMap("userMap")
	void saveUser(User user);
	
	
	
一对一：一个账户只有一个用户
	1. 在Account中添加User属性
	2. 在注解中添加映射
	3. 用User的FindById找到User
--------------------------------------------------------------------------	

	//多对一(mybatis中称之为一对一)的映射: 一个账户只能属于一个用户
	private User user;

	public User getUser() {
		return user;
	}

	public void setUser(User user) {
		this.user = user;
	}
----------------------------------------------------------------------------------
	@Select("select * from hero where id=#{id}")
	@ResultMap("userMap")
	User findById(Integer uid);
-------------------------------------------------------------------


public interface IAccountDao  {

	/**
	 * 查询所有账户,并且获取每个账户所属的用户信息
	 * 1对1,使用立即加载
	 */

	@Select("select * from account")
	@Results(id = "accountMap",value = {
		@Result(id=true,column = "id",property = "id"),
		@Result(column = "uid",property = "uid"),
		@Result(column = "name",property = "name"),
		@Result(column = "balance",property = "balance"),
		@Result(column = "uid",property = "user",one=@One(select="com.sqtian.dao.IUserDao.findById",fetchType = FetchType.EAGER))
	})
	List<Account> findAll();

}

-------------------------------------------------------------------------------
	@Test
	public void testFindAll(){
		List<Account> accounts = accountDao.findAll();
		for (Account account : accounts) {
			System.out.println("---每一个账户的信息---");
			System.out.println(account);
			System.out.println(account.getUser());
		}
	}
	
---------------------------------------------------------------------------------

一对多：一个用户可以有多个账户
	1. 在用户里添加accounts属性
	2. IUserDao的注解里添加映射
	3. 利用account的方法findByUid
	
	//1对多关系映射,一个用户对应多个账户
	private List<Account> accounts;

	public List<Account> getAccounts() {
		return accounts;
	}

	public void setAccounts(List<Account> accounts) {
		this.accounts = accounts;
	}
	
-----------------------------------------------------------------------
	/**
	 * 查询所有用户
	 * 一对多,使用延迟加载
	 * @return
	 */
	@Select("select * from hero")
	@Results(id = "userMap",value={
		@Result(id=true,column = "id",property = "id"),
		@Result(id=false,column = "name",property = "name"),
		@Result(id=false,column = "age",property = "age"),
		@Result(id=false,column = "weight",property = "weight"),
		@Result(id=false,column = "height",property = "height"),
		@Result(property = "accounts",column = "id",
			many=@Many(select = "com.sqtian.dao.IAccountDao.findByUid",fetchType = FetchType.LAZY))

	})
	List<User> findAll();
	
-----------------------------------------------------------------------------------
	/**
	 * 根据用户id查询账户信息
	 * @param uid 用户id
	 * @return
	 */
	@Select("select * from account where uid=#{id}")
	List<Account> findByUid(Integer uid);
---------------------------------------------------------------
	@Test
	public void testFindAll(){
		List<User> users = userDao.findAll();
		for (User user : users) {
			System.out.println("---每一个用户信息---");
			System.out.println(user);
			System.out.println(user.getAccounts());
		}

	}
	
----------------------------------------------------------------------


