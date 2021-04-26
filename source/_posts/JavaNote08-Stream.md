---
title: '''JavaNote08_Stream'''
date: 2020-04-02 21:07:43
tags: Java基础
categories: Java
---
## Stream流

/*
	使用传统的方式遍历集合，对集合中的数据进行过滤

 */
 
 <!--more-->
 
public class Demo01List {
	public static void main(String[] args) {
		//创建一个List集合存储姓名
		List<String> list = new ArrayList<>();
		list.add("张无忌");
		list.add("周芷若");
		list.add("赵敏");
		list.add("张强");
		list.add("张三丰");

		//对list集合中的元素进行过滤,只要以张开头的元素,存储到一个新的集合中
		List<String> listA = new ArrayList<>();
		for (String s : list) {
			if(s.startsWith("张")){
				listA.add(s);
			}
		}

		//对ListA过滤,只要姓名长度为三
		List<String> listB = new ArrayList<>();
		for (String s : listA) {
			if(s.length()==3){
				listB.add(s);
			}
		}

		for (String s : listB) {
			System.out.println(s);
		}
	}
}
--------------------------------------------------------------------------------------
/*
	使用Stream流的方式,遍历集合,对集合中的数据进行过滤
	关注的是做什么而不是怎么做
 */
public class Demo02Stream {
	public static void main(String[] args) {
		List<String> list = new ArrayList<>();
		list.add("张无忌");
		list.add("周芷若");
		list.add("赵敏");
		list.add("张强");
		list.add("张三丰");

		list.stream()
			.filter(name->name.startsWith("张"))
			.filter(name->name.length()==3)
			.forEach(name-> System.out.println(name));
		
	}
}

整体来看，流式思想类似于工厂车间的生产流水线
Stream流是一个来自数据源的元素队列
元素是特定类型的对象，形成一个队列
数据源：流的来源，可以是集合，数组等
pipelining：之间操作都会返回流对象本身。这样多个操作可以串联成一个管道，对操作进行优化
内部迭代：流可以直接调用遍历方法

1.3 获取流 
所以Collection集合都可以通过stream默认方法获取流
Stream接口的静态方法of可以获取数组对应的流
public class Demo02GetStream {
	public static void main(String[] args) {
		//把集合转换为Stream流
		List<String> list = new ArrayList<>();
		Stream<String> stream1 = list.stream();

		Set<String> set = new HashSet<>();
		Stream<String> stream2 = set.stream();
		
		Map<String,String> map = new HashMap<>();
		//获取键,存储到一个Set集合中
		Set<String> keySet = map.keySet();
		Stream<String> stream3 = keySet.stream();
		//获取值,存储到一个Collection集合
		Collection<String> values = map.values();
		Stream<String> stream4 = values.stream();
		//获取键值对,entrySet
		Set<Map.Entry<String,String>> entries = map.entrySet();
		Stream<Map.Entry<String,String>> stream5 = entries.stream();
		
		//把数组转换为Stream流
		Stream<Integer> stream6 = Stream.of(1,2,3,4,5);
		//可变参数可以传递数组
		Integer[] arr = {1,2,3,4,5};
		Stream<Integer> stream7 = Stream.of(arr);
	}
}
--------------------------------------------------------------------------
1.4 常用方法
延迟方法：返回值类型仍然是Stream接口自身类型的方法，因此支持链式调用。除了终结方法，其余方法均为延迟方法
终结方法：返回值类型不再是Stream接口自身类型的方法，不再支持链式调用。常用有count，forEach

逐一处理：forEach
/*
	forEach方法用来遍历流中的数据
	是一个终结方法,遍历之后就不能再调用Stream流中的其他方法
	该方法接收一个Consumer接口函数,会将每一个流元素交给该函数进行处理,Consumer接口可传递Lambda表达式

 */
public class Demo02Stream_forEach {
	public static void main(String[] args) {
		//获取一个Stream流
		Stream<String> stream = Stream.of("张三", "李四", "王五", "赵六", "田七");
//		//使用forEach遍历
//		stream.forEach((String name) -> {
//			System.out.println(name);
//		});
		//化简
		stream.forEach(name ->System.out.println(name));
	}
}
---------------------------------------------------------------------------------------

过滤：filter
/*
	过滤:filter,接收Predicate接口
 */
public class Demo03Stream_filter {
	public static void main(String[] args) {
		Stream<String> stream = Stream.of("张三丰","张无忌","西门吹雪","周芷若","赵敏");
		//对Stream流中的元素进行过滤,只要姓张的
//		Stream<String> stream2 = stream.filter((String name)->{
//			return name.startsWith("张");
//		});
		//简化
//		Stream<String> stream2 = stream.filter(name-> name.startsWith("张"));
//		stream2.forEach(name-> System.out.println(name));
		//链式
		stream.filter(name-> name.startsWith("张"))
			.forEach(name-> System.out.println(name));
	}
}

----------------------------------------------------------------------
Stream流属于管道流，只能被消费(使用)一次
第一个Stream流调用完毕后,数据就会流到下一个Stream上,此时第一个流已经使用完毕,就会关闭
所以第一个流不能再调用方法了


