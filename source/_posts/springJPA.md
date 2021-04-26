---
title: springJPA
author: 世庆
top: false
cover: false
toc: true
mathjax: false
date: 2021-04-23 12:29:05
img:
coverImg:
password:
summary:
tags: SpringJPA
categories: Java
---

day1：orm思想和hibernate以及jpa的概述和jpd的基本操作
day2：springdatajpa的运行原理以及基本操作
day3：多表操作，复杂查询

##day1
第一：orm（对象关系映射）思想
	主要目的：操作实体类就相当于操作数据库表
	需要建立两个映射思想：
		1. 建立实体类和表的关系
		2. 建立实体类中属性和表中字段的关系
	不再重点关注：sql语句
	
	实现了ORM思想的框架：mybatis（半自动orm）， hibernate
	tk.mybatis.mapper 插件


第二：hibernate框架介绍
	hibernate是一个开源的对象关系映射框架，它对JDBC进行了非常轻量级的对象封装，
它将pojo与数据库表建立映射关系，是一个全自动的orm框架，可以自动生成SQL语句，自动执行。

第三：JPA规范
	内部由接口和抽象类构成，所有厂商实现jpa规范
	
第四：jpa的基本操作
	增删改查
	搭建环境的过程
		1. 创建maven工程导入坐标
		2. 需要配置jpa的核心配置文件
			配置到类路径下的META-INF的文件夹下
			命名：persistence.xml
		3. 编写客户的实体类
		4. 配置实体类和表，类中属性和表中字段的映射关系
		5. 保存客户到数据库中
	Jpa操作步骤：
		@Test
		public void testSave(){
			//1. 加载配置文件创建工厂（实体管理类工厂）对象
			EntityManagerFactory factory = Persistence.createEntityManagerFactory("myJpa");
			//2. 通过实体管理类工厂获取实体管理类
			EntityManager em = factory.createEntityManager();
			/*
				createEntityManager()方法内部维护了数据库信息，缓存信息，所有的实体管理类对象
				在创建EntityManagerFactory的过程中会根据配置创建数据库表
				EntityManagerFactory的创建过程比较浪费资源
				特点：线程安全的对象
					多个线程访问同一个emfactory不会有线程安全问题
					创建一个公共的emfactory的对象，使用静态代码块 
			*/
			//3. 获取事务对象，开启事务
			EntityTransaction tx = em.getTransaction();
			tx.begin();
			//4. 完成增删改查操作
			User user = new User();
			user.setUsername("tsq");
			user.setPassword("lcc");
			//保存
			em.persist(user);
			//5. 提交事务
			tx.commit();
			//6. 释放资源
			em.close();
			factory.close();
			}
		}


/**
 * 解决实体管理类工厂的浪费和耗时问题
 *  通过静态代码块创建公共实体管理类工厂
 *  
 * 第一次访问getEntityManager方法: 经过静态代码创建一个factory对象,再调用方法创建一个EntityManger对象
 * 第二次访问: 直接通过已经创建好的factory对象创建EntityManager对象
 * 
 */
public class JpaUtils {
    
    private static EntityManagerFactory factory;
    
    static{
        
        factory = Persistence.createEntityManagerFactory("myJpa");
    }
    
    public static EntityManager getEntityManager(){
        return factory.createEntityManager();
    }
    
}

    /**
     * 根据id查询客户
     */
    @Test
    public void testGetReference(){
        //1. 通过工具类获取entityManager
        EntityManager entityManager = JpaUtils.getEntityManager();
        //2. 开启事务
        EntityTransaction tx = entityManager.getTransaction();
        //3. 增删改查
        /**
         * find: 根据id查询数据
         *      class: 查询数据的结果需要包装的实体类类型字节码
         *      id: 查询的主键的取值
         *      1. 获取的对象就是当前客户对象本身
         *      2. 在调用find方法的时候,就会发送sql语句查询数据库
         *      立即加载
         *
         * getReference方法
         *      1. 获取的对象是一个动态代理对象
         *      2. 调用getReference方法不会立即发送sql语句查询数据库
         *          延迟加载,当调用查询结果对象的时候,才会发送查询的sql语句: 什么时候用,什么时候发送sql语句查询数据库
         *
         */
        User user = entityManager.getReference(User.class,1);
        System.out.println(user);
        //4. 提交事务
        tx.commit();
        //5. 释放资源
        entityManager.close();

    }
}


    /**
     * 查询全部
     *  jpql: from com.sqtian.domain.User
     *  sql: select * from user
     */

    @Test
    public void testFindAll(){
        //1. 获取em
        EntityManager em = JpaUtils.getEntityManager();
        //2. 开启事务
        EntityTransaction tx = em.getTransaction();
        tx.begin();
        //3. 查询全部
        String jpql = "from com.sqtian.domain.User";
        Query query = em.createQuery(jpql);//创建Query查询对象,query对象才是执行jqpl的对象

        //发送查询,并封装结果集
        List resultList = query.getResultList();
        for (Object o : resultList) {
            System.out.println(o);
        }
        //4. 提交事务
        tx.commit();
        //5. 释放资源
        em.close();
    }
	
	
