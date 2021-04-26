---
title: '''JavaNote06_IOStream'''
date: 2020-03-26 15:24:37
tags: Java基础
categories: Java
---
## IO概述
I：input
O：output
流：数据，一个字符=2个字节=16个二进制位
java.io
字节流：一切文件数据存储时都是以二进制数字的形式保存的

字节输出流:java.io.OutputStream,此抽象类是表示输出字节流的所有类的超类
定义了一些子类共性的成员方法:
void close() 
关闭此输出流并释放与此流相关联的任何系统资源。 
void flush() 
刷新此输出流并强制任何缓冲的输出字节被写出。 
void write(byte[] b) 
将 b.length字节从指定的字节数组写入此输出流。 
void write(byte[] b, int off, int len) 
从指定的字节数组写入len个字节，从偏移off开始输出到此输出流。 
abstract void write(int b) 
将指定的字节写入此输出流。 

<!--more-->

java.io.FileOutputStream extends OutputStream

文件字节输出流,把内存中的数据写入到硬盘的文件中
构造方法:
FileOutputStream(String name) 
创建文件输出流以指定的名称写入文件。 
FileOutputStream(File file) 
创建文件输出流以写入由指定的 File对象表示的文件。 
构造方法的作用:
1. 创建一个FileOutputStream对象
2. 会根据构造方法中传递的文件/文件路径,创建一个空的文件
3. 会把FileOutputStream对象指向创建好的文件

写入数据的原理(内存->硬盘)
	java程序->JVM->OS(操作系统)->OS调用写数据方法->把数据写入到文件中
	
字节输出流的使用步骤(重点)
1. 创建一个FileOutputStream对象,构造方法中传递写入数据的目的地
2. 调用FileOutputStream对象中的方法write,把数据写入文件中
3. 释放资源(流使用会占用一定的内存,使用完清空)

public class Demo01OutputStream {
	public static void main(String[] args) {
		try {
			FileOutputStream fos = new FileOutputStream("F:\\a.txt");
			fos.write(97);
			fos.close();
		} catch (IOException e) {
			e.printStackTrace();
		}
	}
}
-------------------------------------------------------------------------
public class Demo01OutputStream {
	public static void main(String[] args) throws IOException {

		FileOutputStream fos = new FileOutputStream("F:\\a.txt");
		fos.write(97);//a
		fos.close();

		FileOutputStream f = new FileOutputStream(new File("F:\\b.txt"));
		f.write(49);
		f.write(48);
		f.write(48);//100
		f.close();
		/*
			public void write(byte[] b)
			一次写入多个字节
			如果写入第一个字节是正数（0-127），那么显示时会查询ASCII表
			如果第一个字节是负数，那么第一个字节会和第二个组成中文显示，查询系统默认码表
			void write(byte[] b, int off, int len)
			写入数组指定索引内容

			写入字符串方法,使用String中的方法把字符串转换成字节数组
		 */
		FileOutputStream f1 = new FileOutputStream(new File("F:\\c.txt"));
		byte[] bytes1 = {65,66,67,68,69};//ABCDE
		byte[] bytes2 = {-65,-66,-67,68,69};//烤紻E
		f1.write(bytes1);
		f1.write(bytes2);
		f1.write(bytes2,1,3);//窘D
		byte[] bytes3 = "你好".getBytes();
		f1.write(bytes3);//你好
		f1.close();
		/*
		追加写/续写
			FileOutputStream(String name,boolean append)创建一个向具有指定name的文件中写入数据的输出文件流
			FileOutputStream(File file,boolean append)创建一个向具有指定File对象表示的文件中写入数据的输出文件流
			boolean append:追加写开关
			true:创建对象不会覆盖原文件,继续在文件末尾写
			false:创建一个新文件覆盖
		写换行:
		windows:\r\n
		linux: /n
		mac:\r

		 */
		FileOutputStream f2 = new FileOutputStream(new File("F:\\d.txt"),true);
		for (int i = 0; i < 10; i++) {
			f2.write("你好".getBytes());
			f2.write("\r\n".getBytes());
		}
	}
}

## 字节输入流
InputStream
int read() 
从该输入流读取一个字节的数据。
int read(byte[] b) 
从该输入流读取最多 b.length个字节的数据为字节数组。
void close() 
关闭此文件输入流并释放与流相关联的任何系统资源

