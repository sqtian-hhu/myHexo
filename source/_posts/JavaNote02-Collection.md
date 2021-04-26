---
title: '''JavaNote02_Collection'''
date: 2020-03-12 12:08:29
tags: Java基础
categories: Java
---
## 集合
学习集合的目标：
1. 会使用集合存储数据
2. 会遍历集合，把数据取出来
3. 掌握每种集合的特性

<!--more-->
		
集合框架
Collection接口->1. List接口 ->2. Set接口
定义的是所有单列集合中共性的方法，都可以使用的方法 (没有带索引的方法)
集合框架的学习方式：
1. 学习顶层：学习顶层接口/抽象类中共性的方法，所有的子类都可以使用
2. 使用底层：顶层不是接口就是抽象类，无法创建对象使用，需要使用子类


->List接口：
1. 有序的集合(存储和取出元素顺序相同)
2. 允许存储重复的元素
3. 有索引，阔以使用普通的for循环遍历

-->Vecter集合 
-->ArrayList集合
-->LinkedList集合

->Set接口
1. 不允许存放重复元素
2. 没有索引(不能使用普通的for循环遍历)
3. TreeSet集合和HashSet集合是无序的集合(存储和取出元素顺序可能不一致)，LinkedHashSet集合是有序的集合

-->TreeSet集合 
-->HashSet集合            --->LinkedHashSet集合
 
		
## 第一章 Collection接口
Collection 常用功能		
	public boolean add(E e); 把给定对象添加到当前集合中
	public void clear();  清空集合中所有元素
	public boolean remove(E e);  把给定对象在当前集合中删除
	public boolean contains(E e);  判断当前集合中是否包含给定对象
	public boolean isEmpty();   判断当前集合是否为空
	public int size();  返回集合中元素的个数
	public Object[] toArray();  把集合中的元素存储到数组中
	
	
public class Demo01Collection {
	public static void main(String[] args) {
		//创建集合对象，可以使用多态
		Collection<String> coll = new ArrayList<>();
		System.out.println(coll);

		coll.add("TT");//返回一个布尔值，一般是true，所以可以不用接收
		coll.add("CC");
		coll.add("YY");
		System.out.println(coll);

		coll.remove("YY");//返回一个布尔值，集合中存在元素，删除成功返回true，不存在则返回false
		System.out.println(coll.contains("YY"));
		System.out.println(coll);
		System.out.println(coll.isEmpty());
		int size = coll.size();
		System.out.println(size);

		Object[] arr = coll.toArray();
		for (int i = 0; i < arr.length; i++) {
			System.out.println(arr[i]);
		}

		coll.clear();//清空集合，但集合还在
		System.out.println(coll.isEmpty());
	}
}
	
	
		
		
## 第二章 Iterator接口
迭代：即Collection集合元素的通用获取方式。在取元素之前要先判断集合中有没有元素，
如果有，就把这个元素取出来，继续再判断，一直到把集合中所有元素取出

iterator接口常用方法
public E next(): 返回迭代的下一个元素
public boolean hasNext(): 如果仍有元素可以迭代，则返回true



增强for循环，也称for each 循环
专门用来遍历数组和集合
格式：
	for(集合/数组的数据类型 变量名：集合名/数组名){	
	}
	
public class Demo01Foreach {
	public static void main(String[] args) {
		demo01();
		demo02();
	}
	//增强for循环遍历集合
	private static void demo02() {
		ArrayList<String> list = new ArrayList<>();
		list.add("CC");
		list.add("TT");
		list.add("YY");
		for(String s:list){
			System.out.println(s);
		}

	}
	//增强for循环遍历数组
	private static void demo01() {
		int[] arr = {1,2,3,4,5};
		for(int i:arr){
			System.out.println(i);
		}
	}
}

## 第三章 泛型
泛型是一种未知的数据类型，当我们不知道使用什么数据类型的时候，可以使用泛型
泛型也可以看成是一个变量，用来接收数据类型
E代表未知的类型

public class Demo01Generic {

	public static void main(String[] args) {
		/*
		创建集合对象
		不使用泛型：
		好处：默认的类型就是Object类型，可以存储任意类型的数据
		弊端：不安全，会引发异常
	 	*/
		demo01();
		/*
		使用泛型
		好处：1. 避免了类型转换的麻烦
			 2. 把运行期异常，提升到了编译期
		弊端：泛型是什么类型，就只能存储什么类型的数据
		 */
		demo02();

		}

	private static void demo01() {
		ArrayList list = new ArrayList();
		list.add("abc");
		list.add(1);
		//使用迭代器遍历
		//获取迭代器
		Iterator it = list.iterator();
		//使用迭代器的方法遍历集合
		while(it.hasNext()) {
			Object obj = it.next();
			System.out.println(obj);

			//如果想使用String类特有的方法，length获取字符串长度，由于多态不能使用
			//向下转型
			//int 无法强转，容易出现错误,需要使用instanceof避免
			if (obj instanceof String) {
				String s = (String) obj;
				System.out.println(obj + " length:"+s.length());
			}else{
				System.out.println(obj+" not String");
			}
		}
	}