添加分页limit查询
        String jpql = "from com.sqtian.domain.User";
        Query query = em.createQuery(jpql);//创建Query查询对象,query对象才是执行jqpl的对象

        //对参数赋值: 分页参数
        //起始索引
        query.setFirstResult(0);
        //每页查询条数
        query.setMaxResults(2);

        //发送查询,并封装结果集
        List resultList = query.getResultList();
        for (Object o : resultList) {
            System.out.println(o);
        }

//对占位符参数赋值		
String jpql = “from User where custName like ？”；
Query query = em.createQuery(jpql);
//占位符的索引位置, 取值
query.setParameter(1,"lcc%");








## day2：springdatajpa的运行原理以及基本操作
第一 springDataJpa的概述
	Spring Data JPA 是Spring 基于ORM框架, JPA规范的基础上封装的一套JPA应用框架
	可以让开发者解脱DAO层的操作,基本上所有的CRUD都可以依赖于它来实现,在实际的工作工程中
	推荐使用Spring Data JPA + ORM(hibernate)完成操作
	
第二 springDataJpa的入门操作
	搭建环境
		创建工程导入坐标
		配置spring的配置文件
		编写实体类(Customer), 使用jpa注解配置映射关系
	编写一个符合springDataJpa的dao层接口
		只需要编写dao层接口，不需要编写dao层接口的实现类
		dao层接口规范
			需要继承两个接口： JpaRepository，JpaSepecificationExecutor
			需要提供相应的泛型
			真正发挥作用的是接口的实现类，在程序执行的过程中，自动帮助我们动态的生成了接口的实现类对象
			动态代理
@RunWith(SpringJUnit4ClassRunner.class)//声明spring提供的单元测试环境
@ContextConfiguration(locations = "classpath:applicationContext.xml")//指定spring容器的配置信息
public class UserDaoTest {
    @Autowired
    private UserDao userDao;

    /**
     * 根据id查询
     */
    @Test
    public void testFindOne(){
        User one = userDao.findOne(1);
        System.out.println(one);
    }

    /**
     * save(user)： 保存或者更新
     *  根据传递对象是否存在主键id
     *  如果没有id主键属性： 保存
     *  存在id主键属性： 根据id查询数据，更新数据
     * delete(id): 根据id删除
     * findAll(): 查询所有
     */

}

第三 springDataJpa的运行过程和原理剖析
	1. 通过JdkDynamicAopProxy的invoke方法创建了一个动态代理对象
	2. SimpleJpaRepository当中封装了JPA的操作（借助JPA的api完成数据库的CRUD）
	3. 通过hibernate完成数据库操作（封装了jdbc）
	
第四 复杂查询
	1. 借助接口中定义的方法
	findOne(id): 根据id查询 立即加载
	getOne(id): 懒加载
	count(): 查询全部客户数量
	exists(id): 查询该id客户是否存在
	
	
	2. 需要将JPQL语句配置到接口方法上
		特有的查询：需要在dao接口上配置方法
		在新添加的方法上，使用@Query注解的形式配置jpql查询语句
	
		对于多个占位符参数,赋值默认情况下占位符的位置需要和方法参数中的位置保持一致
		可以指定占位符参数的位置, ?1 ?2 指定取值来源
		
		
		更新数据
		 /**
		 * 更新操作
		 */
		@Query(value = "update User set username = ?2 where id = ?1")
		@Modifying
		void updateUser(int id,String username);
	
	3. sql语句的查询
		注解:
		@Query(value="", nativeQuery = (false: 使用jpql查询, true: 使用sql查询))

	4. 方法名称规则查询
		是对jpql查询更加深入的一层封装
		无需注解添加语句,只需要按照SpringDataJpa提供的方法名称规则定义方法,不需要再去配置jpql语句,完成查询
		
		findByXXX: 查询
			对象中的属性名,查询条件
			findByUsername
			在springdataJpa运行阶段会根据方法名称进行解析
		findBy + 属性名称 + 查询方式(Like|isnull)
			findByXXXLike: 模糊查询
		findBy + 属性名称 + 查询方式 + 多条件的连接符(and|or) + 属性名称 + 查询方式 
		
		
		
		
		
		