java。io.FileInputStream extends InputStream
文件字节输入流
把硬盘文件中的数据读入到内存中使用
构造方法：
FileInputStream(String name) 
通过打开与实际文件的连接来创建一个 FileInputStream ，该文件由文件系统中的路径名 name命名。
FileInputStream(File file) 
通过打开与实际文件的连接创建一个 FileInputStream ，该文件由文件系统中的 File对象 file命名。

读取数据原理（内存->硬盘)
	java程序->JVM->OS(操作系统)->OS调用读取数据方法->读取文件
	
public class Demo01InputStream {
	public static void main(String[] args) throws IOException {
		//1. 创建对象
		FileInputStream fis = new FileInputStream("F:\\a.txt");//abcd
		//2. read方法读取
		int len = fis.read();//97
		System.out.println(len);
		len = fis.read();//98
		System.out.println(len);
		len = fis.read();//99
		System.out.println(len);
		len = fis.read();//100
		System.out.println(len);
		len = fis.read();//读到末尾返回-1
		System.out.println(len);
		fis.close();

		//一次读入多字节
		FileInputStream fis2 = new FileInputStream("F:\\a.txt");//abcd
		byte[] bytes = new byte[2];
		int len2 = fis2.read(bytes);//读取的有效字节数
		System.out.println(len2);//2
		System.out.println(Arrays.toString(bytes));//[97,98]
		System.out.println(new String(bytes));//ab

		fis2.close();
	}
}

----------------------------------------------------------------------------------
## 练习：文件复制
/*
	文件复制
	一读一写
	数据源:E:\图片\头像\1.jpg
	目的地:E:\图片\1.jpg
	复制步骤
	1. 创建一个字节输入流对象,构造方法中绑定要读取的数据源
	2. 创建一个字节输出流对象,构造方法中绑定要写入的目的地
	3. 使用字节输入流中的read读取文件
	4. 使用字节输出流中的方法write,把读取到的字节写入到目的地的文件中
	5. 释放资源
 */
public class Demo01CopyFile {
	public static void main(String[] args) throws IOException {
		//1,创建输入流
		FileInputStream fis = new FileInputStream("E:\\图片\\头像\\1.jpg");
		//2. 创建输出流
		FileOutputStream fos = new FileOutputStream("E:\\图片\\1.jpg");
		//3. read
//		//一次读取一个字节,写入一个字节的方式
//		int len = 0;
//		while((len = fis.read())!=-1){
//			fos.write(len);
//		}

		//使用数组缓冲读取多个字节,写入多个字节
		byte[] bytes = new byte[1024];
		int len = 0;
		while((len = fis.read(bytes))!=-1){
			fos.write(bytes,0,len);
		}
		//5. 释放资源,先关写的,再关读的
		fos.close();
		fis.close();
	}
}


## 字符流

使用字节流读取中文的问题，编码不同使用字节流容易出现问题，使用字符流

字符输入流
java.io.Reader 字符输入流最顶层的父类
共性成员方法:
	int read() 读一个字符 
	int read(char[] cbuf) 将字符读入数组。 
	void close() 关闭流并释放与之相关联的任何系统资源。 

/*
	java.io.FileReader extends InputStreamReader extends Reader
	FileReader:文件字符输入流
	作用:把硬盘文件中的数据以字符的方式读取
	构造方法:
	FileReader(String fileName)
	FileReader(File file)

 */
public class Demo01Reader {
	public static void main(String[] args) throws IOException {
		FileReader fr = new FileReader("F:\\a.txt");
//		int len = 0;
//		while((len = fr.read())!=-1){
//			System.out.print((char) len);//你好abcd
//
//		}
		//一次读取多个字符
		char[] cs = new char[1024];
		int len = 0;
		while((len = fr.read(cs))!=-1){
			/*
				String构造方法
				把字符数组转换成字符串
			 */
			System.out.println(new String(cs,0,len));//你好abcd
		}
		fr.close();
	}
}

## 字符输出流
java.io.Writer:字符输出流,是所有字符输出流的最顶层的父类,是一个抽象类
共性的成员方法:
void write(int c)  写一个字符
void write(char[] cbuf) 写入一个字符数组。 
abstract void write(char[] cbuf, int off, int len) 写入字符数组的一部分。 
void write(String str) 写一个字符串 
void write(String str, int off, int len) 写一个字符串的一部分。
abstract void
flush() 刷新流。 
abstract void
close() 关闭流，先刷新。  

