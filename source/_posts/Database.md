---
title: '''Database'''
date: 2020-02-26 13:03:07
tags: 数据库
categories: 数据库
---
## 数据库设计 
三范式：列不可拆分
	   唯一标识
	   引用主键
					
<!--more-->
E-R模型				
关系及存储：
		1对1：一个对象A对应着一个对象B，一个对象B对应着一个对象A，
		关系可以存入A也可以存入B
		1对多：一个对象对应着n个对象B，一个对象B对应着一个对象A，
		关系存入B对象中
		多对多： 一个对象A对应着n个对象B，一个对象B也对应着n个对象A，
		关系存入新建的一个关系表中
		
	
	
-- 查询
-- select * from hero
-- 创建表
CREATE TABLE students3 (
id INT UNSIGNED PRIMARY KEY AUTO_INCREMENT,
NAME VARCHAR (10), age INT UNSIGNED) ；
SELECT 
  * 
FROM
  students3 ；
  
 -- 删除表
 
 DROP TABLE IF EXISTS students2；
 DROP TABLE students；
 
 -- 插入

INSERT INTO students3 VALUES(0,'cc',18)
主键默认写0，

插入多条
INSERT INTO students3 VALUES(0,'cc',18),(0，'tt'，18),(0,'yy',18);

给指定字段插入
INSERT INTO students3(id,name,age) VALUES(0,'cc',18),(0，'tt'，18),(0,'yy',18);

-- 修改

updata students3 set name = 'll',age = 18 where id = 2;

-- 删除

delete from students3 where id = 3;

-- 逻辑删除（还可以恢复）
1. 设计表，给表添加一个字段isdelete，1代表删除，0代表没有删除
2. 给所有数据的isdelete都改为0
3. 要删除某一条数据时，更新isdelete为1
4. 查询数据时，只查询isdelete为0的数据

alter table student add isdelete int


updata student set isdelete where id =1;

-- 显示没有标记删除的数据
select * from student where isdelete=0;
 
-- 查询 *代表所有列
select name,sex,hometown from students; 

-- 查询时显示字段别名 （as 可以省略）
select name as 姓名,sex as 性别,hometown 家乡from students; 

-- 给表起别名（查询多个表时使用）
select s.name,s.sex,s.hometown from students as s;


-- 去重
查询学生性别有几种
select distinct sex from students;

select distinct sex,class from students;

-- 查询小乔的年龄

select age from students where name = '小乔'；

where 后面过滤行，select 后面过滤列

-- 查询20岁以下的学生

select * from students where age < 20;

-- 查询家乡不在北京

select * from students where hometown != '北京'；

--练习
1. 查询学号007的学生的身份证号码
select card from students where studentNo = '007';

2. 查询1班以外的学生信息
select * from students where calss != '1班';

3。 查询年龄大于20的学生的姓名和性别
select name,sex from students where age > 20;




--逻辑运算
and or not 

1. 查询年龄小于20的女同学
select * from students where age < 20 and sex = '女';

2. 查询女学生或一班学生
select * from students where sex = '女' or class = '1班';

-- 查询家乡不在北京

select * from students where not hometown = '北京'；


## 各种查询


-- 模糊查询
like
% 表示任意多个字符
_ 表示一个字符

查询姓田的学生
select * from students where name like '田%';

查询姓田且名字是一个字
select * from students where name like '田_';

查询名字是呈呈的学生
select * from students where name like '%呈呈';

查询名字含庆的学生
select * from students where name like '%庆%';

查询姓名为两个字的学生
select * from students where name like '__';


-- 范围查询
查询家乡是北京上海深圳的学生
select * from students where hometown in ('北京','上海','深圳');

查询年龄在18到20之间的学生
select * from students where age between 18 and 20;

-- 空判断
is null

select * from students where card is null;
与空字符串不同
select * from students where card = ' ';


-- 排序
asc 升序(可省略) desc 降序 
select * from 表名 order by 列1 asc|desc,列2 asc|desc;

select * from students order by age asc;

年龄相同时再按学号从小到大排
select * from students order by age, studentNo；

中文排序需要把utf转成国标 
select * from students order by convert(name using gbk);


-- 聚合函数

-- 查询学生总数
select count(*) from students;
select count(*) from students where class = '1班';

-- 查询女生最大年龄
select max(age) from students where sex = '女';

-- 一班最小年龄
select min(age) from students where class = '一班';