## day3 	
第一：Specifications动态查询
	方法
	T findOne(Specification<T> spec); //查询单个对象
	List<T> findAll(Specification<T> spec); //查询列表
	
	//查询全部, 分页 pageable: 分页参数
	//返回值: 分页pageBean
	Page<T> findAll(Specification<T> spec, Pageable pageable);
	
	//查询列表
	List<T> findAll(Specification<T> spec); //统计查询
	
	Specification: 查询条件
		自定义我们自己的Specification实现类
		实现 
			//root: 查询的根对象(查询的任何属性都可以从根对象中获取)
			//CriteriaQuery: 顶层查询对象, 自定义查询方式(了解: 一般不用)
			//CriteriaBuilder: 查询的构造器,封装了很多的查询条件
			Predicate toPredicate(Root<T> root, CriteriaQuery<?> query, CriteriaBuilder cb); //封装查询条件
			
    @Test
    public void testSpec(){
        /**
         * 自定义查询条件
         *      1. 实现Specification接口（提供泛型： 查询对象类型）
         *      2. 实现toPredication方法（构造查询条件）
         *      3. 需要借助方法参数中的两个参数（
         *          root： 获取需要查询的对象属性
         *          CriteriaBuilder： 构造查询条件， 内部封装了很多查询条件（模糊匹配，精准匹配）
         *
         */
        Specification<User> spec = new Specification<User>() {
            public Predicate toPredicate(Root<User> root, CriteriaQuery<?> criteriaQuery, CriteriaBuilder criteriaBuilder) {
                //1. 获取比较的属性
                Path<Object> username = root.get("username");
                //2. 构造查询条件　select * from user where username = "tsq"
                Predicate predicate = criteriaBuilder.equal(username, "tsq");//进行精准匹配 （比较的属性，比较的属性的取值）
                return predicate;
            }
        };
        User one = userDao.findOne(spec);
        System.out.println(one);
    }



			/**
             * 多条件查询
             *
             */
            Specification<User> spec = new Specification<User>() {
                public Predicate toPredicate(Root<User> root, CriteriaQuery<?> criteriaQuery, CriteriaBuilder criteriaBuilder) {
                    //1. 获取比较的属性
                    Path<Object> username = root.get("username");
                    Path<Object> password = root.get("password");
                    //2. 构造查询条件　select * from user where username = "tsq" and password = "123"
                    Predicate predicate1 = criteriaBuilder.equal(username, "tsq");//进行精准匹配 （比较的属性，比较的属性的取值）
                    Predicate predicate2 = criteriaBuilder.equal(username, "123");
                    //3. 组合多个查询条件　cb.and  cb.or
                    Predicate predicate = criteriaBuilder.and(predicate1, predicate2);

                    return predicate;
                }
            };
			
			/**
			* 模糊查询
			*/
			equal: 直接得到path对象进行比较即可
			gt, ge, le, like: 得到path对象之后,根据path指定比较的参数类型,再去进行比较 path.as(类型的字节码对象)
			
			Predicate predicate = criteriaBuilder.like(username.as(String.class), "ts%");
			
			//排序
			Sort sort = new Sort(Sort.Direction.DESC,"id");
			List<USer> list = userDao.findAll(spec,sort);
			
			//分页查询
			Pageable pageable = new PageRequest(0,2);
			

第二：多表之间的关系和操作多表的操作步骤
	表关系:
		一对一
		一对多
			一的一方: 主表
			多的一方: 从表
			外键: 需要在从表上新建一列作为外键,他的取值来源于主表的主键
			
		多对多
	实体类中的关系:
		包含关系: 可以通过实体类中的包含关系描述表关系
		继承关系
	
	分析步骤:
		1. 明确表关系
		2. 确定表关系 (描述 外键|中间表)
		3. 编写实体类, 在实体类中描述表关系(包含关系)
		4. 配置映射关系