/*
java.io.FileWriter extends OutputStreamWriter extends Writer
FileWriter: 文件字符输出流
作用:把内存中的字符数据写到文件中
构造方法:
FileWriter(File file)
FileWriter(String fileName)


 */
public class Demo01Writer {
	public static void main(String[] args) throws IOException {
		//1. 创建FileWriter对象
		FileWriter fw = new FileWriter("F:\\d.txt");
		//2. write方法把数据写入到内存缓冲区
		fw.write(97);
		//3. flush把内存缓冲区的数据刷新到文件中
		fw.flush();
		//刷新后流可以继续使用
		fw.write(98);

		fw.close();

		FileWriter fw2 = new FileWriter("F:\\d.txt",true);
		char[] cs = {'a','b','c','d'};
		fw2.write(cs);
		fw2.write("\r\n");
		fw2.write(cs,1,3);
		fw2.flush();
		fw2.close();
	}
}

-----------------------------------------------------------------------

## IO异常处理
public class Demo02TryCatch {
	public static void main(String[] args) {
		//提高变量fw的作用域,需要初始化值
		FileWriter fw = null;
		try {
			fw = new FileWriter("F:\\d.txt");
			fw.write(99);
			fw.write(98);
			fw.write(97);
		} catch (IOException e) {
			e.printStackTrace();
		}finally {
			//如果创建对象失败了,fw默认是空,会出现空指针异常,加一个判断
			if(fw!=null){
				try {
					fw.flush();
					fw.close();
				} catch (IOException e) {
					e.printStackTrace();
				}
			}

		}
	}
}
-------------------------------------------------------------------------
JDK7之后，在try后边增加一个(),在括号中可以定义流对象
那么这个流对象的作用域就在try中有效
try中的代码执行完毕,会自动把流对象释放,不用写finally
格式:
try(定义流对象;定义流对象...){
	可能出现异常的代码
}catch(异常类变量 变量名){
	异常的处理逻辑
}

public class Demo02CopyFile {
	public static void main(String[] args) {
		try(//1,创建输入流
			FileInputStream fis = new FileInputStream("E:\\图片\\头像\\1.jpg");
			//2. 创建输出流
			FileOutputStream fos = new FileOutputStream("E:\\图片\\1.jpg")){
			byte[] bytes = new byte[1024];
			int len = 0;
			while((len = fis.read(bytes))!=-1){
				fos.write(bytes,0,len);
			}
		}catch(IOException e){
			System.out.println(e);
		}
	}
}
------------------------------------------------------------------------------
JDK9新特性
try前面可以定义流对象
在try后面的()中可以直接引入流对象的名称(变量名)
在try代码执行完毕后,流对象也可以释放掉,不要写finally
格式:
	A a = new A();
	B b = new B();
	try(a,b){
	}catch(){
	}









## 属性集
Properties 类表示一个持久的属性集。
Properties可保存在流中或从流中加载。属性列表中每个键及其对应值都是一个字符串
/*
	java.util.Properties集合 extends Hashtable<k,v> implements Map<k,v>
	Properties集合是唯一一个和IO流相结合的集合
 	可以使用Properties集合中的方法store,把集合中的临时数据,持久化写入到硬盘存储
 	load,把硬盘中保存的文件(键值对),读取到集合中使用



 */
public class Demo01Properties {
	public static void main(String[] args) throws IOException {
		show01();
		System.out.println("------------------------------------");
		show02();
		System.out.println("------------------------------------");
		show03();
	}



	/*
		使用Properties集合存储数据,遍历取出Properties集合中的数据
		Properties集合是一个双列集合,key和value默认都是字符串
		Properties集合有一些操作字符串的特有方法
			Object setProperty(String key,String value) 调用Hashtable 的方法put
			String getProperty(String key) 通过key找到value值,此方法相当于Map集合中的get(key)方法
			Set<String> stringPropertyNames() 返回此属性列表
	 */

