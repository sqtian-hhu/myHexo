---
title: '''JavaNote12_JDBCDateSource'''
date: 2020-04-06 18:43:36
tags: Java基础
categories: Java
---
## 数据库连接池
1. 概念：其实就是一个容器（集合），存放数据库连接的容器
	当系统初始化好后，容器被创建，容器中会申请一些连接对象，当用户来访问数据库时，
	从容器中获取连接对象，用户访问完之后，会将连接对象归还给容器

2. 好处： 1. 节约资源 2. 用户访问高效

<!--more-->

3. 实现：
	1. 标准接口：DataSource javax.sql报包下
		1. 方法:
			获取连接: getConnection()
			归还连接:如果连接对象Connection是从连接池中获取的,那么调用Connection.close()方法不会再关闭连接,而是归还连接
			
		2. 一般我们不去实现它,有数据库厂商来实现
			1. C3P0: 数据库连接池技术
			2. Druid: 数据库连接池技术,由阿里巴巴提供
		
4. C3P0:
步骤1. 导入jar包c3p0-0.9.5.2.jar和mchange-commons-java-0.2.12.jar,同时不要忘记导入数据库驱动jar包
	2. 定义配置文件
		名称必须是 c3p0.properties 或者 c3p0-config.xml
		路径: 直接将文件放在src目录下
		    <property name="driverClass">com.mysql.cj.jdbc.Driver</property>
			<property name="jdbcUrl">jdbc:mysql://localhost:3306/lovelycc?serverTimezone=GMT%2B8</property>
			<property name="user">root</property>
			<property name="password">root</property>
			各参数要根据自己配置改
		
	3. 创建核心对象 数据库连接池对象 ComboPooledDataSource
	4. 获取连接: getConnection
		
public class C3P0Demo1 {
	public static void main(String[] args) throws SQLException {
		//1. 创建数据库连接池对象,不传参数,使用默认配置.
		// 可以添加不同的配置,通过 new ComboPooledDataSource("otherc3p0")创建
		DataSource ds = new ComboPooledDataSource();
		//2. 获取连接对象
		for (int i = 0; i < 11; i++) {
			Connection connection = ds.getConnection();
			System.out.println(i+":"+ connection);
			if (i==5){
				connection.close();
			}
		}
	}
}
----------------------------------------------------------------------------------
5. Druid
步骤1. 导入jar包druid-1.0.9.jar
	2. 定义配置文件:
		1. properties形式
		2. 可以叫任意名称,放在任意目录下
	3. 获取数据库连接池对象: 通过工厂来获取 DruidDataSourceFactory.createDataSource(pro)
	4. 获取连接: ds.getConnection()
public class DruidDemo {
	public static void main(String[] args) throws Exception {
		//1. 导入jar包
		//2. 定义配置文件
		//3. 加载配置文件
		Properties pro = new Properties();
		InputStream is = DruidDemo.class.getClassLoader().getResourceAsStream("druid.properties");
		pro.load(is);

		//4. 获取连接池对象
		DataSource ds = DruidDataSourceFactory.createDataSource(pro);
		//5. 获取连接
		Connection connection = ds.getConnection();
		System.out.println(connection);
	}
}


定义工具类:
	1. 定义一个类 JDBCUtils
	2. 提供静态代码块加载配置文件,初始化连接池对象
	3. 提供方法
		1. 获取连接方法: 通过连接池获取连接
		2. 释放资源
		3. 获取连接池的方法

/*
	Druid连接池的工具类
 */
public class JDBCUtils2 {
	//1. 定义成员变量 DataSource
	private static DataSource ds;
	static{
		try {
			//1. 加载配置文件
			Properties pro = new Properties();
			pro.load(DruidDemo.class.getClassLoader().getResourceAsStream("druid.properties"));
			//2. 获取DataSource
			ds = DruidDataSourceFactory.createDataSource(pro);

		} catch (Exception e) {
			e.printStackTrace();
		}
	}

	/*
		获取连接
	 */
	public static Connection getConnection() throws SQLException {
		return ds.getConnection();
	}

	/*
		释放资源
	 */
	public static void close(ResultSet res, Statement stmt, Connection conn){
		if(res != null){
			try {
				res.close();
			} catch (SQLException e) {
				e.printStackTrace();
			}
		}
		if(stmt != null){
			try {
				stmt.close();
			} catch (SQLException e) {
				e.printStackTrace();
			}
		}
		if(conn != null){
			try {
				conn.close();
			} catch (SQLException e) {
				e.printStackTrace();
			}
		}
	}

	//重载方法
	public static void close(Statement stmt, Connection conn){
		close(null,stmt,conn);
	}

	/*
		获取连接池的方法
	 */