	private static void demo02() {
		ArrayList<String> list = new ArrayList<>();
		list.add("abc");
		list.add("cc");
		list.add("tt");
		for(String s: list){
			System.out.println(s+" length:"+s.length());
		}
	}
}


泛型的定义与使用
定义使用含有泛型的类；
格式：
修饰符 class 类名<代表泛型的变量>{}

public class GenericClass<E> {
	private E name;

	public E getName() {
		return name;
	}

	public void setName(E name) {
		this.name = name;
	}
}

public class Demo02GenericClass {
	public static void main(String[] args) {

		//不写泛型默认为Object
		GenericClass gc = new GenericClass();
		gc.setName("只能是字符串");

		Object name = gc.getName();
		System.out.println(name);

		//创建泛型为Integer类型
		GenericClass<Integer> gc2 = new GenericClass<>();
		gc2.setName(123);
		Integer iname = gc2.getName();
		System.out.println(iname);

		//创建泛型为String类型
		GenericClass<String> gc3 = new GenericClass<>();
		gc3.setName("TT");
		String sname = gc3.getName();
		System.out.println(sname);
	}
}



定义含有泛型的方法：泛型定义在方法的修饰符和返回值类型之间
格式：
修饰符 <泛型> 返回值类型 方法名(参数列表(使用泛型)){
	方法体
}
含有泛型的方法，在调用方法的时候确定泛型的数据类型
传递什么类型的参数，泛型就是什么类型
public class GenericMethod {
	//定义一个含泛型的方法
	public <M> void method01(M m){
		System.out.println(m);
	}

	//定义一个有泛型的静态方法
	public static <S> void method02(S s){
		System.out.println(s);
	}
}


public class Demo03GenericMethod {
	public static void main(String[] args) {
		//创建GenericMethod对象
		GenericMethod gm = new GenericMethod();
		gm.method01(222);
		gm.method01("TTT");
		gm.method02("CCC");//静态方法不建议对象使用

		//静态方法直接类名.方法使用
		GenericMethod.method02("lalala");
	}

}


含有泛型的接口：
格式：
修饰符 interface接口名<代表泛型的变量>{}

public interface GenericInterface<I> {
	public abstract void method(I i);
}

第一种实现方式：

public class GenericInterfaceImpl implements GenericInterface<String> {
	/*
	含有泛型的接口，第一种使用方式：定义接口的实现类，实现接口，指定接口的泛型
	public interface Iterator<E>{}
	 */
	@Override
	public void method(String s) {
		System.out.println(s);
	}
}

第二种实现方式：
public class GenericInterfaceImpl2<I> implements GenericInterface<I>{
	/*
	第二种实现方式，接口使用什么泛型，实现类就使用什么泛型，类跟着接口走
	就相当于定义了一个含有泛型的类
	 */
	@Override
	public void method(I i) {
		System.out.println(i);
	}
}

测试：
public class Demo04GenericInterface {
	/*
	测试含有泛型的接口
	 */
	public static void main(String[] args) {
		//第一种
		GenericInterfaceImpl gil = new GenericInterfaceImpl();
		gil.method("字符串");
		//第二种
		GenericInterfaceImpl2<Double> gi2 = new GenericInterfaceImpl2<>();
		gi2.method(2.2);
	}
}


泛型通配符：
当使用泛型类或者接口时，传递的数据中，泛型类型不确定，可以通过通配符<?>表示，
但是一旦使用泛型的通配符后，只能使用Object类中的共性方法，集合中元素自身方法无法使用
此时只能接收数据，不能往该集合中存储数据

/*
使用方式：不能创建对象使用，只能作为方法的参数使用
 */
public class Demo05Generic {
	public static void main(String[] args) {
		ArrayList<Integer> list01 = new ArrayList<>();
		list01.add(1);
		list01.add(2);

		ArrayList<String> list02 = new ArrayList<>();
		list02.add("a");
		list02.add("b");
		printArray(list01);
		printArray(list02);
	}

	/*
	定义一个方法，能遍历所有类型的ArrayList集合，这时候我们不知道ArrayList集合使用什么数据类型，
	可以用泛型的通配符？来接收数据类型
	注意:
		泛型没有继承概念，想都接收，里面不能写Object
	 */
	public static void printArray(ArrayList<?> list){
		//迭代器遍历集合
		Iterator<?> it = list.iterator();
		while (it.hasNext()){
			System.out.println(it.next());
		}
	}
}

通配符的高级使用---受限泛型
可以设置泛型的上限和下限