	private static void show01() {
		//创建Properties集合对象
		Properties prop = new Properties();
		//使用setProperty往集合中添加数据
		prop.setProperty("田世庆","172");
		prop.setProperty("呈呈","160");
		prop.setProperty("西门吹雪","180");
		//prop.put(1,true);

		//使用stringPropertyNames把Properties集合的到一个Set集合中
		Set<String> set = prop.stringPropertyNames();

		//遍历set集合,取出每一个键
		for(String key:set){
			//使用getProperty方法通过key获取value
			String value = prop.getProperty(key);
			System.out.println(key+"="+value);
		}

	}
	/*
		void store(OutputStream out,String comments)
		void store(Writer writer,String comments)
		参数:
			OutputStream out: 字节输出流,不能写入中文
			Writer writer: 字符输出流,可以写中文
			String comments: 注释,用来解释说明保存的文件是做什么的,不能使用中文,默认是unicode编码
		使用步骤:
			1. 创建Properties集合对象,添加数据
			2. 创建字节输出流/字符输出流对象,构造方法中绑定要输出的目的地
			3. 使用Properties集合中的方法store,把集合中的临时数据,持久化写入到硬盘中存储
			4. 释放资源

	 */
	private static void show02() throws IOException {
		//创建Properties集合对象
		Properties prop = new Properties();
		//使用setProperty往集合中添加数据
		prop.setProperty("田世庆","172");
		prop.setProperty("呈呈","160");
		prop.setProperty("西门吹雪","180");

		//创建字符流输出对象
		FileWriter fw = new FileWriter("F:\\d.txt");
		prop.store(fw,"save data");
		fw.close();
	}

	/*
		使用load方法,把硬盘中保存的文件(键值对),读取到集合中使用
		void load(InputStream inStream)
		void load(Reader reader)
		参数:
			InputStream inStream: 字节输入流,不能读取含有中文的键值对
			Reader reader: 字符输入流,能读取含有中文的键值对
		使用步骤:
			1. 创建Properties集合对象
			2. 使用Properties集合对象中的方法load读取保存键值对的文件
			3. 遍历Properties集合
		注意:
			1. 存储键值对的文件中,键与值默认的连接符号可以使用=,空格(其他符号)
			2. 存储键值对的文件中,可以使用#进行注释,被注释的键值对不会再被读取
			3. 存储键值对的文件中,键与值默认都是字符串,不用再加引号

	 */

	private static void show03() throws IOException {
		Properties prop = new Properties();
		prop.load(new FileReader("F:\\d.txt"));
		Set<String> set = prop.stringPropertyNames();
		for (String key : set) {
			String value = prop.getProperty(key);
			System.out.println(key+"="+value);
		}
	}
}
--------------------------------------------------------------------------------------

## 缓冲流
字节缓冲输入流，给基本的字节输入流增加一个缓冲区(数组)提高基本的字节输入流的读取效率
BufferedInputStream(new FileInputStream)

缓冲流的基本原理,是在创建流对象的同时,创建一个内置的默认大小的缓冲区数组,通过缓冲区读写,
减少系统IO次数,从而提高读写的效率

字节缓冲流: BufferedInputStream,BufferedOutputStream
字符缓冲流: BufferedReader, BufferedWriter

/*
	java.io.BufferedOutputStream extends OutputStream
	BufferedOutputStream: 字节缓冲输出流

	构造方法:
		BufferedOutputStream(OutputStream out) 创建一个新的缓冲流,以将数据写入指定的底层输出流
		BufferedOutputStream(OutputStream out, int size) 创建一个新的缓冲输出流,以将指定缓冲区大小数据写入指定的底层输出流

	使用步骤:
		1. 创建FileOutputStream对象,构造方法中绑定要输出的目的地
		2. 创建BufferedOutputStream对象,构造方法中传递FileOutputStream对象
		3. write
		4. flush
		5. close
 */
public class Demo01BufferedOutputStream {
	public static void main(String[] args) throws IOException {
		FileOutputStream fos = new FileOutputStream("F:\\d.txt");
		BufferedOutputStream bos = new BufferedOutputStream(fos);
		bos.write("我把数据写入到内部缓冲区中".getBytes());
		bos.flush();
		bos.close();


		/*
			java.io.BufferedInputStream extends InputStream
			BufferedInputStream: 字节缓冲输出流

			构造方法:
				BufferedInputStream(InputStream in) 创建一个新的缓冲流,以将数据写入指定的底层输入流
				BufferedInputStream(InputStream in, int size) 创建一个新的缓冲输出流,以将指定缓冲区大小数据写入指定的底层输入流
		 */

		FileInputStream fis = new FileInputStream("F:\\d.txt");
		BufferedInputStream bis = new BufferedInputStream(fis);
		int len = 0;
		while((len = bis.read())!=-1){
			System.out.println(len);
		}
		bis.close();
	}
}

