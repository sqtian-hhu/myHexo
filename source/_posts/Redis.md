---
title: Redis
author: 世庆
top: false
cover: false
toc: true
mathjax: false
date: 2021-04-23 12:24:16
img:
coverImg:
password:
summary:
tags: Redis
categories: 数据库
---


## 今日内容
	1. redis
		1. 概念
		2. 下载安装
		3. 命令操作
			1. 数据结构
		4. 持久化操作
		5. 使用java客户端操作redis


# redis
	1. 概念：redis是一款高性能的NOSQL系列的非关系型数据库
	关系型数据库：mysql、oracle...
		1. 数据之间有关联关系
		2. 数据存储在硬盘的文件上
		
	非关系型数据库：redis、hbase...
		存储key: value
		name: 张三
		age: 23
		
		1. 数据之间没有关联关系
		2. 数据存储在内存中
		
	经常查询一些不太经常变化的数据, 操作关系型数据库非常耗时, 缓存思想解决这个问题
		1. 从缓存中获取数据
			有缓存: 直接返回
			没有数据: 从数据库中查询->放入缓存->返回数据
	redis可用作缓存
	
	2. 下载安装
		1. 官网: https://redis.io
		2. 中文网: https://www.redis.net.cn/
		3. 解压直接可以使用
			redis.windows.conf: 配置文件
			redis-cli.exe: redis的客户端
			redis-server.exe: redis服务器端
			
	3. 命令操作
		1. redis的数据结构:
			redis存储的是: key, value格式的数据, 其中key都是字符串, value有5种不同的数据结构
				value的数据结构
					1) 字符串类型: string
					2) 哈希类型: hash map格式
					3) 列表类型: list linkedlist格式
					4) 集合类型: set
					5) 有序集合类型: sortedset
		
		2. 字符串类型: string
			存储: set key value
			获取: get key
			删除: del key
			
		3. 哈希类型: hash
			存储: hset key field value
			获取: 
				hget key field: 获取指定的field对应的值
				hgetall key: 获取所有的field和value
			删除: hdel key field
		
		4. 列表类型: list
			可以添加一个元素到列表的头部(左边) 或者尾部(右边)
			添加:
				lpush key value: 将元素加入列表左边
				rpush key value: 将元素加入列表右边
			获取:
				lrange key start end: 范围获取
			删除:
				lpop key: 删除列表最左边的元素, 并将元素返回
				rpop key: 删除列表最右边的元素, 并将元素返回
		
		5. 集合类型: set
			不允许重复元素
			存储: 
				sadd key value
			获取: 
				smembers key: 获取set集合中的所有元素
			删除:
				srem key value: 删除set集合中的某个元素
			
		6. 有序集合类型: sortedset
			不允许重复元素, 且元素有序, 赋值时要赋上分数由于从小到大排序
			存储:
				zadd key score value;
			获取:
				zrange key start end;
			删除:
				zrem key value
				
		7. 通用命令
			keys * : 查询所有的键
			type key: 获取键对应的value的类型
			del key: 删除指定的key value
	
		
	4. 持久化操作
		1. redis是一个内存数据库,当redis服务器或者电脑重启, 数据会丢失,
		   我们可以将redis内存中的数据持久化保存到硬盘的文件中
		2. redis持久化机制
			1. RDB: 默认方式
				在一定的间隔时间中, 检测key的变化情况, 然后持久化数据
				编辑redis.windows.conf文件可以修改
				重启redis服务器并指定配置文件名称
			
			2. AOF: 日志记录的方式, 可以记录每一条命令的操作. 每一次命令操作后持久化数据
				编辑redis.windows.conf文件
					appendonly no(关闭aof) appendonly yes(开启aof) 
			
		
		
	5. Java客户端 Jedis
		Jedis: 一款java操作redis数据库的工具		
		jedis操作各种redis中的数据结构
			1) 字符串类型
				get
				set
			2) 哈希类型 
				hset
				hget
				hgetall
			3) 列表类型
				lpush/rpush
				lpop/rpop
			4) 集合类型
				sadd
			5) 有序集合类型
				zadd
		使用步骤:	
			//1. 获取连接
			Jedis jedis = new Jedis("localhost",6379);//如果使用空参构造,默认值就是"localhost",6379
			//2. 操作
				//存储String
			jedis.set("username","张三");
				//获取
			String username = jedis.get("username");
				//使用setex()方法存储可以指定过期时间的key value,可以用来存储激活码
			jedis.setex("activatecode",20,"hehe"); //将activatecode: hehe键值对存入redis,并且20秒后自动删除该键值对
			
				//存储hash
			jedis.hset("user", "name", "lisi");
			jedis.hset("user", "age", "23");
			jedis.hset("user", "gender", "male");
				//获取hash
			String name = jedis.hget("user", "name");
				//获取hash的所有map中的数据
			Map<String,String> user = jedis.hgetAll("user");
				//keyset
			Set<String> keyset = user.keySet();
			for(String key: keySet){
				String value = user.get(key);
				sout(key+ ":" + value);
			}
			
				//存储list
			jedis.lpush("mlist","a","b","c"); //从左边存
			jedis.rpush("mlist","a","b","c"); //从右边存
				//范围获取
			List<String> mylist = jedis.lrange("mylist", 0, -1);
				//弹出
			String element1 = jedis.lpop("mylist"); //c
			String element2 = jedis.rpop("mylist"); //c
			
				//存储 set
			jedis.sadd("myset","java","php","c++"):
				//获取set
			Set<String> myset = jediis.smember("myset");
				
				//存储 sortedset
			jedis.zadd("mysortedset", 3, "剑心");
			jedis.zadd("mysortedset", 5, "李白");
			jedis.zadd("mysortedset", 10, "西门");
				//获取
			Set<String> mysortedset = jedis.zrange("mysortedset", 0, -1);
				
			//3. 关闭连接
			jedis.close();
			
		
		jedis连接池: JedisPool
			使用:
				//0. 创建一个配置对象
				JedisPoolConfig config = new JedisPoolConfig();
				config.setMaxTotal(50);
				config.setMaxIdle(10);
				
				//1. 创建JedisPool连接池对象
				JedisPool jedisPool = new JedisPool(config, "localhost", 6379);
				//2. 调用方法 getResource()方法获取jedis连接
				Jedis jedis = jedisPool.getResource();
				//3. 使用
				jedis.set("hehe", "haha");
				//4. 关闭 归还到连接池中
				jedis.close();
				
		
		
		抽取Jedis连接池工具类
		加载配置文件,配置连接池的参数
		提供获取连接的方法
		public class JedisPoolUtils{
			private static JedisPool jedisPool;
			
			static{
				//读取配置文件
				InputStream is = JedisPoolUtils.class.getClassLoader().getResourceAsStream("jedis.properties");
				//创建Properties对象
				Properties properties = new Properties();
				//关联文件
				try{
					properties.load(is);
				}catch(IOException e){
					e.printStackTrace();
				}
				//获取数据, 设置到JedisPoolConfig中
				JedisPoolConfig config = new JedisPoolConfig();
				config.setMaxTotal(Integer.parseInt(properties.getProperty("maxTotal")));
				config.setMaxIdle(Integer.parseInt(properties.getProperty("maxIdle")));
				//初始化JedisPool
				jedisPool = new JedisPool(config,properties.get("host"),Integer.parseInt(pro.getProperty("port")));
				
			}
			public static Jedis getJedis(){
				return jedisPool.getResource();
			}
		
		
		}
		
		jedis.properties
		
		host=127.0.0.1
		port=6379
		maxTotal=50
		maxIdle=10
		
		
		//通过连接池工具类获取
		 Jedis jedis = JedisPoolUtils.getJedis();
		
		
## 案例
		1. 提供index.html页面,页面中有一个省份 下拉列表
		2. 当页面加载完成后 发送ajax请求,夹在所有省份
		
		
	注意: 使用redis缓存一些不经常发生变化的数据
		  数据库的数据一旦发生改变,则需要更新缓存
			数据库的表执行 增删改的相关操作, 需要将redis缓存数据情况再次存入
			在service对应的增删改方法中,将redis数据删除
		
		
		
		
		