/*
	泛型的上限限定： ? extends E 代表泛型只能是E类型的子类/本身
	泛型的下限限定： ? super E 代表使用的泛型只能是E类的父类/本身
 */
public class Demo06Generic {
	public static void main(String[] args) {
		Collection<Integer> list1 = new ArrayList<>();
		Collection<String> list2 = new ArrayList<>();
		Collection<Number> list3 = new ArrayList<>();
		Collection<Object> list4 = new ArrayList<>();
		/*类与类之间继承关系：
			Integer extends Number Extends Object
			String extends Object
		*/
		getElement1(list1);
		//getElement1(list2);//报错
		getElement1(list3);
		//getElement1(list4);//报错

		//getElement2(list1);//报错
		getElement2(list2);
		//getElement2(list3);//报错
		getElement2(list4);
	}

	//泛型的上限，此时的泛型?,必须是Number或Number的子类
	private static void getElement1(Collection<? extends Number> coll1) { }
	//泛型的下限，此时的泛型?,必须是String或String的父类
	private static void getElement2(Collection<? super String> coll2) { }
}


## 第四章 集合综合案例
案例介绍：
按照斗地主的规则，完成洗牌发牌动作
具体规则：使用54张牌打乱顺序，三个玩家参与游戏，三人交替摸牌，每人17张牌，最后三张留作底牌

1. 准备牌：54张牌，存储到一个集合中去
特殊牌：大王，小王
其他52张牌： 定义一个数组/集合，存储四种花色。定义一个数组/集合，存储13个序号
循环嵌套遍历两个数组/集合，组装52张牌
2. 洗牌：使用集合工具类Collection的方法
		 shuffle打乱集合中元素的顺序
3. 发牌：
		要求：1人17张，剩余三张作为底牌，一人一张轮流发牌，集合的索引(0-53)%3
		定义4个集合，存储三个玩家的牌和底牌
		索引%3有三个值：0%3=0，1%3=1，2%3=2，3%3=0
		索引>=51,改底牌发牌
4. 看牌
		直接打印集合，遍历存储玩家和底牌的集合

/*
1. 准备牌
2. 洗牌
3. 发牌
4. 看牌

 */
public class Doudizhu {
	public static void main(String[] args) {
		//1.准备牌
		//定义一个存储54张牌的Arraylist集合，泛型使用String
		ArrayList<String> poker = new ArrayList<>();
		//定义两个数组，一个数组存储牌的花色，一个数组存储牌的序号
		String[] colors = {"♠", "♥", "♣", "♦"};
		String[] numbers = {"2", "A", "K", "Q", "J", "10", "9", "8", "7", "6", "5", "4", "3"};

		//先把大王小王存储到poker中
		poker.add("大王");
		poker.add("小王");
		//循环嵌套遍历两个数组，组装52张牌
		for (String number : numbers) {
			for (String color : colors) {
				//把组装好的牌存入poker中
//				System.out.println(color+number);
				poker.add(color + number);
			}
		}

		//2. 洗牌
		Collections.shuffle(poker);

		//3. 发牌
		//定义四个集合，存储玩家的牌和底牌
		ArrayList<String> player01 = new ArrayList<>();
		ArrayList<String> player02 = new ArrayList<>();
		ArrayList<String> player03 = new ArrayList<>();
		ArrayList<String> dipai = new ArrayList<>();

		for (int i = 0; i < poker.size(); i++) {
			if (i < 51) {
				if (i % 3 == 0) {
					player01.add(poker.get(i));
				} else if (i % 3 == 1) {
					player02.add(poker.get(i));
				} else {
					player03.add(poker.get(i));
				}
			} else {
				dipai.add(poker.get(i));
			}

		}

		//4. 看牌
		System.out.println("赌神：" + player01);
		System.out.println("赌侠：" + player02);
		System.out.println("赌怪：" + player03);
		System.out.println("底牌：" + dipai);

	}
}



## 数据结构
栈：先进后出
入栈123，出栈321
队列：先进先出
存储123，取出123

数组：查询快：数组的地址是连续的，我们通过数组的首地址可以找到数组，通过数组的索引可以快速查找某一个元素
	  增删慢：数组的长度是固定的，我们想要增加/删除一个元素，必须创建一个数组，把源数组的数据复制过来
	  
	 
链表：查询慢：链表中地址不是连续的，每次查询元素都必须从头开始查询
	  增删快：链表结构增加/删除一个元素，对链表的整体结构没有影响，所以增删快。
	链表中每一个元素也称为一个节点
	一个节点包含了一个数据源（存储数组），两个指针域（存储地址）
	自己的地址|数据|下一个节点的地址
	单向链表：链表中只有一条链子，不能保证元素的顺序（存储元素和取出元素的顺序可能不一致）
	双向链表：链表中有两条链子，有一条专门记录元素的顺序，是一个有序的集合
	