使用缓冲流效率更高


## 字符缓冲流
public class Demo03BufferedWriter {
	/*
		java.io.BufferedWriter extends Writer
		特有的成员方法:
			void newLine() 写入一个行分隔符,会根据不同的操作系统,获取不同的行分隔符
	 */
	public static void main(String[] args) throws IOException {
		BufferedWriter bw = new BufferedWriter(new FileWriter("F:\\d.txt"));
		for (int i = 0; i < 10; i++) {
			bw.write("田世庆");
			//bw.write("\r\n");
			bw.newLine();//自动换行
		}
		bw.flush();
		bw.close();


		/*
			字符缓冲输入流
			特有的方法:
				String readLine() 读取一行数据

		 */
		BufferedReader br = new BufferedReader(new FileReader("F:\\d.txt"));
		String line1 = br.readLine();
		System.out.println(line1);
		br.close();
	}
}

## 练习:
对文本内容进行排序
(3.入其城，则四顾萧条，寒水自碧，暮色渐起，戍角悲吟。
2.夜雪初霁，荠麦弥望。
8.自胡马窥江去后，废池乔木，犹厌言兵。
4.予怀怆然，感慨今昔，因自度此曲。
5.千岩老人以为有“黍离”之悲也。 
7.过春风十里，尽荠麦青青。
11.纵豆蔻词工，青楼梦好，难赋深情。
9.渐黄昏，清角吹寒，都在空城。 
10.杜郎俊赏，算而今、重到须惊。
12.二十四桥仍在，波心荡、冷月无声。
1.淳熙丙申至日，予过维扬。
13.念桥边红药，年年知为谁生。
6.淮左名都，竹西佳处，解鞍少驻初程。)

/*
	分析:
		1. 创建一个HashMap对象,可以存储每行文本的序号(1,2,3,...);value:存储每行文本
		2. 创建字符缓冲输入流对象,构造方法中绑定字符输入流
		3. 创建字符缓冲输出流对象
		4. 使用字符缓冲输入流中的方法readline,逐行读取文本
		5. 对读取到的文本切割,获取行中的序号和文本内容
		6. 把切割好的序号和文本的内容存储到HashMap集合中(key序号是有序的,会自动排序1,2,3...)
		7. 遍历HashMap,获取每一个键值对
		8. 把每一个键值对,拼接为一个文本行
		9. 把拼接好的文本,使用字符缓冲输出流中的方法write写入文件中
		10.释放资源

 */
/*
	分析:
		1. 创建一个HashMap对象,可以存储每行文本的序号(1,2,3,...);value:存储每行文本
		2. 创建字符缓冲输入流对象,构造方法中绑定字符输入流
		3. 创建字符缓冲输出流对象
		4. 使用字符缓冲输入流中的方法readline,逐行读取文本
		5. 对读取到的文本切割,获取行中的序号和文本内容
		6. 把切割好的序号和文本的内容存储到HashMap集合中(key序号是有序的,会自动排序1,2,3...)
		7. 遍历HashMap,获取每一个键值对
		8. 把每一个键值对,拼接为一个文本行
		9. 把拼接好的文本,使用字符缓冲输出流中的方法write写入文件中
		10.释放资源

 */
public class Demo01SortTxt {
	public static void main(String[] args) throws IOException {
		HashMap<String,String> map = new HashMap<>();
		BufferedReader br = new BufferedReader(new FileReader("F:\\扬州慢.txt"));//要用utf8编码存储
		BufferedWriter bw = new BufferedWriter(new FileWriter("F:\\扬州慢2.txt"));
		String line;
		while ((line = br.readLine())!=null){
			String[] arr = line.split("\\.");
			map.put(arr[0],arr[1]);
		}

		for (String key : map.keySet()) {
			String s = key+ "." + map.get(key);
//			System.out.println(s);
			bw.write(s);
			bw.newLine();
		}
		bw.flush();
		bw.close();
		br.close();
	}
}