映射:map
如果需要将流中的元素映射到另一个流中，可以使用map方法
需要Function函数式接口
public class Demo04Stream_map {
	public static void main(String[] args) {
		Stream<String> stream = Stream.of("1","2","3","4");
		//使用map方法,把字符串类型的整数,转换为Integer类型的整数
		Stream<Integer> stream2 = stream.map((String s)->{
			return Integer.parseInt(s);
		});
		//遍历Stream2流
		stream2.forEach(i-> System.out.println(i));
	}
}
----------------------------------------------------------------------------

统计个数：count
计数流中的元素个数
/*
	long count();
	count是一个终结方法,返回值是一个long类型的整数
 */
public class Demo05Stream_count {
	public static void main(String[] args) {
		ArrayList<Integer> list = new ArrayList<>();
		list.add(1);
		list.add(2);
		list.add(3);
		list.add(4);
		list.add(5);
		list.add(6);
		list.add(7);
		Stream<Integer> stream = list.stream();
		long count = stream.count();
		System.out.println(count);
	}
}
---------------------------------------------------------------

取用前几个：limit
对流进行截取，只取用前n个。
/*
	limit是一个延迟方法,用于截取流中的元素
 */
public class Demo06Stream_limit {
	public static void main(String[] args) {
		String[] arr = {"西门吹雪","叶孤城","陆小凤","楚留香","李寻欢",""};
		Stream<String> stream = Stream.of(arr);
		Stream<String> stream2 = stream.limit(3);//前三个
		stream2.forEach(name-> System.out.println(name));
	}
}
----------------------------------------------------------------------------

跳过前几个：skip
public class Demo07Stream_skip {
	public static void main(String[] args) {
		String[] arr = {"西门吹雪","叶孤城","陆小凤","楚留香","李寻欢"};
		Stream<String> stream = Stream.of(arr);
		//使用skip方法跳过前3个元素
		Stream<String> stream2 = stream.skip(3);//跳过前三
		stream2.forEach(name-> System.out.println(name));//楚留香,李寻欢
	}
}
------------------------------------------------------------------------------

组合：concat
public class Demo08Stream_concat {
	public static void main(String[] args) {
		Stream<String> stream1 = Stream.of("西门吹雪", "叶孤城", "陆小凤", "楚留香", "李寻欢");
		Stream<String> stream2 = Stream.of("杨过", "小龙女", "郭靖", "黄蓉", "周伯通");
		//合并
		Stream<String> concat = Stream.concat(stream1, stream2);
		concat.forEach(name-> System.out.println(name));
	}
}

------------------------------------------------------------------------------------------------

练习：集合元素处理
传统方法：
/*
	现有两个ArrayList集合存储队伍当中的多个成员姓名，要求使用传统for循环
	1. 第一个队伍只要名字为3个字的成员姓名，存储到一个新集合中
	2. 第一个队伍筛选之后，只要前3个人，存储到一个新集合
	3. 第二个队伍只要姓张的成员姓名，存储到一个新集合
	4. 第二个队伍筛选之后不要前2个人，存储到一个新集合中
	5. 将两个队伍合并为一个队伍，存储到一个新集合中
	6. 根据姓名创建Person对象，存储到一个新集合中
	7. 打印整个队伍的Person对象信息

 */
public class Demo01StreamTest {
	public static void main(String[] args) {
		//第一支队伍
		ArrayList<String> one = new ArrayList<>();
		one.add("西门吹雪");
		one.add("陆小凤");
		one.add("叶孤城");
		one.add("李寻欢");
		one.add("楚留香");
		ArrayList<String> one1 = new ArrayList<>();
		for (String name : one) {
			if(name.length()==3){
				one1.add(name);
			}
		}

		ArrayList<String> one2 = new ArrayList<>();
		for (int i = 0; i < 3; i++) {
			one2.add(one1.get(i));
		}


		//第二支队伍
		ArrayList<String> two = new ArrayList<>();
		two.add("杨过");
		two.add("郭襄");
		two.add("小龙女");
		two.add("郭靖");
		two.add("郭嘉");
		two.add("郭奕");
		two.add("黄蓉");

		ArrayList<String> two1 = new ArrayList<>();
		for (String name : two) {
			if(name.startsWith("郭")){
				two1.add(name);
			}
		}
		ArrayList<String> two2 = new ArrayList<>();
		for (int i = 0; i < 2; i++) {
			two2.add(two1.get(i));
		}

		ArrayList<String> all = new ArrayList<>();
		all.addAll(one2);
		all.addAll(two2);

		ArrayList<Person> list = new ArrayList<>();
		for (String name : all) {
			list.add(new Person(name,18));
		}
		for (Person person : list) {
			System.out.println(person);
		}
	}
}
--------------------------------------------------------------
Stream流
public class Demo02StreamTest {
	public static void main(String[] args) {
		//第一支队伍
		ArrayList<String> one = new ArrayList<>();
		one.add("西门吹雪");
		one.add("陆小凤");
		one.add("叶孤城");
		one.add("李寻欢");
		one.add("楚留香");

		Stream<String> oneStream = one.stream().filter(name->name.length()==3).limit(3);

		//第二支队伍
		ArrayList<String> two = new ArrayList<>();
		two.add("杨过");
		two.add("郭襄");
		two.add("小龙女");
		two.add("郭靖");
		two.add("郭嘉");
		two.add("郭奕");
		two.add("黄蓉");

		Stream<String> twoStream = two.stream().filter(name->name.startsWith("郭")).limit(2);
		Stream.concat(oneStream,twoStream)
			.map(name->new Person(name,18))
			.forEach(person-> System.out.println(person));
	}
}