红黑树：趋近于平衡树，查询速度非常快，查询叶子节点最大次数和最小次数不能超过2倍
		约束：1. 节点可以是红色的或者是黑色的
			  2. 根节点是黑色的
			  3. 叶子节点(空节点）是黑色的
			  4. 任何一个节点到其每一个叶子节点的所有路径上的黑色节点数相同


			  
## List接口
继承了Collection接口
特点：
	1. 有序的集合，存储元素和取出元素的顺序是一致的(存储123，取出123)
	2. 有索引，包含了一些带索引的方法
	3. 允许存储重复的元素
	
List接口中带索引的方法(特有)
	add(int index,E element): 将指定的元素添加到该集合指定位置上
	get(int index): 返回集合中指定位置的元素
	remove(int index): 移除列表中指定位置的元素，返回的是被移除的元素
	set(int index,E element): 用指定元素替换集合中指定位置的元素，返回更新前的元素
注意： 操作索引时，一定要防止索引越界异常
public class Demo01List {
	public static void main(String[] args) {
		//创建一个List集合对象，多态
		List<String> list = new ArrayList<>();
		//使用add的方法往集合中添加元素
		list.add("a");
		list.add("b");
		list.add("c");
		list.add("a");
		//打印集合
		System.out.println(list); //[a,b,c,a] 有序，允许重复
		list.add(3,"TT");
		System.out.println(list); //[a, b, c, TT, a]
		list.remove(2); //返回c
		System.out.println(list); //[a, b, TT, a]
		list.set(3,"A"); //返回a
		System.out.println(list); //[a, b, TT, A]
	}
}


List的实现类
	ArrayList集合：底层使用的是数组，因此增删慢，查找快
	
	
	LinkedList集合：
	1. List接口的链表实现，查询慢，增删快
	2. 包含大量首尾元素方法
	3. 使用特有方法时，不能使用多态
	addFirst(E e): 将指定元素插入此列表开头 
	addLast(E e): 将指定元素添加到此列表结尾
	push(E e): 将元素推入此列表表示的堆栈
	pop(): 从堆栈弹出一个元素
	getFirst(): 返回列表第一个元素
	getLast():返回此列表最后一个元素
	removeFirst(): 移除并返回此列表第一个元素
	removeLast(): 一处并返回此列表最后一个元素
	isEmpty(): 从此列表所表示的堆栈处弹出一个元素
	
	LinkedList<String> linked = new LingkedList<>();
	

## Set接口：
继承了Collection接口
1. 不允许存储重复的元素
2. 没有带索引的方法，也不能使用普通的for循环遍历

Set的实现类
	HashSet：
	1. 不允许存储重复元素
	2. 没有带索引的方法，也不能使用普通的for循环遍历
	3. 是一个无序的集合，存储元素与取出元素有可能不一致
	4. 底层是一个哈希表结构(查询速度非常快)
	public class Demo01Set {
	public static void main(String[] args) {
		Set<Integer> set = new HashSet<>();
		set.add(1);
		set.add(3);
		set.add(2);
		set.add(1);
		//使用迭代器遍历
		Iterator<Integer> it = set.iterator();
		while (it.hasNext()) {
			System.out.println(it.next());//1 2 3
		}

	}
}

哈希值：是一个十进制的整数，由系统随即给出(就是对象的地址值，是一个逻辑地址，是模拟出来得到的地址，不是实际存储的物理地址(16进制)，进制不同)
		Obje类中有一个方法，可以获取对象的哈希值
		int hashCode() 返回对象哈希码值
哈希表：采用数组+链表+红黑树实现，当链表长度超过阈值(8)时，将链表转换为红黑树，大大减少查找时间

存储数据到集合中，先计算元素的哈希值
数组结构：把元素进行了分组，(相同哈希值的元素是一组)链表/红黑树结构把相同哈希值的元素连接到了一起
 0 | 1 | 2 |...| 15 |  初始容量16
 a   c
 a   c
哈希值相同的元素用链表存在同一个组里，超过8个就会把链表转换成红黑树

Set集合不允许存储重复元素的原理
public class Demo02HashSetSaveString {
	public static void main(String[] args) {
		//创建HashSet集合对象
		HashSet<String> set = new HashSet<>();
		String s1 = new String("abc");
		String s2 = new String("abc");

		set.add(s1);
		set.add(s2);
		set.add("重地");
		set.add("通话");
		set.add("abc");
		System.out.println(set);//[重地, 通话, abc]
	}
}

Set集合在调用add方法的时候，add方法会调用元素的hashCode方法和equals方法，判断是否重复
set.add(s1);
add方法会调用s1的hashCode方法，计算字符串“abc”的哈希值，哈希值是96354
在集合中找有没有96354这个哈希值的元素，发现没有
就会把s1存到集合中

set.add(s2);
add方法会调用s2的hashCode方法，计算字符串“abc”的哈希值，哈希值是96354
在集合中找有没有96354这个哈希值的元素，发现有(哈希冲突)
s2会调用equals方法和哈希值相同的元素进行比较s2.equals(s1),返回true
两个元素哈希值相同，equals方法返回true，认定两个元素相同
就不会把s2存到集合中

set.add("重地");
add方法会调用"重地"的hashCode方法，计算字符串"重地"的哈希值，哈希值是1179395
在集合中找有没有1179395这个哈希值的元素，发现没有
就会把"重地"存到集合中

set.add("通话");
add方法会调用"通话"的hashCode方法，计算字符串"通话"的哈希值，哈希值是1179395
在集合中找有没有1179395这个哈希值的元素，发现有(哈希冲突)
"通话"会调用equals方法和哈希值相同的元素进行比较"通话".equals("重地"),返回false
两个元素哈希值相同，equals方法返回true，认定两个元素不同
就会把"通话"存到集合中



HashSet存储自定义类型元素
给HashSet中存放自定义元素时，需要重写对象中的hashCode和equals方法，
建立自己的比较方式，才能保证HashSet集合中的对象唯一
    @Override
	public boolean equals(Object o) {
		if (this == o) return true;
		if (o == null || getClass() != o.getClass()) return false;
		Person person = (Person) o;
		return age == person.age &&
			Objects.equals(name, person.name);
	}

	@Override
	public int hashCode() {
		return Objects.hash(name, age);
	}
	

LinkedHashSet集合
继承了HashSet
底层是一个哈希表+链表，多了一条链表记录元素的存储顺序，保证元素有序
有序的，也不允许重复元素


可变参数：
如果我们定义一个方法需要接受多个参数，并且多个参数类型一致，我们可以对其简化成如下格式
修饰符 返回值类型 方法名(参数类型... 形参名){}

使用前提：当方法的参数列表数据类型已经确定，但是参数的个数不确定，就可以使用可变参数	
public class Demo01VarArgs {
	public static void main(String[] args) {
		/*
		定义计算（0-n）整数和的方法
		已知：计算整数的和，数据类型确定为int
		但是参数个数不确定，不知道要计算几个整数的和，就可以使用可变参数
		add();就会创建一个长度为0的数组，new int[0]
		add(10);就会创建一个长度为1的数组，new int[]{10}
		add(10,20);就会创建一个长度为2的数组，new int[]{10,20}

		注意事项：
		1. 一个方法的参数列表，只能有一个可变参数
		2. 如果方法的参数有多个，那么可变参数必须在参数列表的末尾

		 */
		int i = add();
		System.out.println(i);
	}

	public static int add(int... arr) {
		System.out.println(arr);
		return 0;
	}

	//可变参数终极写法,用Object接收所有类型
	public static void method(Object... obj) {

	}

}


## Colletions
集合工具类，用来对集合进行操作
部分方法：
Collections.addAll(Collection<T> c, T... elements):往集合中添加一些元素
Collections.shuffle(List<?> list) 打乱集合顺序
Collections.sort(List<?>  list): 将集合中元素按照默认规则排序。
Collections.sort(List<T> list,Comparator<? super T>):将集合中元素按照制定规则排序
注意：sort方法使用前提，被排序的集合里面存储的元素，必须实现Comparable接口，重写接口中的compareTo方法定义排序的规则

Collections.sort(List<?>  list):
public class Person implements Comparable<Person>{
	private String name;
	private int age;
	@Override
	public int compareTo(Person o) {
		//自定义比较的规则，比较两个人的年龄
		//return this.getAge() - o.getAge();//年龄升序排序
		return o.getAge() - this.getAge();//年龄降序排序

	}
}

Comparable接口排序规则：
	自己(this) - 参数：升序

Collections.sort(List<T> list,Comparator<? super T>):
Comparator和Comparable的区别
	Comparable：自己(this)和别人(参数)比较，自己需要实现Comparable接口，重写比较的规则compareTo方法
	Comparator：相当于找一个第三方的裁判，比较两个
	
Collections.sort(list01,new Comparator<Integer>(){     //匿名内部类
	//重写比较规则
	publicinterestingcompare（Integer o1，Integer o2){
		//return o1-o2;//升序
		return o2-o1;//降序
	}
});