## 转换流
FileReader可以读取IDE默认编码格式（UTF-8）的文件
FileReader读取系统默认编码（中文GBK）会产生乱码
要读取默认编码，需要转换流
InputStreamReader 字节流通向字符流的桥梁
OutputStreamWriter 字符流通向字节流的桥梁

/*
	java.io.OutputStreamWriter extends Writer
	OutputStreamWriter: 可使用指定的字符集将写入流中的字符编码成字节
	构造方法:
	OutputStreamWriter(OutputStream out)创建使用默认字符编码的OutputStreamWriter
	OutputStreamWriter(OutputStream out,String charsetName)创建使用指定字符编码的OutputStreamWriter,不指定默认使用utf-8
	使用步骤:
	1. 创建OutputStreamWriter对象,构造方法中传递字节输出流和指定的编码表名称
	2. 使用OutputStreamWriter对象中的方法writer,吧字符转换为字节存储缓冲区中(编码)
	3. 使用OutputStreamWriter对象中的方法flush
	4.close

 */
public class Demo01OutputStreamWriter {
	public static void main(String[] args) throws IOException {
		write_utf_8();
		write_gbk();

		read_utf_8();
		System.out.println("--------------------------");
		read_gbk();
	}


	private static void write_gbk() throws IOException {
		OutputStreamWriter osw = new OutputStreamWriter(new FileOutputStream("F:\\gbk.txt"),"gbk");
		osw.write("你好");
		osw.flush();
		osw.close();

	}

	private static void write_utf_8() throws IOException {
		OutputStreamWriter osw = new OutputStreamWriter(new FileOutputStream("F:\\utf-8.txt"),"utf-8");
		osw.write("你好");
		osw.flush();
		osw.close();
	}


	/*
		java.io.InputStreamReader extends Reader
		构造方法:
		InputStreamReader(InputStream in)
		InputStreamReader(InputStream in,String charsetName)

		注意事项:
		构造方法中指定的编码表名称要和文件的编码相同,否则会乱码
	 */

	private static void read_utf_8() throws IOException {
		InputStreamReader isr = new InputStreamReader(new FileInputStream("F:\\utf-8.txt"));//不指定默认使用u8
		int len = 0;
		while((len = isr.read())!=-1){
			System.out.println((char)len);
		}
		isr.close();
	}

	private static void read_gbk() throws IOException {
		InputStreamReader isr = new InputStreamReader(new FileInputStream("F:\\gbk.txt"),"gbk");
		int len = 0;
		while((len = isr.read())!=-1){
			System.out.println((char)len);
		}
		isr.close();
	}
}

## 练习
转换文件编码

/*
	练习:转换编码
	将GBK编码的文件转换为UTF-8的文本文件
	分析:
		1. 创建InputStreamReader对象,构造方法中传递字节输入流和指定的编码表名称GBK
		2. 创建OutputStreamWriter对象,构造方法中传递字节输出流和指定的编码表名称UTF-8
		3. 使用InputStreamReader对象中的方法read读取文件
		4. 使用OutputStreamWriter对象中的方法write,把读取的数据写入到文件中
		5. 释放

 */

public class ChangeCode {
	public static void main(String[] args) throws IOException {
		InputStreamReader isr = new InputStreamReader(new FileInputStream("F:\\gbk.txt"),"gbk");
		OutputStreamWriter osw = new OutputStreamWriter(new FileOutputStream("F:\\gbk2u8.txt"),"utf-8");
		int len = 0;
		while((len = isr.read())!=-1){
			osw.write(len);
		}
		osw.flush();
		osw.close();
		isr.close();
	}
}

## 序列化和反序列化
Person p = new Person("TT",18);
把对象以流的方式,写入到文件中保存,叫写对象,也叫对象的序列化
对象中包含的不仅仅是字符,使用字节流
ObjectOutputStream:对象的序列化流

把文件中保存的对象,以流的方式读取出来,叫做读对象,也叫对象的反序列化
读取的文件保存的都是字节,使用字节流
ObjectInputStream:对象的反序列化流

