---
title: '''JavaNote11_JDBC'''
date: 2020-04-06 18:41:11
tags: Java基础
categories: Java
---
## JDBC
1. JDBC基本概念
	Java DataBase Connectivity 
	Java数据库连接，Java语言操作数据库
	JDBC本质：官方定义了一套操作所有关系型数据库的规则(接口),各个厂商去实现这套接口,提供数据库驱动jar包.
			  我们可以使用这套接口编程,真正执行的代码时驱动jar包中的实现类
			  
	期望使用统一的一套Java代码可以操作所有关系型数据库。
	每一个厂商写类自己数据库的JDBC实现类

<!--more-->

2. 快速入门
	步骤:
		1. 导入驱动jar包
			1. 复制jar文件到项目的libs目录下
			2. 右键-->add as Library
			
		2. 注册驱动
		3. 获取数据库连接对象 Connection
		4. 定义sql
		5. 获取执行sql语句的对象 Statement
		6. 执行sql,接受返回结果
		7. 处理结果
		8. 释放资源
		
/**
 * JDBC快速入门
 */
public class JdbcDemo1 {
	public static void main(String[] args) throws Exception {
		//1. 导入驱动jar包 mysql-connector-java-8.0.19.jar
		//2. 注册驱动
		Class.forName("com.mysql.cj.jdbc.Driver");
		//3. 获取数据库连接对象
		Connection connection = DriverManager.getConnection("jdbc:mysql://localhost:3306/lovelycc?serverTimezone=UTC", "root", "root");
		//4. 定义sql语句
		String sql = "update hero set weight = 57 where id = 1";
		//5. 获取执行sql的对象 Statement
		Statement statement = connection.createStatement();
		//6. 执行sql
		int count = statement.executeUpdate(sql);
		//7. 处理结果
		System.out.println(count);
		//8. 释放资源
		statement.close();
		connection.close();
	}
}



3. 详解各个对象
1. DriverManager: 驱动管理对象
	功能:
		1. 注册驱动: 告诉程序该使用哪一个数据库驱动jar
			Class.forName("com.mysql.cj.jdbc.Driver");
			在com.mysql.cj.jdbc.Driver类中存在静态代码块注册驱动
			
			注意: mysql5之后的驱动jar包可以省略注册驱动的步骤
				
		2. 获取数据库连接
			方法: static Connection getConnection(String url, String user, String password)
			参数:
				url: 指定连接的路径
					语法: jdbc:mysql://ip地址(域名):端口号/数据库名称
						  jdbc:mysql://localhost:3306/lovelycc
						  如果连接本机,端口是默认的3306,可以省略成jdbc:mysql:///lovelycc
				user:用户名 root
				password:密码 root
				
2. Connection: 数据库连接对象
	1. 功能:
		1. 获取执行sql的对象
			Statement createStatement()
			PreparedStatement prepareStatement(String sql)
		2. 管理事务:
			开启事务: setAutoCommit(boolean autoCommit): 调用该方法设置参数为false,即开启事务
			提交事务: commit()
			回滚事务: rollback()
			
3. Statement: 执行sql的对象
	用于执行静态SQL语句并返回其生成结果的对象
	1. 执行sql
		1. boolean execute(String sql): 可以执行任意的sql (了解)
		2. int executeUpdate(String sql): 执行DML(insert,update,delete)语句,DDL(create,alter,drop)语句
			返回值: 影响的行数,可以通过这个影响的行数判断DML语句是否执行成功,返回值>0成功,反之失败
		3. ResultSet executeQuery(String sql): 执行DQL(select)语句
	2. 练习:
		1. hero表 添加一条记录

------------------------------------------------------------------------------------------------------------------------------		
/*
	hero表练习insert
 */