## day04 Map

第一章 Map集合
Map<K,V>
将键映射到值的对象，一个映射不能包含重复的键，每个键最多只能映射一个值
特点：1. Map集合是一个双列集合，一个元素包含两个值(一个是key,一个是value)
	  2. Map集合中的元素,key和value的数据类型可以相同,也可以不同
	  3. Map集合中的元素,key不允许重复,value是可以重复的
	  4. Map集合中的元素,key和value是一一对应的
	  
## Map接口常用实现类
I. HashMap<K,V>集合:
	1. HashMap集合底层是哈希表;查询速度特别快.
	jdk1.8之后:数组+单向链表/红黑树
	2. HashMap集合是一个无序的集合,存储元素和取出元素的顺序可能不一致

	LinkedHashMap<K,V>集合 extends HashMap<K,V>集合
	LinkedHashMap<K,V>集合的特点:
	1. LinkedHashMap集合底层是哈希表+链表(保证迭代顺序)
	2. LinkedHashMap集合是有序的集合,存储元素和取出元素是一致的

Map接口中的常用方法:
public class Demo01Map {
	public static void main(String[] args) {
		show01();//put方法
		show02();//remove方法
		show03();//get方法

	}


	/*
		public V put(K key,V value): 把指定的键与指定的值添加到Map集合中
	 	返回值：V
	 		存储键值对时，key不重复，返回值V是null。key重复，会使用新的value替换map中重复的value，返回被替换的value值
	 */
	private static void show01() {
		//创建Map集合对象，多态
		Map<String,String> map = new HashMap<>();
		String v1 = map.put("TT","CC");
		System.out.println("v1："+v1);//v1：null

		String v2 = map.put("TT","YY");
		System.out.println("v2："+v2);//key重复，返回v2：CC

		System.out.println(map);//{TT=YY}

		map.put("杨过","小龙女");
		map.put("尹志平","小龙女");
		System.out.println(map);//{TT=YY, 杨过=小龙女, 尹志平=小龙女}
	}