/*
	java.io.ObjectOutputStream extends OutputStream
	ObjectOutputStream:对象的序列化流
	作用:把对象以流的方式写入到文件中保存
	构造方法:
	ObjectOutputStream(OutputStream out) 创建写入指定 OutputStream 的ObjectOutputStream
	特有的成员方法:
	void writeObject(Object obj) 将指定的对象写入ObjectOutputStream
	使用步骤:
	1. 创建ObjectOutputStream,构造方法中传递字节输出流
	2. 使用writeObject方法,把对象写入到文件中
	3. 释放资源

	类通过实现Serializable接口以启用序列化功能
 */
public class Demo01ObjectOutputStream {
	public static void main(String[] args) throws IOException, ClassNotFoundException {
		ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("F:\\person.txt"));
		oos.writeObject(new Person("TT",16));
		oos.close();

		/*
			java.io.ObjectInputStream extends InputStream
			把文件中保存的对象以流的方式读取出来
			构造方法:
			ObjectInputStream(InputStream in) 创建指定InputStream读取的ObjectInputStream

			特有的成员方法:
			Object readObject() 从ObjectInputStream读取对象

			使用步骤:
				1. 创建ObjectInputStream,构造方法中传递字节输入流
				2. 使用readObject方法,把对象写入到文件中
				3. 释源
				4.使用读取出来的对象

			readObject方法声明抛出了ClassNotFoundException(class文件找不到异常);
			当不存在对象的class文件时抛出异常
			反序列化的前提:
			1. 类必须实现Serializable
			2. 必须存在类对应class文件
		 */
		ObjectInputStream ois = new ObjectInputStream(new FileInputStream("F:\\person.txt"));
		Object o = ois.readObject();
		ois.close();
		System.out.println(o);
	}
}

## transient关键字：瞬态关键字
static：静态关键字，优先于非静态加载到内存中（静态优先于对象进入内存），被static修饰的成员不能被序列化，序列化的都是对象
transient：修饰后，不能被序列化。如果只是不想某变量被序列化，就用transient

Person类修改后，序列号会发生改变，反序列化会失败
问题：每次修改类的定义，都会给class文件生成一个新的序列号
解决方案：无论是否对类的定义进行修改，都不重新生生成序列号，可以手动给类添加一个序列号
private static final long serialVersionUID = 1L;

## 练习
/*
	练习:序列化集合
	当我们想在文件中保存多个对象的时候
	可以把多个对象存储到一个集合中
	对集合进行序列化和反序列化
	分析:
	1. 定义一个存储Person对象的ArrayList集合
	2. 往ArrayList集合中存储Person对象
	3. 创建一个序列化流ObjectOutputStream对象
	4. 使用writeObject对集合序列化
	5. 创建一个反序列化对象
	6. 使用readObject读取文件中保存的集合
	7. 把Object类型的集合转换成ArrayList类型
	8. 遍历ArrayList集合
	9. 释放资源

 */
public class SerializationExercise {
	public static void main(String[] args) throws IOException, ClassNotFoundException {
		ArrayList<Person> list = new ArrayList<>();
		list.add(new Person("TT",16));
		list.add(new Person("CC",18));
		list.add(new Person("YY",15));
		ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("F:\\list.txt"));
		oos.writeObject(list);
		ObjectInputStream ois = new ObjectInputStream(new FileInputStream("F:\\list.txt"));
		Object o =ois.readObject();
		ArrayList<Person> list2 = (ArrayList<Person>)o;

		for (Person person : list2) {
			System.out.println(person);
		}
		ois.close();
		oos.close();
	}
}

## 打印流
java.io.PrintStream:打印流,为其他输出流添加了功能,使他能够方便地打印各种数据值表示形式
PrintStream特点:
1. 只负责数据的输出,不负责数据的读取
2. 与其他输出流不同,PrintStream 永远不会抛出 IOException
3. 有特有的方法,print,println

注意:如果使用继承父类的write方法写数据,那么查看数据的时候会查询编码表,使用自己的就原样输出

PrintStream ps = new PrintStream("F:\\d.txt");
ps.write(97); //=> a
ps.println(91) //=> 97
ps.close();


可以改变输出语句的目的地
输出语句默认在控制台输出,使用System.setOut方法改变输出语句的目的地为参数中传递的打印流目的地

sout("控制台输出");

PrintStream ps = new PrintStream("F:\\d.txt");
System.setOut(ps);//把输出语句目的地改为d.txt
sout("目的地输出");//会打印在d.txt中