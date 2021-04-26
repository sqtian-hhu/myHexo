---
title: '''DataType'''
date: 2020-10-26 18:30:46
tags: Java基础
categories: Java
---
数组,字符串,集合等等总结
<!--more-->

import java.util.*;

public class 基础结构 {


    public static void main(String[] args) {

        arrays();
        System.out.println();

        strings();
        System.out.println();

        collection();
        System.out.println();

        map();
        System.out.println();

        transfer();
        System.out.println();


    }

    public static void arrays(){
        //数组的初始化
        /**
         * 动态初始化（指定长度）
         * 格式：
         * 数据类型 [] 数组名称 = new 数据类型[数组长度]
         */
        int[] array1 = new int[5];
        array1[0] = 1;
        array1[1] = 2;
        array1[2] = 3;
        array1[3] = 4;
        array1[4] = 5;

        /**
         * 静态初始化（指定内容）
         * 格式：
         * 数据类型 [] 数组名称 = new 数据类型[]{元素一，元素二，元素三，….}
         */

        String[] array2 = new String[]{"a","b","c"};

        //省略格式
        String[] array3 = {"a","b","c"};


        /**
         * 访问数组：数组名称[索引值]
         * 获取数组长度：arrayA.length
         * 数组一旦创建，程序运行期间，长度不可改变
         * 想返回多个值时，需要用数组
         */

        for (int i = 0; i < array1.length; i++) {
            array1[i] += 1;
            System.out.print(array1[i]+" ");    //2 3 4 5 6
        }
    }

    public static void strings(){
        /**
         * 字符串内容永不可变
         * 因此，字符串可以共享使用
         * 字符串效果上相当于char[]
         */

        //创建字符串的方法
        String str1 = "hello"; //直接创建

        char[] charArray = {'A','B','C'};
        String str2 = new String(charArray); //根据字符数组内容创建

        byte[] byteArray = {97,98,99};
        String str3 = new String(byteArray); //根据字节数组

        System.out.print("str1:"+str1+" str2:"+str2+" str3:"+str3);     //str1:hello str2:ABC str3:abc

        /**
         * String中常用方法
         * 长度： str.length()
         *
         * 拼接： str1.concat(str2) //str1不会变化，字符串是常量，返回全新的字符串
         * ""+""
         *
         * 获取指定索引的字符： charAt()
         * char ch = str.charAt(1);
         *
         * 查找参数字符串，第一次出现的索引，没有则返回-1
         * int index = original.indexOf(“cc”);
         *
         * 截取： [ , )
         * public String substring(int index); //截取指定位置到最后
         * public String substring(int begin,int end);
         *
         * 转换成字符数组：
         * char[] chars = “Hello”.toCharArray(); //变成数组
         *
         * 转换成字节数组：
         * byte[] bytes = “abc”.getBytes()
         *
         * 替换：
         * String lang2 = lang1.replace(charsequence oldstring,charsequence newstring);
         *
         * 分割：
         * String str1 = “aaa,bbb,ccc”;
         * String[] a1 = str1.split(“,”);
         */

        for (int i = 0;i<str1.length();i++){
            str2 += str1.charAt(i);

        }

        String newstring = str2.replace("ll","LL");
        System.out.println();
        System.out.print(newstring.substring(2,newstring.length()));     //CheLLo

    }