	/*
		public V remove(Object key): 把指定的键 所对应的键值对元素 在Map集合中删除,返回被删除元素的值
	 	返回值：V
	 		key存在,v返回被删除的值
	 		key不存在,v返回null
	 */


	private static void show02() {
		//创建Map对象
		Map<String,Integer> map = new HashMap<>();
		map.put("TT",172);
		map.put("CC",160);
		map.put("YY",178);
		System.out.println(map);//{TT=172, CC=160, YY=178}
		Integer v1 = map.remove("YY");
		System.out.println("v1:"+v1);//v1:178
		System.out.println(map);//{TT=172, CC=160}
	}

	/*
	public V get(Object key)根据指定的键,在Map集合中获取对应的值
		返回值:
			key存在,返回对应值,key不存在则返回null
	 */
	private static void show03() {
		Map<String,Integer> map = new HashMap<>();
		map.put("TT",172);
		map.put("CC",160);
		map.put("YY",178);
		Integer t = map.get("TT");
		Integer c = map.get("bao");
		System.out.println("t:"+t +" c:" + c);//t:172 c:null

		//containsKey(Object key) 判断是否包含指定键,包含返回true,不包含返回false
		System.out.println(map.containsKey("TT"));//true
	}
}


Map集合的遍历：
第一种：
Set<K> keySet() 把Map集合中所有的key取出来存储在Set集合中 -> 使用迭代器/增强for遍历set集合获取每一个key -> get(key)获取键值
/*
	Map集合第一种遍历方式:通过键找值
	Set<K> keySet() 返回此映射中包含的键的视图
 */
public class Demo02KeySet {
	public static void main(String[] args) {
		Map<String,Integer> map = new HashMap<>();
		map.put("TT",172);
		map.put("CC",160);
		map.put("YY",178);

		//1. 取出key放到集合
		Set<String> set = map.keySet();
		//2. 迭代器遍历set集合
		Iterator<String> it = set.iterator();
		while (it.hasNext()){
			String key = it.next();
			//3. get
			Integer value = map.get(key);
			System.out.println(key+"="+value);
		}

		//增强for
		for (String key:map.keySet()){
			Integer value = map.get(key);
			System.out.println("foreach:"+key+"="+value);
		}
	}
}


第二种：
Entry键值对对象：Map.Entry<K,V>
在Map接口中有一个内部接口Entry
作用：当Map集合一创建，那么就会在Map集合中创建一个Entry对象，用来记录键与值(键值对对象,键与值的映射关系)
entrySet(): 把Map集合内部的多个entry对象取出来存储到一个Set集合中 -> 遍历Set集合中的每一个Entry对象 

Entry对象中的两个方法:getKey()获取所有key,getValue()获取所有value

/*
	Map集合遍历的第二种方式:使用Entry对象遍历
	Map集合中的方法:
		Set<Map.Entry<K,V>> entrySet() 返回此映射中包含的映射关系的Set视图
 */
public class Demo03EntrySet {
	public static void main(String[] args) {
		Map<String,Integer> map = new HashMap<>();
		map.put("TT",172);
		map.put("CC",160);
		map.put("YY",178);

		//1. 使用Map集合中的方法entry Set(),把Map中多个Entry出来,存储到Set集合中
		Set<Map.Entry<String,Integer>> set = map.entrySet();
		//2. 遍历Set
		//迭代器
		Iterator<Map.Entry<String,Integer>> it = set.iterator();
		while(it.hasNext()){
			Map.Entry<String,Integer> entry = it.next();
			System.out.println(entry);
			System.out.println(entry.getKey());
			System.out.println(entry.getValue());
		}
		System.out.println("-------------------------------------");
		//增强for
		for (Map.Entry<String,Integer> entry:set) {
			System.out.println(entry);
		}
	}
}