-- sum() 求此列的和
-- 查询北京学生年龄总和
select sum(age) from students where hometown = '北京';

-- 查询女生平均年龄
select avg(age) from students where sex = '女';

-- 取别名
select max(age) 最大年龄, min(age) 最小年龄, avg(age) 平均年龄 from students 



-- 分组

查询各种性别的人数
select sex, count(*) from students group by sex;

查询各种年龄的人数
select age, count(*) from students group by age;

查询各个班级的最大年龄，最小年龄，平均年龄
select class,max(age),min(age),avg(age) from students group by class;

多字段分组
select class,sex,count(*) from students group by class,sex;

分组后再过滤
having 必须用在group by 后面 
select sex, count(*) from students group by sex having sex = '男';

查询一班以外的其他班学生最大年龄。最小年龄，平均年龄
select class, max(age), min(age),avg(age)from students where class != '1班' group by class;


select class, max(age), min(age),avg(age)from students group by class not having class = '1班';



-- 分页， 获取部分行
查询前三行学生信息
0是起始位置，3是获取个数，从0 起始时0可以省略
select * from students limit 0,3; 

每页显示三条，分页
先获取总数N，获取总页数N/3
第L页
select count(*) from students limit 3*(L-1),3

-- 连接查询
把两个表连起来显示

先连接起来再用where过滤，会产生临时表占内存
select * from students,scores where students.studentNo = scores.studentNo;

select * from students as stu,scores as sc where stu.studentNo = sc.studentNo;


-- 内连接
连接时先判断，等于则连接(节约内存)，交集

select * from students inner join scores on students.studentNo = scores.studentNo;

三个表连接
select * from students，courses, scores where students.studentNo = scores.studentNo and scores.courseNo  =courses.courseNo;

select * from students 
inner join scores on students.studentNo = scores.studentNo 
inner join courses on scores.courseNo = course.courseNo


查询西门吹雪的单杀成绩，要求显示姓名，课程号，成绩
select
students.name as 姓名, courses.courseNo as 课程号, scores.score as 成绩
from students
inner join scores on students.studentNo = scores.studentNo
inner join courses on scores.courseNo = courses.courseNo
where students.name = '西门吹雪' and courses.name = '单杀';

查询所有学生成绩，包括没有成绩的学生
left join 左表全显示出来
左连接 join 左边的表称为左表
select * from students 
left join scores on students.studentNo = scores.studentNo

select * from students 
left join scores on students.studentNo = scores.studentNo 
left join courses on scores.courseNo = course.courseNo

right join 右表全显示出来




自关联
存在上下级关系，如地址

编号，区域，上级编号用一个表存储
aid，atitle，pid

查询河南省所有城市

select * from areas,areas_copy where areas.aid = areas_copy.pid and areas.atitle = '河南省';

实际上并不要copy
select * from areas as sheng,areas as shi where sheng.aid = shi.pid and sheng.atitle = '河南省';

查询郑州市下所有区
select * from areas as sheng,areas as shi where sheng.aid = shi.pid and sheng.atitle = '郑州市';

三级查询
查询 河南省下所有区县
select * from areas as sheng,areas as shi, areas as qu where sheng.aid = shi.pid and shi.aid = qu.pid and sheng.atitle = '河南省';


-- 子查询
在一个select里嵌入另一个select

查询大于平均年龄的学生
select avg(age) from students;
select * from students where age > 21.5833;

嵌入
select * from students where age >(select avg(age) from students);

查询最小年龄的人
select * from students where age =(select min(age) from students);

-- 
标量子查询，子查询返回的结果是一个值
查询王昭君的成绩
select studentNo from students where name = '王昭君';
select score from courses where studentNo = '001';

嵌入

select score from courses where studentNo = (select studentNo from students where name = '王昭君');

查询西门吹雪的数据库成绩，要求返回成绩
select score from courses where studentNo = (select studentNo from students where name = '西门吹雪') 
and courseNo = (select courseNo from courses where name = '数据库');

-- 列级子查询
子查询返回的是一列多行

查询十八岁学生的成绩，要求显示成绩
select studentNo from students where age = 18;
select score from scores in ('002','006')

嵌入
select score from scores in (select studentNo from students where age = 18);

-- 行级子查询
子查询返回一行多列

查询男生中年龄最大的学生信息
select * from students where sex = '男' and age = 26;

select * from students where (sex,age) = ('男',26);