    public static void collection(){

        /**
         * Collection接口
         * Collection 常用功能
         * public boolean add(E e); 把给定对象添加到当前集合中
         * public void clear(); 清空集合中所有元素
         * public boolean remove(E e); 把给定对象在当前集合中删除
         * public boolean contains(E e); 判断当前集合中是否包含给定对象
         * public boolean isEmpty(); 判断当前集合是否为空
         * public int size(); 返回集合中元素的个数
         * public Object[] toArray(); 把集合中的元素存储到数组中
         *
         *
         * Collection接口
         *              ->1. List接口   -> Vecter集合
         *                              -> ArrayList集合
         *                              -> LinkedList集合
         *              ->2. Set接口    ->TreeSet集合
         *                              ->HashSet集合     ->LinkedHashSet集合
         *
         */


        /**
         * List接口
         * 继承了Collection接口
         * 特点：
         * 1. 有序的集合，存储元素和取出元素的顺序是一致的(存储123，取出123)
         * 2. 有索引，包含了一些带索引的方法
         * 3. 允许存储重复的元素
         * List接口中带索引的方法(特有)
         * add(int index,E element): 将指定的元素添加到该集合指定位置上
         * get(int index): 返回集合中指定位置的元素
         * remove(int index): 移除列表中指定位置的元素，返回的是被移除的元素
         * set(int index,E element): 用指定元素替换集合中指定位置的元素，返回更新前的元素
         */


        /**
         * ArrayList集合：底层使用的是数组，因此增删慢，查找快
         *
         * <>代表泛型 装什么类型，只能放引用类型,不能放基本类型
         * 想放基本类型，必须使用“包装类”
         * 数组长度不可以改变
         * 但ArrayList可以改变
         * ArrayList直接打印得到的不是地址值，而是内容
         * 增加：list.add();
         * 获取：list.get(i);
         * 删除：list.remove(i);//返回删除的值
         * 尺寸：int size = list.size();
         */

        List<Double> doubles = new ArrayList<>();
        doubles.add(100d);
        doubles.add(99.99d);
        for (int i = 0; i < doubles.size(); i++) {
            System.out.print(doubles.get(i)+" ");   //100.0 99.99
        }




        /**
         * LinkedList集合：List接口的链表实现，查询慢，增删快
         * 包含大量首尾元素方法
         * 使用特有方法时，不能使用多态
         * addFirst(E e): 将指定元素插入此列表开头
         * addLast(E e): 将指定元素添加到此列表结尾
         * push(E e): 将元素推入此列表表示的堆栈
         * pop(): 从堆栈弹出一个元素
         * getFirst(): 返回列表第一个元素
         * getLast():返回此列表最后一个元素
         * removeFirst(): 移除并返回此列表第一个元素
         * removeLast(): 一处并返回此列表最后一个元素
         * isEmpty(): 从此列表所表示的堆栈处弹出一个元素
         */
        System.out.println();
        System.out.print("LinkedList");
        LinkedList<Integer> linkedList = new LinkedList<>();
        for (int i = 0; i<4;i++){
            linkedList.addFirst(i*i);
            linkedList.addLast(i);
        }

        for (Integer integer : linkedList) {
            System.out.print(integer);       //LinkedList94100123
        }


        /**
         * Set接口:
         * 继承了Collection接口
         * 不允许存储重复的元素
         * 没有带索引的方法，也不能使用普通的for循环遍历,要使用迭代器Iterator遍历或者增强for循环
         */

        /**
         * HashSet是一个无序的集合，存储元素与取出元素有可能不一致
         * 底层是一个哈希表结构(散列表,查询速度非常快)
         * 哈希表：采用数组+链表+红黑树实现，当链表长度超过阈值(8)时，将链表转换为红黑树，大大减少查找时间
         * 存储数据到集合中，先计算元素的哈希值
         * 数组结构把元素进行了分组，(相同哈希值的元素是一组)链表/红黑树结构把相同哈希值的元素连接到了一起
         */
        System.out.println();
        System.out.print("HashSet:");
        HashSet hashSet = new HashSet<>();
        for (int i = 0;i<5;i++){
            hashSet.add(i);
        }
        hashSet.add("h");
        hashSet.add("a");
        hashSet.add("s");
        hashSet.add("h");
        
        //Set集合遍历第一种方式:使用迭代器遍历
        Iterator it = hashSet.iterator();
        while (it.hasNext()){
            System.out.print(it.next()+ " ");   //HashSet:0 1 a 2 3 s 4 h   无序
        }


        /**
         * LinkedHashSet集合: 继承了HashSet
         * 底层是一个哈希表+链表，多了一条链表记录元素的存储顺序，保证元素有序
         * 有序的，也不允许重复元素
         */

        //不加泛型时默认什么都能存
        LinkedHashSet linkedHashSet = new LinkedHashSet();
        for (int i = 0;i<5;i++){
            linkedHashSet.add(i);
        }
        linkedHashSet.add("h");
        linkedHashSet.add("a");
        linkedHashSet.add("s");
        linkedHashSet.add("h");

        System.out.println();
        System.out.print("LinkedHashSet:");
        
        //Set集合的第二种遍历方式: 使用增强for循环遍历
        for (Object o:linkedHashSet){
            System.out.print(o +" ");  //0 1 2 3 4 h a s   顺序与输入时一致
        }


    }