HashMap存储自定义类型键值:
/*
	HashMap存储自定义类型键值
	Map集合保证key唯一:作为key的元素,必须重写hashCode方法和equals方法,以保证key唯一
 */
public class Demo01HashMapSavePerson {
	public static void main(String[] args) {
		show01();
		System.out.println("-------------------------------");
		show02();
	}

	/*
		HashMap存储自定义类型键值
		key:String类型
			String类重写了hashCode方法和equals方法,可以保证key唯一
		value:Person类型
			value可以重复(同名同年龄)

	 */
	private static void show01() {
		HashMap<String,Person> map = new HashMap<>();
		map.put("TT",new Person("田世庆",18));
		map.put("CC",new Person("呈呈",18));
		map.put("YY",new Person("杨二",15));
		map.put("ZZ",new Person("阿爆",17));
		map.put("TT",new Person("阿浪",18));
		Set<String> set = map.keySet();
		for (String key:set) {
			System.out.println(key+"-->"+map.get(key));
		}
		/*
			TT-->Person{name='阿浪', age=18}
			CC-->Person{name='呈呈', age=18}
			YY-->Person{name='杨二', age=15}
			ZZ-->Person{name='阿爆', age=17}
			key唯一
		 */
	}

	/*
		HashMap存储自定义类型键值
		key:Person类型
			Person类必须重写hashCode方法和equals方法,保证key唯一
		value:String类型
			String可以重复

	 */
	private static void show02() {
		HashMap<Person,String> map = new HashMap<>();
		map.put(new Person("田世庆",18),"TT");
		map.put(new Person("呈呈",18),"CC");
		map.put(new Person("杨二",15),"YY");
		map.put(new Person("阿爆",17),"ZZ");
		map.put(new Person("田世庆",18),"Tian");
		Set<Person> set = map.keySet();
		for (Person key:set) {
			System.out.println(key+"-->"+map.get(key));
		}
		/*
			Person{name='杨二', age=15}-->YY
			Person{name='呈呈', age=18}-->CC
			Person{name='田世庆', age=18}-->Tian
			Person{name='阿爆', age=17}-->ZZ
			重写hashCode和equals后保证了唯一
		 */
	}
}


LinkedHashMap<K,V>
extends HashMap<K,V>
有序的,存储abc,打印abc
public class Demo01LinkedHashMap {
	public static void main(String[] args) {
		HashMap<String,String> map = new HashMap<>();
		map.put("a","A");
		map.put("c","C");
		map.put("b","B");
		System.out.println(map);//{a=A, b=B, c=C},key不允许重复,无序

		LinkedHashMap<String,String> linked = new LinkedHashMap<>();
		linked.put("a","A");
		linked.put("c","C");
		linked.put("b","B");
		System.out.println(linked);//{a=A, c=C, b=B},key不允许重复,有序
	}
}

## Map接口常用实现类
II. HashTable<K,V>
/*
	Hashtable:底层也是一个哈希表,是一个线程安全的集合,是单线程集合,速度慢
	HashMap:底层是一个哈希表,是一个线程不安全的集合,多线程,速度快
	HashMap集合(之前学的所有集合):可以存储null值,null键
	Hashtable集合,不能存储null值,null键

	Hashtable和vector集合一样,在jdk1.2版本之后被更先进的集合(HashMap,ArrayList)取代了
	Hashtable的子类Properties依然活跃在历史舞台
	Properties集合是一个唯一和IO流相结合的集合

 */
public class Demo02Hashtable {
	public static void main(String[] args) {
		HashMap<String,String> map = new HashMap<>();
		map.put(null,"a");
		map.put("b",null);
		map.put(null,null);
		System.out.println(map);//{null=null, b=null},允许空值空键

		Hashtable<String,String> table = new Hashtable<>();
		//map.table(null,"a");
		//map.table("b",null);都会空指针异常
	}
}

## 练习
计算一个字符串中每个字符出现次数
使用Scanner获取用户输入的一个字符串 
aaabbbcca
a            4
b            4
c            2
不能重复 可以重复
字符     统计个数
=> 使用HashMap<Character,Integer>
=> 遍历字符串,获取每一个字符
   1. String类的方法toCharArray,把字符串转为为一个字符数组,遍历数组
   2. String类的方法length() + charAt(索引)
   
=> 使用Map集合中的方法判断获取到的字符是否存储在Map集合中
   1. 使用Map集合中的方法containsKey(获取到的字符),返回布尔值
		true:
		value++
		false:
		把字符作为key,value设为1
   2. 使用Map集合的get(key)也可以判断