第三：完成多表操作
1. 一对多操作
		一个客户可以有多个联系人
		主表: 客户表
		从表: 联系人表
		在从表上添加外键
		
		
    /**
     * 在联系人类里配置联系人到客户的多对一关系
     *      使用注解的形式配置多对一关系
     *      1. 配置表关系
     *      2. 配置外键
     *
     */

    @ManyToOne(targetEntity = User.class)
    @JoinColumn(name = "lkm_usr_id",referencedColumnName = "id")
    private User user;
		
		
		
    //在客户类里配置客户和联系人之间的关系（一对多关系）
    /**
     * 使用注解形式配置多表关系
     *      1. 声明关系
     *          @OneToMany: 配置一对多的关系
     *              targetEntity： 对方对象的字节码对象
     *
     *      2. 配置外键（中间表）
     *          @JoinColum: 配置外键
     *              name： 外键字段名称
     *              referencedColumnName： 参照的主表的主键字段名称
     * 在客户实体类上（一的一方）添加了外键配置， 所以对于客户而言，也具备了维护外键的作用
     *
     */
    @OneToMany(targetEntity = LinkMan.class)
    @JoinColumn(name = "lkm_usr_id",referencedColumnName = "id")
    private Set<LinkMan> linkManSet = new HashSet<LinkMan>();
		
		
		
	测试方法
    @Test
    @Transactional
    @Rollback(false)
    public void testAdd(){
        User user = new User();
        user.setUsername("tian");
        user.setPassword("tsq");

        LinkMan linkMan = new LinkMan();
        linkMan.setLkmName("lv");
        linkMan.setLkmPhone("1314");


        linkMan.setUser(user);
        user.getLinkManSet().add(linkMan);

        userDao.save(user);
        linkManDao.save(linkMan);

    }
		
	
	//放弃外键维护权	
	mappedBy: 对方配置关系的属性名称
	@OneToMany(mappedBy = "user")
    private Set<LinkMan> linkManSet = new HashSet<LinkMan>();
	
	//级联操作
	在操作主体的实体类上配置cascade属性

	cascade: 配置级联 CascadeType.
	@OneToMany(mappedBy = "user", cascade = CascadeType.ALL)
    private Set<LinkMan> linkManSet = new HashSet<LinkMan>();
	
		
2. 多对多操作
		案例： 用户和角色
			用户: 
			角色：
		分析步骤：
			1. 明确表关系
				多对多
			2. 确定表关系（描述 外键/中间表）
				中间表
			3. 编写实体类，在实体类中描述表关系（包含关系）
				用户：包含角色的集合
				角色：包含用户的集合
			4. 配置映射关系
	
	
		User.java
		//声明关系
		@MangToMany(targetEntity = Role.class) //多对多 targetEntity: 代表对方的实体类字节码
		//配置中间表(包含两个外键)
		@JoinTable(name = "sys_user_role",   //中间表的名字
			//joinColums, 当前对象在中间表中的外键 name 外键名,即中间表中列的名字 referencedColumnName 参照的主表的主键名
			joinColums ={@JoinColum(name="sys_user_id",referencedColumnName = "user_id")},
			//inverseJoinColumns, 对方对象在中间表的外键
			inverseJoinColumns = {@JoinColumn(name = "sys_role_id", referencedColumnName = "role_id")}
		)
		private Set<Role> roles = new HashSet<>();
		
		
		Role.java
		//声明关系
		@MangToMany(targetEntity = User.class) //多对多 targetEntity: 代表对方的实体类字节码
		//配置中间表(包含两个外键)
		@JoinTable(name = "sys_user_role",   //中间表的名字
			//joinColums, 当前对象在中间表中的外键 name 外键名 referencedColumnName 参照的主表的主键名
			joinColums ={@JoinColum(name="sys_role_id",referencedColumnName = "role_id")},
			//inverseJoinColumns, 对方对象在中间表的外键
			inverseJoinColumns = {@JoinColumn(name = "sys_user_id", referencedColumnName = "user_id")}
		)
		private Set<User> users = new HashSet<>();
		
		
		*多对多放弃维护权, 被动的一方放弃维护权
		角色被用户选择, 所以角色放弃维护权
		Role.java
		//声明关系
		@MangToMany(mappedBy = "roles") 
		private Set<User> users = new HashSet<>();
		
		
		
		*级联操作
		只操作User对象,因此在User上配置
		User.java
		//声明关系
		@MangToMany(targetEntity = Role.class, cascade=CascadeType.ALL) //多对多 targetEntity: 代表对方的实体类字节码

3. 多表查询
	1. 对象导航查询
		查询一个对象的同时, 通过此对象查询他的关联对象
		    //测试对象导航查询(查询一个对象的同时, 通过此对象查询他的关联对象)
    @Test
    @Transactional  //解决java代码中 no session问题报错
    public void testQuery1(){
        //查询id为1的用户
        User one = userDao.getOne(11);
        //对象导航查询, 查询此用户下的所有联系人
        Set<LinkMan> linkManSet = one.getLinkManSet();
        for (LinkMan linkMan : linkManSet) {
            System.out.println(linkMan);
        }
    }
	
	对于对象导航查询, 默认使用的延迟加载的形式查询, 调用getLinkManSet方法并不会立即查询, 而是使用关联对象时才会查询
	不想用延迟加载则需要修改配置fetch到@OneToMany()注解中
	//EAGER: 立即加载  LAZY: 延迟加载 
	@OneToMany(fetch = FetchType.EAGER)
	
	//从联系人对象导航查询所属联系人
	
	//多查一, 默认立即加载
    @Test
    @Transactional  //解决java代码中 no session问题报错
    public void testQuery2(){
        //从联系人查询所属用户
        LinkMan linkMan = linkManDao.findOne(1);
        User user = linkMan.getUser();
        System.out.println(user);


    }	