	public static DataSource getDataSource(){
		return ds;
	}
}
--------------------------------------------------------------------------
public class DruidDemo2 {
	public static void main(String[] args) throws SQLException {

	/*
		完成添加操作,给account表添加一条记录
	 */

		Connection connection = null;
		PreparedStatement pstmt = null;
		//1. 获取连接
		 connection = JDBCUtils2.getConnection();
		//2. 定义sql
		String sql = "insert into account values(0,?,?)";
		//3. 获取pstmt对象
		pstmt = connection.prepareStatement(sql);
		//4. 给?赋值
		pstmt.setString(1,"傅红雪");
		pstmt.setDouble(2,1000.99);
		//5. 执行sql
		pstmt.executeUpdate();
	}
}
		
	

## Spring JDBC
	Spring框架对JDBC的简单封装
	提供了一个JDBCTemplate对象简化JDBC的开发
	步骤: 
		1. 导入jar包
		2. 创建JdbcTemplate对象.依赖数据源DataSource
			JdbcTemplate template = new JdbcTemplate(ds);
		3. 调用方法JdbcTemplate的方法来完成CRUD(增删改查)的操作
			update(): 执行DML语句,增删改
			queryForMap(): 查询结果将结果封装为map集合,
				将列名作为key,将值作为value 将记录封装为一个map集合<String,Object>
			queryForList(): 查询结果将结果封装为List集合
				将每一条记录封装为一个Map集合,再将Map集合装载到List集合中
			query(): 查询结果, 将结果封装为JavaBean对象
				query的参数: RowMapper
					一般使用BeanPropertyRowMapper<>(Hero.class)实现类,可以完成数据到javaBean的自动封装
			queryForObject: 查询结果,将结果封装为对象
				一般用于聚合函数的查询
			
/*
	JdbcTemplate入门
 */
public class JdbcTemplateDemo1 {
	public static void main(String[] args) {
		//1. 导入jar包
		//2. 创建JdbcTemplate对象,可以传入一个DataSource对象
		JdbcTemplate template = new JdbcTemplate(JDBCUtils2.getDataSource());
		//3. 调用方法
		String sql = "update account set balance = 5000 where name = ?";
		int count = template.update(sql, "西门吹雪");
		System.out.println(count);
	}
}
------------------------------------------------------------------------------
练习:
	需求: 
		1. 修改李寻欢身高178
		2. 添加一条记录
		3. 删除刚才添加的记录
		4. 查询id为1的记录,将其封装为Map集合
		5. 查询所有的记录,将其封装为List
		6. 查询所有的记录,将其封装为Hero对象的List集合
		7. 查询总记录数

public class JdbcTemplateDemo2 {
	//junit单元测试,可以让方法独立执行

	//1. 获取JdbcTemplate对象
	private JdbcTemplate template = new JdbcTemplate(JDBCUtils2.getDataSource());

	// update执行增删改语句
	@Test
	public void test1(){

		//2. 定义sql
		String sql = "update hero set height = ? where name = ?";
		//3. 执行sql
		int count = template.update(sql,178,"李寻欢");
		System.out.println(count);
	}

	@Test
	public void test2(){
		String sql = "insert into hero values(0,?,?,?,?)";
		template.update(sql,"傅红雪",20,65,175);
	}

	@Test
	public void test3(){
		String sql = "delete from hero where name = ?";
		template.update(sql,"傅红雪");
	}

	//查询query
	//4. 查询id为1的记录,将其封装为Map集合
	@Test
	public void test4(){
		String sql = "select * from hero where id = ?";
		Map<String,Object> map = template.queryForMap(sql,1);
		System.out.println(map);//{ID=1, name=呈呈, age=18, weight=57.0, height=160.0}
	}

	@Test
	//5. 查询所有的记录,将其封装为List
	public void test5(){
		String sql = "select * from hero ";
		List<Map<String,Object>> list = template.queryForList(sql);
		for (Map<String, Object> stringObjectMap : list) {
			System.out.println(stringObjectMap);
		}
	}

	//6. 查询所有的记录,将其封装为Hero对象的List集合
	@Test
	public void test6(){
		String sql = "select * from hero ";

		// 自己实现的RowMapper<Hero>()类
//		List<Hero> list = template.query(sql, new RowMapper<Hero>() {
//			@Override
//			public Hero mapRow(ResultSet resultSet, int i) throws SQLException {
//				Hero hero = new Hero();
//				int id = resultSet.getInt("id");
//				String name = resultSet.getString("name");
//				int age = resultSet.getInt("age");
//				double weight = resultSet.getDouble("weight");
//				double height = resultSet.getDouble("height");
//
//				hero.setId(id);
//				hero.setName(name);
//				hero.setAge(age);
//				hero.setWeight(weight);
//				hero.setHeight(height);
//
//				return hero;
//			}
//		});

		//别人实现好的
		List<Hero> list = template.query(sql, new BeanPropertyRowMapper<>(Hero.class));
		for (Hero hero : list) {
			System.out.println(hero);
		}
	}

	//7. 查询总记录数
	@Test
	public void test7(){
		String sql = "select count(id) from hero ";
		Long total = template.queryForObject(sql, Long.class);
		System.out.println(total);
	}
}