    public static void map(){
        /**
         * Map集合
         * Map<K,V>
         * 将键映射到值的对象，一个映射不能包含重复的键，每个键最多只能映射一个值
         *
         * 1. Map集合是一个双列集合，一个元素包含两个值(一个是key,一个是value)
         * 2. Map集合中的元素,key和value的数据类型可以相同,也可以不同
         * 3. Map集合中的元素,key不允许重复,value是可以重复的
         * 4. Map集合中的元素,key和value是一一对应的
         *
         * 常用方法
         * put(K key,V value): 把指定的键与指定的值添加到Map集合中
         *                      返回值：V
         *                      存储键值对时，key不重复，返回值V是null。key重复，会使用新的value替换map中重复的value，返回被替换的value值
         * remove(Object key): 把指定的键 所对应的键值对元素 在Map集合中删除,返回被删除元素的值
         *
         * get(Object key): 根据指定的键,在Map集合中获取对应的值
         *                  返回值:
         *                  key存在,返回对应值,key不存在则返回null
         *
         *
         */

        /**
         * HashMap<K,V>集合: Map接口的常用实现类
         * HashMap集合底层是哈希表;查询速度特别快.
         * HashMap集合是一个无序的集合,存储元素和取出元素的顺序可能不一致
         *
         */

        Map<String,Integer> hashMap = new HashMap<>();

        hashMap.put("武力",36);
        hashMap.put("智力",91);
        hashMap.put("统率",87);
        hashMap.put("政治",93);
        hashMap.put("魅力",99);

        //遍历方式一:hashMap.keySet() 把Map集合中所有的key取出来存储在Set集合中
        //          -> 使用迭代器/增强for遍历set集合获取每一个key -> get(key)获取键值
        Set<String> ability = hashMap.keySet();

        System.out.println();
        System.out.print("hashMap第一种遍历: ");
        for (String key : ability) {
            System.out.print(key+":"+hashMap.get(key)+" ");  //hashMap第一种遍历: 政治:93 魅力:99 统率:87 武力:36 智力:91    无序的
        }

        hashMap.remove("魅力");
        hashMap.put("武力",60);

        //遍历方式二:Entry键值对对象：Map.Entry<K,V>
        //在Map接口中有一个内部接口Entry
        //作用：当Map集合一创建，那么就会在Map集合中创建一个Entry对象，用来记录键与值(键值对对象,键与值的映射关系)
        //entrySet(): 把Map集合内部的多个entry对象取出来存储到一个Set集合中 -> 遍历Set集合中的每一个Entry对象
        //Entry对象中的两个方法:getKey()获取所有key,getValue()获取所有value

        Set<Map.Entry<String, Integer>> entries = hashMap.entrySet();

        System.out.println();
        System.out.print("hashMap第二种遍历: ");
        for (Map.Entry<String, Integer> entry : entries) {
            System.out.print(entry.getKey()+":"+entry.getValue()+" ");  //hashMap第二种遍历: 政治:93 统率:87 武力:60 智力:91   无序的
        }


        /**
         * LinkedHashMap<K,V>
         * extends HashMap<K,V>
         * 有序的,存储abc,打印abc
         */
        LinkedHashMap<String,String> linked = new LinkedHashMap<>();
        linked.put("a","A");
        linked.put("c","C");
        linked.put("b","B");
        System.out.println();
        System.out.println(linked);   //{a=A, c=C, b=B},key不允许重复,    有序的


        /**
         * HashTable<K,V>
         * HashMap:底层是一个哈希表,是一个线程不安全的集合,多线程,速度快
         * Hashtable:底层也是一个哈希表,是一个线程安全的集合,是单线程集合,速度慢
         *
         * HashMap集合(之前学的所有集合):可以存储null值,null键
         * Hashtable集合,不能存储null值,null键
         *
         * Hashtable和vector集合一样,在jdk1.2版本之后被更先进的集合(HashMap,ArrayList)取代了
         * Hashtable的子类Properties依然活跃在历史舞台
         * Properties集合是一个唯一和IO流相结合的集合
         */

        Hashtable properties = new Properties();
        properties.put("用户名","letter");
        properties.put("密码","letter");

        System.out.println();
        System.out.println(properties);  //{用户名=letter, 密码=letter}
    }

    public static void transfer(){
        //数组变字符串 连带数组的[]和,全转了
        String[] strings = {"h","e","l","l","o"};

        String str = Arrays.toString(strings); //就长这样的字符串 [h, e, l, l, o]

        System.out.println("数组转换成字符串,strings:"+strings+" str:"+str);
        //数组转换成字符串,strings:[Ljava.lang.String;@4554617c str:[h, e, l, l, o]

        //想变成hello  str不会改变而是再创,因此消耗内存,用StringBuilder
        String str2 = "";
        for (String string : strings) {
            str2 += string;
        }
        System.out.println("直接String拼接:"+str2);     //直接String拼接:hello

        /**
         * String类是常量，创建之后不可改变。进行字符串相加，内存中就会有多个字符串，占用空间多，效率低下
         * StingBuilder类 字符串缓冲区，可以提高字符串的操作效率底层是一个数组，但没有被final修饰，可以改变长度
         * StringBuilder在内存中始终是一个数组，占用空间少，效率高。初始长度16，如果超出容量，会自动扩容
         *
         * 拼接: .append()
         * 获取： .charAt()
         * 转为字符串: .toString()
         */
        StringBuilder str3 = new StringBuilder();
        for (String string : strings) {
            str3.append(string);
        }
        System.out.println("StringBuilder实现:" + str3);  //StringBuilder实现:hello


        //字符串变数组
        char[] strings2 = str3.toString().toCharArray();
        for (char c : strings2) {
            System.out.print(c+"-");           // h-e-l-l-o-
        }

        System.out.println();
        //集合变数组
        List<String> list = new ArrayList<>();
        list.add("a");
        list.add("b");
        list.add("c");

        Object[] listToArray = list.toArray();

        System.out.print("集合变数组:");
        for (Object o : listToArray) {
            System.out.print(o);                //集合变数组:abc
        }

    }
}