public class JdbcDemo2 {
	public static void main(String[] args) {
		Connection connection = null;
		Statement statement = null;
		//1. 注册驱动
		try {
			Class.forName("com.mysql.cj.jdbc.Driver");
			//2. 定义sql
			String sql = "insert into hero values(0,'楚留香',18,60,188)";
			//3. 获取Connection对象
			connection = DriverManager.getConnection("jdbc:mysql://localhost:3306/lovelycc?serverTimezone=GMT%2B8&useUnicode=true&characterEncoding=utf8", "root", "root");
			//4. 获取执行sql的Statement对象
			statement = connection.createStatement();
			//5. 执行sql
			int count = statement.executeUpdate(sql);
			//6. 处理结果,影响的行数
			if(count>0){
				System.out.println("添加成功");
			}else{
				System.out.println("添加失败");
			}
		} catch (ClassNotFoundException e) {
			e.printStackTrace();
		} catch (SQLException e) {
			e.printStackTrace();
		}finally{
			//7. 释放资源
			//避免空指针异常
			if(statement != null){
				try {
					statement.close();
				} catch (SQLException e) {
					e.printStackTrace();
				}
			}

			if(connection != null){
				try {
					connection.close();
				} catch (SQLException e) {
					e.printStackTrace();
				}
			}

		}
	}
}

------------------------------------------------------------------------------------
		2. hero表 修改记录
		
String sql = "update hero set name = '陆小凤' where id = 9";
		
		3. hero表 删除一条记录

String sql = "update hero set name = 'delete from hero where name = 'ww'";

		4. DDL(create,alter,drop)语句

String sql = "create table student (id int , name varchar(20))";
...
//处理结果 DDL语句statement.executeUpdate返回值一直为0
sout(count)
		
		tips: 1. The server time... 时区问题: url后面加上serverTimezone=GMT%2B8
			  2. 中文写进mysql变成???: url后面加上 useUnicode=true&characterEncoding=utf8
			  最终写成: "jdbc:mysql://localhost:3306/lovelycc?serverTimezone=GMT%2B8&useUnicode=true&characterEncoding=utf8"
			  
			  
			  
4. ResultSet: 结果集对象, 封装查询结果
	boolean next(): 游标向下移动一行,判断当前行是否有数据,有数据为true
	getXxx(): 获取Xxx类型的数据
		参数: int: 代表列的编号,从1开始 如: getString(1)
			  String: 代表列名称 如getDouble("weight")
			  
	使用步骤:
	1. 游标向下移动一行
	2. 判断是否有数据
	3. 获取数据

--------------------------------------------------------------------------	
public class JdbcDemo3 {
	public static void main(String[] args) {
		Connection connection = null;
		Statement statement = null;
		ResultSet resultSet = null;
		//1. 注册驱动
		try {
			Class.forName("com.mysql.cj.jdbc.Driver");
			//2. 定义sql
			String sql = "select * from hero ";
			//3. 获取Connection对象
			connection = DriverManager.getConnection("jdbc:mysql://localhost:3306/lovelycc?serverTimezone=GMT%2B8&useUnicode=true&characterEncoding=utf8", "root", "root");
			//4. 获取执行sql的Statement对象
			statement = connection.createStatement();
			//5. 执行sql
			resultSet = statement.executeQuery(sql);
			//6. 处理结果,影响的行数
//			//6.1 游标向下移动一行
//			resultSet.next();
			//6.2 获取数据

			//while循环判断
			while(resultSet.next()){
				int id = resultSet.getInt(1);
				String name = resultSet.getString("name");
				double weight = resultSet.getDouble("weight");
				System.out.println(id+"号选手"+name+"重达"+weight+"KG");
			}

		} catch (ClassNotFoundException e) {
			e.printStackTrace();
		} catch (SQLException e) {
			e.printStackTrace();
		}finally{
			//7. 释放资源
			//避免空指针异常
			if (resultSet != null) {
				try {
					resultSet.close();
				} catch (SQLException e) {
					e.printStackTrace();
				}
			}

			if(statement != null){
				try {
					statement.close();
				} catch (SQLException e) {
					e.printStackTrace();
				}
			}

			if(connection != null){
				try {
					connection.close();
				} catch (SQLException e) {
					e.printStackTrace();
				}
			}
		}
	}
}
----------------------------------------------------------------------------
	练习:
	定义一个方法,查询hero表的数据将其封装为对象,然后装载集合,返回,打印
	1. 定义Hero类
	2. 定义方法 public List<Hero> findAll(){}
	3. 实现方法 select * from hero;
/*
	封装Hero表数据的JavaBean
 */
 
--------------------------------------------------------------------
public class Hero {
	//定义成封装类比较好,可以接受null
	private Integer id;
	private String name;
	private Integer age;
	private Double weight;
	private Double height;