/*
	练习
		计算一个字符串中每个字符出现次数
 */
public class Exercise01 {
	public static void main(String[] args) {
		//1.使用Scanner获取用户输入的一个字符串
		Scanner sc = new Scanner(System.in);
		System.out.println("请输入一个字符串:");
		String str = sc.next();
		//2. 创建HashMap集合
		HashMap<Character,Integer> map = new HashMap<>();
		//3. 遍历字符串
		for(char c:str.toCharArray()){
			//4. 判断集合中是否已存在
			if(map.containsKey(c)){
				map.put(c,map.get(c)+1);
			}else{
				map.put(c,1);
			}
		}
		System.out.println(map);
	}
}

## JDk9对集合添加的优化
新特性:List接口,Set接口,Map接口里面增加了一个静态的方法of,可以给集合一次性添加多个元素
static <E> List<E> of (E... elements)
使用前提:当集合中存储的元素的个数已经确定,不再改变时使用
注意: 1. of方法只适用于List接口,Set接口,Map接口,不适用于接口的实现类
	  2. of方法的返回值是一个不能改变的集合,集合不能再使用add,put方法添加元素,会抛出异常
	  3. Set接口和Map接口在调用of方法的时候,不能有重复的元素,否则会抛出异常
	  
List<String> list = List.of("a","b","a");
	  
  


## Debug追踪
/*
	Debug调试程序:
		可以让代码逐行执行,查看代码执行的过程
	使用方式:在行号右边,左键单击,添加断点(每个方法的第一行,哪里有bug添加哪里)
	右键,选择Debug执行
	程序会停止在添加的第一个断点处
	执行程序:
		f8:逐行执行程序
		f7:进入方法中
		shift+f8:跳出方法
		f9:跳到下一个断点,没有则结束程序
		ctrl+f2:退出debug
 */
 
 

## 斗地主案例2
有序版本
1. 准备牌：
54张牌，存储到一个集合中去
特殊牌：大王，小王
其他52张牌： 定义一个数组/集合，存储四种花色。定义一个数组/集合，存储13个序号
循环嵌套遍历两个数组/集合，组装52张牌
2. Map存储52张牌
牌的索引  组装好的牌
key       value
0         大王         
1         小王
2         ♠2
.         .
.         .
.         .
53        ♦3

创建一个List保存索引,对牌的索引shuffle
3. 发牌
一人一张每人17张
集合索引%3
最后三张给底牌
4. 排序
Collections方法soft(List)
5.看牌:可以使用查表法
遍历List集合,根据索引key获取Map的value
/*
	有序版本
	1. 准备牌
	2. 洗牌
	3. 发牌
	4. 排序
	5. 看牌
 */
public class Doudizhu2 {
	public static void main(String[] args) {
		//1.准备牌
		HashMap<Integer,String> poker = new HashMap<>();
		//创建一个集合存放索引
		List<Integer> pokerIndex = new ArrayList<>();
		//组装牌
		String[] colors = {"♠","♥","♣","♦"};
		String[] numbers = {"2", "A", "K", "Q", "J", "10", "9", "8", "7", "6", "5", "4", "3"};
		int index = 0;
		poker.put(index,"大王");
		pokerIndex.add(index);
		index++;
		poker.put(index,"小王");
		pokerIndex.add(index);
		index++;

		for (String number : numbers) {
			for (String color : colors) {
				poker.put(index,color+number);
				pokerIndex.add(index);
				index++;
			}
		}
		//System.out.println(poker);

		//2. 洗牌
		Collections.shuffle(pokerIndex);

		//3. 发牌
		List<Integer> player1 = new ArrayList<>();
		List<Integer> player2 = new ArrayList<>();
		List<Integer> player3 = new ArrayList<>();
		List<Integer> dipai = new ArrayList<>();

		for (int i = 0; i < pokerIndex.size(); i++) {
			if(i<51){
				if(i%3==0){
					player1.add(pokerIndex.get(i));
				}else if(i%3==1){
					player2.add(pokerIndex.get(i));
				}else{
					player3.add(pokerIndex.get(i));
				}
			}else{
				dipai.add(pokerIndex.get(i));
			}
		}

		//4. 排序
		Collections.sort(player1);
		Collections.sort(player2);
		Collections.sort(player3);
		Collections.sort(dipai);

		//5. 看牌
		show("周星驰",player1,poker);
		show("周润发",player2,poker);
		show("卢本伟",player3,poker);
		show("底牌",dipai,poker);
	}

	private static void show(String name,List<Integer> player,HashMap<Integer,String> poker) {
		System.out.print(name+": ");
		for (Integer key : player) {
			System.out.print(poker.get(key) + " ");
		}
		System.out.println();
		System.out.println("--------------------------------");
	}
}