select * from students where （sex,age) = (select sex,age from students where sex = '男' order by age desc limit 1);

-- 表级子查询

查询数据库和系统测试的课程成绩
先连接再筛选
select * from scores 
inner join courses on scores.courseNo = courses.courseNo 
where course.name in ('数据库','系统测试');

先筛选再连接
把查询出来的结果当成数据源,一定要取别名

select * from scores 
inner join (select * from courses where name in ('数据库','系统测试')) as c on scores.courseNo = c.courseNo 

any，some
=any 相当于 in
>any 大于其中任意一个都行，即大于最小值
<any 小于其中任意一个都行，即小于最大值
!=any 没有意义

all
!=all 除去这些
>all 大于所有，即大于最大值
<all 小于所有，即小于最小值
=all 没有意义

商品表练习
id name cate brand_name price is_show is_saleoff

1. 求所有产品的平均价格，保留两位小数
select round(avg(price),2) as avg_price from goods;

2. 查询所有价格大于平均价格（保留两位小数）的商品，并且按价格降序排序
select * from goods where price > (select round(avg(price),2) from goods) order by price desc;

3. 查询价格大于等于超极本的商品，并且价格降序
select * from goods where price >= any(select price from goods where cate = '超极本') order by price desc;

-- 数据分表
分出一个类型表，把重复的数据单独存起来
create table good_cates(
id int unsigned primary key auto_increment,
cate_name varchar(10)
);

select distinct cate from goods;

把一个表查询出来的数据插入到另一个表
insert into good_cates(cate_name) select distinct cate from goods;


创建品牌表
create table goods_brands(
id int unsigned primary key auto_increment,
brand_name varchar(10)
) select distinct brand_name from goods;


update goods 
inner join goods_cates on goods.cate = goods_cates.cate_name
set goods.cate = goods_cates.id


## 内置函数

字符串函数 
拼接字符串concat（str1,str2...)

select concat(123,'abc')

长度
select length()
中文长度是3

截取字符串
left(str,len) 从左边截，返回字符串str的左端len个字符
right(str,len) 返回字符串str的右端len个字符
substring(str,pos,len) 返回字符串str的位置pos起len个字符，pos从1开始，不是从0开始

select name, sex,concat(left(name,1),'某某') from students;

去除空格
ltrim(str) 返回删除了左空格的字符串
rtrim(str) 返回删除了右空格的字符串


大小写
select upper()
select lower()

四舍五入
select round(1.6,1) 1表示小数点后几位

当前日期时间
now()

日期格式化 data_format(data,format)
参数format %Y %y %m 等等

-- 流程控制
case语法
select 
case 1
when 1 then 'one'
when 2 then 'two'
else 'zero'

end as result;



select name,sex from students 
case sex
when '男‘ then concat(left(name,1),'先生')
when '女' then concat(left(name,1),'女士')
else concat(left(name,1),'公公')

end as result;


-- 自定义函数
delimiter $$
creat function 函数名称(参数列表) returns 返回类型
begin
sql语句;
end

$$ 
命令行运行时遇到分号会直接运行，delimiter将运行标志改成$$



-- 存储过程
delimiter $$
creat procedure 过程名称()
begin
sql语句;
end

$$ 

call 过程名称;


-- 视图
create view v_stu as 
select * from students
.....

调用时
select * from v_stu
直接调用view中查询的表

-- 事务
开启事务 
begin；
所有操作都成功
.....
commit;

begin；
如果有任意一步失败
rollback；

-- 索引
优化查询速度，但降低了更新效率
创建
create index title_index on test_index(title(10));


建表时创建
create table create_index(
id int primary key,
name varchar(10) unique,
age int;
key(age)
);
只要是主键，建表时自动附带索引
带有unique的自动建索引
手动给字段加索引 key()

表已经存在时添加索引
create index 索引名称 on 表名(字段名称(长度))

-- 外键
根据一个表约束另一个表，但会降低更新效率
alter table 从表名 add foreign key (从表字段) references 主表名(主表字段);

alter table stu add foreign key (class_id) references class(id);
用class的id约束stu的班级号

删除约束
alter table 从表名 drop foreign key 外键名称

-- 修改密码

use mysql;
update user set password=password('新密码') where user = '用户名';

flush privileges;

-- 忘记密码
MySQL\MySQL Sever\my.ini
找到mysqld
添加一行 skip-grant-tables
重启免密登录，修改密码后删除此行