	@Override
	public String toString() {
		return "Hero{" +
			"id=" + id +
			", name='" + name + '\'' +
			", age=" + age +
			", weight=" + weight +
			", height=" + height +
			'}';
	}

	public int getId() {
		return id;
	}

	public void setId(int id) {
		this.id = id;
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public int getAge() {
		return age;
	}

	public void setAge(int age) {
		this.age = age;
	}

	public double getWeight() {
		return weight;
	}

	public void setWeight(double weight) {
		this.weight = weight;
	}

	public double getHeight() {
		return height;
	}

	public void setHeight(double height) {
		this.height = height;
	}
}

-------------------------------------------------------------------------------
/*
	定义一个方法,查询hero表的数据将其封装
 */
public class JdbcDemo4 {
	public static void main(String[] args) {
		List<Hero> list = new JdbcDemo4().findAll();
		System.out.println(list);
		System.out.println(list.size());

	}

	/*
		查询所有H象
	 */
	public List<Hero> findAll(){
		Connection connection = null;
		Statement statement = null;
		ResultSet resultSet = null;
		List<Hero> list = null;

		//1. 注册驱动
		try {
			Class.forName("com.mysql.cj.jdbc.Driver");
			//2. 定义sql
			String sql = "select * from hero ";
			//3. 获取Connection对象
			connection = DriverManager.getConnection("jdbc:mysql://localhost:3306/lovelycc?serverTimezone=GMT%2B8&useUnicode=true&characterEncoding=utf8", "root", "root");
			//4. 获取执行sql的Statement对象
			statement = connection.createStatement();
			//5. 执行sql
			resultSet = statement.executeQuery(sql);
			//6. 遍历结果集,封装对象,装载集合
			list = new ArrayList<Hero>();

			//while循环判断
			while(resultSet.next()){
				int id = resultSet.getInt("ID");
				String name = resultSet.getString("name");
				int age = resultSet.getInt("age");
				double weight = resultSet.getDouble("weight");
				double height = resultSet.getDouble("height");
				//创建Hero对象,并赋值
				Hero hero = new Hero();
				hero.setId(id);
				hero.setName(name);
				hero.setAge(age);
				hero.setWeight(weight);
				hero.setHeight(height);

				//装载集合
				list.add(hero);
			}

		} catch (ClassNotFoundException e) {
			e.printStackTrace();
		} catch (SQLException e) {
			e.printStackTrace();
		}finally{
			//7. 释放资源
			//避免空指针异常
			if (resultSet != null) {
				try {
					resultSet.close();
				} catch (SQLException e) {
					e.printStackTrace();
				}
			}

			if(statement != null){
				try {
					statement.close();
				} catch (SQLException e) {
					e.printStackTrace();
				}
			}

			if(connection != null){
				try {
					connection.close();
				} catch (SQLException e) {
					e.printStackTrace();
				}
			}
		}
		return list;
	}
}
------------------------------------------------------------------

代码重复度太高
抽取JDBC工具类: JDBCUtils
目的: 简化书写
分析:
	1. 抽取注册驱动
	2. 抽取一个方法获取连接对象
		需求：不想传递参数，太麻烦，还得保证工具类的通用性
		解决：配置文件
			jdbc.properties
				url=
				user=
				password=
	3. 抽取一个方法释放资源
	

/*
	JDBC工具类
 */
public class JDBCUtils {
	private static String url;
	private static String user;
	private  static String password;
	private static  String driver;


	/*
		配置文件的读取,只需要读取一次即可拿到这些值.使用静态代码块
	 */
	static{
		try {
			//读取资源文件,获取值.
			//1. 创建Properties集合类
			Properties pro = new Properties();

			//获取src路径下的文件的方式 ---> ClassLoader 类加载器
			//如果路径中存在空格，则会报系统找不到路径错误
			ClassLoader classLoader = JDBCUtils.class.getClassLoader();
			URL res = classLoader.getResource("jdbc.properties");
			String path = res.toURI.getPath();//URL对象转换成字符串前，先调用toURI()方法

			//2. 加载文件
//			pro.load(new FileReader("D:\\IntelliJ IDEA Community Edition 2019.3.1\\JDBCNote\\jdbc\\src\\jdbc.properties"));
				pro.load(new FileReader(path));
			//3. 获取数据
			url = pro.getProperty("url");
			user = pro.getProperty("user");
			password = pro.getProperty("password");
			driver = pro.getProperty("driver");
			//注册驱动
			Class.forName(driver);
		} catch (IOException e) {
			e.printStackTrace();
		} catch (ClassNotFoundException e) {
			e.printStackTrace();
		} catch (URISyntaxException e) {
			e.printStackTrace();
		}
	}


	/*
		获取连接
		返回连接对象
	 */

	public static Connection getConnection() throws SQLException {
		return DriverManager.getConnection(url,user,password);
	}


	public static void close(Statement stmt, Connection conn){
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
	public static void close(ResultSet res,Statement stmt, Connection conn){
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
}
---------------------------------------------------------
public List<Hero> findAll2(){
		Connection connection = null;
		Statement statement = null;
		ResultSet resultSet = null;
		List<Hero> list = null;

		//1. 注册驱动
		try {
			//1. 获取Connection对象
			connection = JDBCUtils.getConnection();
			//2. 定义sql
			String sql = "select * from hero ";
			//4. 获取执行sql的Statement对象
			statement = connection.createStatement();
			//5. 执行sql
			resultSet = statement.executeQuery(sql);
			//6. 遍历结果集,封装对象,装载集合
			list = new ArrayList<Hero>();

			//while循环判断
			while(resultSet.next()){
				int id = resultSet.getInt("ID");
				String name = resultSet.getString("name");
				int age = resultSet.getInt("age");
				double weight = resultSet.getDouble("weight");
				double height = resultSet.getDouble("height");
				//创建Hero对象,并赋值
				Hero hero = new Hero();
				hero.setId(id);
				hero.setName(name);
				hero.setAge(age);
				hero.setWeight(weight);
				hero.setHeight(height);

				//装载集合
				list.add(hero);
			}
		} catch (SQLException e) {
			e.printStackTrace();
		}finally{
			//7. 释放资源
			//避免空指针异常
			JDBCUtils.close(resultSet,statement,connection);
		}
		return list;
	}
	
练习:
	需求:
		1. 通过键盘录入用户名和密码
		2. 判断用户是否登录成功
			SELECT * FROM USER WHERE username = "" and password = "";
			如果sql有查询结果,则成功,反之失败
	步骤:
		1. 创建数据库表 user表
CREATE TABLE USER(
	id INT PRIMARY KEY AUTO_INCREMENT,
	username VARCHAR(32),
	PASSWORD VARCHAR(32)
);
SELECT * FROM USER;
INSERT INTO USER VALUES(0,'西门吹雪','123');
INSERT INTO USER VALUES(0,'陆小凤','456');
SELECT * FROM USER;

		2. 创建登录方法
public class JDBCDemo5 {
	public static void main(String[] args) {
		//1. 键盘录入
		Scanner sc = new Scanner(System.in);
		System.out.println("请输入用户名:");
		String username = sc.nextLine();
		System.out.println("请输入密码:");
		String password = sc.nextLine();

		boolean flag = new JDBCDemo5().login(username, password);
		if (flag){
			System.out.println("登录成功");
		}else{
			System.out.println("登陆失败");
		}

		//2. 调用方法

		//3. 判断结果,输出不同语句
	}

	/*
		登录方法
	 */
	public boolean login(String username,String password){
		Connection connection = null;
		Statement statement = null;
		ResultSet resultSet = null;
		if (username == null || password == null){
			return false;
		}
		//连接数据库判断是否登陆成功
		try {
			connection = JDBCUtils.getConnection();
			//定义sql
			String sql = "select * from user where username = '"+username+"' and password = '"+password+"'";
			statement = connection.createStatement();
			resultSet = statement.executeQuery(sql);
			//判断是否有数据,有则登陆成功
			return resultSet.next();
		} catch (SQLException e) {
			e.printStackTrace();
		}finally{
			JDBCUtils.close(resultSet,statement,connection);
		}
		return false;
	}
}
--------------------------------------------------------------------------------------------------
	
5. PreparedStatement: 执行sql的对象
	1. SQL注入问题: 在拼接sql时,有一些sql的特殊关键字参与字符串拼接,会造成安全性问题
		1. 输入用户名随便,输入密码: a' or 'a' = 'a
		select * from user where username = 'sssss' and password = 'a' or 'a' = 'a' 
		最后恒为true,从而登陆成功
	2. 解决sql注入问题: 使用PreparedStatement对象来解决
	3. 预编译的SQL: 参数使用?作为占位符
	4. 步骤:
		1. 导入驱动jar包
		2. 注册驱动
		3. 获取数据库连接对象 Connection
		4. 定义sql
			注意: sql的参数使用?作为占位符,如 select * from user where username = ? and password = ?;
		5. 获取执行sql语句的对象 PreparedStatement Connection.prepareStatement(String sql)
		6. 给?赋值
			方法: setXxx(参数1,参数2);
				参数1: ?的位置
				参数2: ?的值
		6. 执行sql,接受返回结果,不需要再传递sql语句
		7. 处理结果
		8. 释放资源
	public boolean login(String username,String password){
		Connection connection = null;
		PreparedStatement preparedstatement = null;
		ResultSet resultSet = null;
		if (username == null || password == null){
			return false;
		}
		//连接数据库判断是否登陆成功
		try {
			connection = JDBCUtils.getConnection();
			//定义sql
//			String sql = "select * from user where username = '"+username+"' and password = '"+password+"'";
			String sql = "select * from user where username = ? and password = ?";

			preparedstatement = connection.prepareStatement(sql);
			//给问号赋值
			preparedstatement.setString(1,username);
			preparedstatement.setString(2,password);
			//不需要再传递sql了
			resultSet = preparedstatement.executeQuery();
			//判断是否有数据,有则登陆成功
			return resultSet.next();
		} catch (SQLException e) {
			e.printStackTrace();
		}finally{
			JDBCUtils.close(resultSet,preparedstatement,connection);
		}
		return false;
	}
-------------------------------------------------------------------------------
注意: 后期都会使用PreparedStatement对象来完成增删改查的操作
	1. 可以防止SQL注入
	2. 效率更高
	

## JDBC控制事务
	1. 事务: 一个包含多个步骤的业务操作.如果这个业务操作被事务管理,则这多个步骤要么同时成功,要么同时失败.
	2. 操作:
		开启事务: setAutoCommit(boolean autoCommit): 调用该方法设置参数为false,即开启事务
			在执行sql之前开启事务
		提交事务: commit()
			当所有sql都执行完提交事务
		回滚事务: rollback()
			在catch中回滚事务
	3. 使用Connection对象来管理事务
	
银行转账案例:

CREATE TABLE account(
	id INT PRIMARY KEY AUTO_INCREMENT,
	NAME VARCHAR(32),
	balance DOUBLE
);
INSERT INTO account VALUES(0,'西门吹雪',2000.55);
INSERT INTO account VALUES(0,'陆小凤',3000.77);
SELECT * FROM account;

----------------------------------------------------------------------------------------
/*
	事务操作
 */
public class JDBCDemo6 {
	public static void main(String[] args) {
		Connection connection = null;
		PreparedStatement preparedStatement1 = null;
		PreparedStatement preparedStatement2 = null;
		try {
			connection = JDBCUtils.getConnection();

			//开启事务, 中间出现异常则停止转账,不然钱就不翼而飞了
			connection.setAutoCommit(false);

			//陆小凤-500,西门吹雪+500
			String sql1 = "update account set balance = balance-? where name = ?";
			String sql2 = "update account set balance = balance+? where name = ?";

			preparedStatement1 = connection.prepareStatement(sql1);
			preparedStatement2 = connection.prepareStatement(sql2);
			preparedStatement1.setDouble(1,300);
			preparedStatement1.setString(2,"陆小凤");
			preparedStatement2.setDouble(1,300);
			preparedStatement2.setString(2,"西门吹雪");
			preparedStatement1.executeUpdate();

			//手动制造异常
			int x = 3/0;
			preparedStatement2.executeUpdate();

			//提交事务
			connection.commit();
			//改成Exception,不管出现什么异常都进入回滚,回到事务开启之前的状态
		} catch (Exception e) {
			//事务回滚
			try {
				if(connection!=null){
					connection.rollback();
				}

			} catch (SQLException ex) {
				ex.printStackTrace();
			}
			e.printStackTrace();
		}finally {
			JDBCUtils.close(preparedStatement1,connection);
			JDBCUtils.close(preparedStatement2,null);
		}
	}
}
------------------------------------------------------------------------------
