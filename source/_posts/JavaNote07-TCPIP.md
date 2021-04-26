---
title: '''JavaNote07_TCPIP'''
date: 2020-03-30 16:08:29
tags: Java基础
categories: Java
---
## 网络编程
1.1 软件结构
C/S结构：全称Client/Server结构，客户端和服务器结构。QQ迅雷等
B/S结构：全称Browser/Server结构，浏览器和服务器结构。谷歌火狐
1.2 网络通信协议
计算机在连接和通信时要遵守一定的规则,这些连接和通信的规则被称作网络通信协议,
通信双方必须同时遵守才能完成数据交换

TCP/IP协议:定义了计算机如何连入英特网,以及数据如何在他们之间传输的标准
内部包含一系列的用于处理数据通信的协议,并采用了4层的分层模型,每一层都呼叫他的下一层所提供的协议来完成自己的需求
物理层 -> 数据链路层 -> 网络层 -> 传输层 -> 应用层

<!--more-->

1.3 协议分类
UDP:无连接通信协议,即在数据传输时,数据的发送端和接收端不建立逻辑链接.
当一台计算机像另一台计算机发送数据时,发送端不会确认接收端是否存在,就会发出数据,
同样接收端在收到数据时,也不会向发送端反馈是否收到数据


UDP协议消耗资源小,通信效率高,所以通常用于音频视频和普通数据的传输
特点: 数据被限制在64kb以内,超过这个范围就不能发送了

TCP:传输控制协议,面向连接的通信协议,传输数据之前,在发送端和接收端建立逻辑连接,然后再传输数据,
他提供了两台计算机之间可靠无差错的数据传输
在TCP连接中必须要明确客户端与服务器端,有客户端向服务器发出连接请求,每次连接的创建都要经过三次握手
1. 客户端向服务器端发出连接请求,等待服务器确认
2. 服务器端向客户端回送一个响应,通知客户端收到了连接请求
3. 客户端再次向服务器端发送确认信息,确认连接
TCP协议可以保证传输数据的安全,所以应用十分广泛,例如下载文件,浏览网页

1.4 网络编程三要素
1. 协议:计算机网络通信必须遵守的规则
2. IP地址:指互联网协议地址,IP地址用来给一个网络中的计算机设备做唯一的编号.
   IP地址分类: 
         IPV4:一个32位的二进制数,通常分为4段,表示成a.b.c.d的形式,例如192.168.65.100 其中abcd都是0-255之间的十进制整数,最多可以表示42亿个
		 IPV6:为了扩大地址空间,128位二进制数,分成16个字节,分成8段
   查看本机IP地址:ipconfig
   检查网络是否连通:ping 192.168.1.102
   

## 端口号
端口号是一个逻辑端口,我们无法直接看到,可以使用一些软件查看端口号
当我们使用网络软件一打开,那么操作系统就会为网络软件随机分配一个端口号
或者网络软件打开的时候和系统要指定的端口号
端口号两个字节组成,取值范围0-65535之间
注意:1024之前的端口号我们不能使用,已经被系统分配给已知的网络软件了,网络软件的端口号不能重复

使用IP地址加端口号,就可以保证数据正确无误地发送到对方计算机地指定软件上了

常用的端口号:
1. 80端口: 网络端口,www.baidu.com:80 正确的网址
					www.baidu.com:70 错误的网址
2. 数据库: mysql:3306 oracle:1521
3. tomcat服务器: 8080

## TCP通信程序
2.1 概述
TCP通信能实现两台计算机之间的数据交互,通信的两端,要严格区分为客户端与服务器
两端通信时步骤:
1. 服务端程序需要事先启动,等待客户端的连接
2. 客户端主动连接服务器端,连接成功才能通信.服务端不可以主动连接客户端
客户端与服务器端建立一个逻辑连接,这个连接中包含一个对象,
这个对象就是IO对象,客户端与服务器端使用IO对象进行通信
通信数据不仅仅是字符,所以IO对象是字节流对象

步骤:
服务器端: ip:端口号 SeverSocket类
2. 服务器读取客户端发送的数据
InputStream: 你好服务器
3. 服务器端给客户端发送数据
OutputStream:收到谢谢
Socket s1 = sever.accept();                        Socket s2 = sever.accept();
| |
| | 连接通路,IO流对象
| |
客户端 ip:端口号 Socket类
1. 客户端给服务器发送数据
OutputStream: 你好服务器
4. 客户端读取服务器端发送的数据
InputStream:收到谢谢

客户端和服务器端进行一个数据交互,需要4个IO流对象

服务器必须明确两件事情:
1. 多个客户端同时和服务器进行交互,服务器必须明确和哪个客户端进行的交互
	在服务器端accept方法获取到请求客户端的对象
2. 服务器本身没有IO流,使用客户端Socket中提供的IO流和客户端交互


## 客户端代码实现
/*
	TCP通信的客户端：向服务器发送连接请求，给服务器发送数据，读取服务器回写的数据
	表示客户端的类：
		java.net.Socket：此类实现客户端套接字。套接字是两台机器间通信的端点。
		套接字：包含了IP地址和端口号的网络单位

	构造方法：
		Socket(String host，int port) 创建一个流套接字并将其连接到指定主机上的指定端口号
		参数：
			String host：服务器主机的名称/服务器的IP地址
			int port：服务器的端口号
	成员方法：
		OutputStream getOutputStream() 返回此套接字的输出流
		InputStream getInputStream() 返回此套接字的输入流
		void close() 关闭此套接字
	实现步骤:
		1. 创建一个客户端对象Socket,构造方法绑定服务器的IP地址和端口号
		2. 使用Socket对象中的方法getOutputStream()获取网络字节输出流OutputStream对象
		3. 使用write方法给服务器发送数据
		4. 使用Socket中的方法getInputStream()获取网络字节输入流InputStream对象
		5. read方法读取服务器回写的数据
		6. 释放资源(Socket)
	注意:
		1. 客户端与服务器交互,必须使用Socket中提供的网络流,不能使用自己创建的流对象
		2. 当我们创建客户端对象Socket时,就回去请求服务器和服务器经过三次握手建立连接通路
			这时如果服务器没有启动,就会抛出异常
			如果服务器已经启动,就可以进行交互

 */
public class TCPClient {
	public static void main(String[] args) throws IOException {
		//1. 创建一个客户端对象Socket,构造方法绑定服务器的IP地址和端口号
		Socket socket = new Socket("192.168.1.102",8848);
		//2. 获取网络字节输出流
		OutputStream outputStream = socket.getOutputStream();
		//3. 给服务器放松数据
		outputStream.write("你好服务器".getBytes());
		//4. 使用Socket中的方法getInputStream()获取网络字节输入流InputStream对象
		InputStream inputStream = socket.getInputStream();
		//5. read方法读取服务器回写的数据
		byte[] bytes = new byte[1024];
		int len =inputStream.read(bytes);
		System.out.println(new String(bytes,0,len));
		//6.释放资源
		socket.close();
	}
}

## 服务器端代码
/*
	TCP通信的服务器端:接收客户端的请求,读取客户端发送的数据,给客户端回写数据
	表示服务器的类:
		java.net.ServerSocket:此类实现服务器套接字
		构造方法:
			ServerSocket(int port) 创建绑定到特定端口的服务器套接字
		成员方法:
			Socket accept() 倾听并接受到此套接字的连接

		服务器实现步骤:
		1. 创建服务器ServerSocket对象和系统要指定的端口号
		2. 使用ServerSocket对象中的方法accept,获取到请求的客户端对象Socket
		3. 使用Socket对象中的方法getInputStream()获取网络字节输入流InputStream对象
		4. 使用read方法读取客户端发送的数据
		5. 使用Socket对象中的方法getOutputStream()获取网络字节输出流OutputStream对象
		6. 使用write方法回写数据
		7. 释放资源(Socket,ServerSocket)
 */
public class TCPServer {
	public static void main(String[] args) throws IOException {
		ServerSocket serverSocket = new ServerSocket(8848);
		Socket socket = serverSocket.accept();
		InputStream is = socket.getInputStream();
		byte[] bytes = new byte[1024];
		int len =is.read(bytes);
		System.out.println(new String(bytes,0,len));
		OutputStream os = socket.getOutputStream();
		os.write("收到谢谢".getBytes());

		socket.close();
		serverSocket.close();
	}
}


## 文件上传案例
TCP通信的文件上传
原理：客户端读取本地的文件，把文件上传到服务器，服务器再把上传的文件保存到服务器的硬盘上
1. 客户端使用本地的字节输入流读取要上传的文件
2. 客户端使用网络字节输出流，把读取到的文件上传到服务器
3. 服务器使用网络字节输入流，读取客户端上传的文件
4. 服务器使用本地字节输出流，把读取到的文件，保存到服务器的硬盘上
5. 服务器使用网络字节输出流，给客户端回写一个上传成功
6. 客户端使用网络字节输入流读取服务器回写的数据
7. 释放资源

注意：	
	客户端和服务器和本地硬盘进行读写，需要使用自己创建的字节流对象（本地流）
	客户端和服务器之间进行读写，必须使用Socket中提供的字节流对象（网络流）
	
	
/*
	文件上传案例的服务器端:读取客户端上传的文件,保存到服务器的硬盘,给客户端回写上传成功
	明确:
		数据源:客户端上传的文件
		目的地:服务器的硬盘 F:\\upload

 */
public class TCPServer2 {
	public static void main(String[] args) throws IOException {
		//	1. 创建一个服务器对象,和系统要指定的端口号
		ServerSocket server = new ServerSocket(8848);
		//	2. 使用accept方法,获取Socket对象
		/*
			优化:
			让服务器一直处于监听状态(死循环accept方法)
			有一个客户端上传文件就保存一个文件
			while(true)
		 */
		while(true){
			Socket socket = server.accept();

			/*
				优化:
					使用多线程技术,提高程序的效率
					有一个客户端上传文件,就开启一个线程,完成文件的上传
					run方法不能throw
					只能改成try...catch了
			 */
			new Thread(new Runnable() {
				//完成文件的上传
				@Override
				public void run() {
					try {
						// 	3. 获取网络字节输入流
						InputStream is = socket.getInputStream();
						//	4. 判断文件夹是否存在
						File file = new File("F:\\upload");
						if(!file.exists()){
							file.mkdirs();
						}

		/*
			优化:
			自定义一个文件的命名规则:防止同名的文件被覆盖
			规则:域名+毫秒值+随机数
		 */

						String fileName = "itcast"+System.currentTimeMillis() + new Random().nextInt(999999)+".jpg";
						//	5. 创建本地字节输出流对象
//		FileOutputStream fos = new FileOutputStream(file+"\\1.jpg");
						FileOutputStream fos = new FileOutputStream(file+"\\"+fileName);
						//	6. 使用网络字节输入流中的read方法
						int len = 0;
						byte[] bytes = new byte[1024];
						while((len = is.read(bytes))!=-1){
							//	7. 使用本地字节输入流中的write方法
							fos.write(bytes,0,len);
						}
						//	8. 使用socket对象中的方法获取网络字节输出流
						//	9. 使用网络字节输出流中的write方法回写上传成功
						socket.getOutputStream().write("上传成功".getBytes());
						fos.close();

					} catch (IOException e) {
						e.printStackTrace();
					}finally{
						try {
							socket.close();
						} catch (IOException e) {
							e.printStackTrace();
						}
					}
				}
			}).start();


		}

		//服务器不需要再关闭了
		//server.close();
	}
}
-----------------------------------------------------------------------------------

/*
	文件上传案例的客户端读取本地文件，上传到服务器，读取服务器回写的数据
	明确：
		数据源：E:\图片\1.jpg
		目的地:服务器
 */
public class TCPClient2 {
	public static void main(String[] args) throws IOException {
		//1. 创建一个本地字节输入流
		FileInputStream fis = new FileInputStream("E:\\图片\\1.jpg");
		//2. 创建一个客户端Socket对象,绑定IP地址和端口号
		Socket socket = new Socket("192.168.1.102",8848);
		//3. 获取网络字节输出流对象
		OutputStream os = socket.getOutputStream();
		//4. 使用本地字节输入流中的read方法
		int len = 0;
		byte[] bytes = new byte[1024];
		while((len = fis.read(bytes))!=-1){
			//5. 使用网络字节输出流中的write方法
			os.write(bytes,0,len);
		}

		/*
			阻塞状态问题解决:上传完文件,给服务器写一个结束标记
			void shutdownOutput() 禁用此套接字的输出流
			对于 TCP 套接字,任何以前写入的数据都将被发送,并且后跟TCP的正常连接终止序列
		 */
		socket.shutdownOutput();
		//6. 获取网络字节输入流对象
		InputStream is = socket.getInputStream();
		//7. 使用网络字节输入流中的read方法读取服务器回写的数据
		while((len = is.read(bytes))!=-1){
			System.out.println(new String(bytes,0,len));
		}
		//8. 释放资源
		fis.close();
		socket.close();
	}

}

------------------------------------------------------------------------------------------------

## 模拟BS服务器分析

浏览器访问 http://192.168.1.102:8080/Algorithms/web/TSQ.html
=>
GET /Algorithms/web/TSQ.html HTTP/1.1
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: zh-Hans-CN,zh-Hans;q=0.8,en-IE;q=0.5,en;q=0.3
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/70.0.3538.102 Safari/537.36 Edge/18.18362
Accept-Encoding: gzip, deflate
Host: 192.168.1.102:8080
Connection: Keep-Alive
	

/*
	创建BS版的TCP服务器
	http://192.168.1.102:8080/Algorithms/web/TSQ.html

 */
public class TCPServer3 {
	public static void main(String[] args) throws IOException {
		ServerSocket serverSocket = new ServerSocket(8080);
		/*
			浏览器解析服务器回写的html页面,页面中如果有图片,浏览器就会单独开启一个线程,去读取服务器的图片
			我们就得让服务器一直处于监听状态,客户端请求一次,服务器回写一次
		 */

		while(true){
			Socket socket = serverSocket.accept();

			new Thread(new Runnable() {
				@Override
				public void run() {
					try {
						InputStream is = socket.getInputStream();
						//		byte[] bytes = new byte[1024];
						//		int len =0;
						//		while((len=is.read(bytes))!=-1){
						//			System.out.println(new String(bytes,0,len));
						//		}

						//把网络输入字节流转换为字符缓冲输入流
						BufferedReader br = new BufferedReader(new InputStreamReader(is));
						//把客户端请求信息的第一行读取出来 GET /Algorithms/web/TSQ.html HTTP/1.1
						String line = br.readLine();
						//切割读取信息,只要中间部分/Algorithms/web/TSQ.html
						String[] arr = line.split(" ");
						//再把路径前的杠去掉,截取Algorithms/web/TSQ.html
						//??怎么相对路径不行呢??
						String htmlpath = "D:\\IntelliJ IDEA Community Edition 2019.3.1\\"+arr[1].substring(1);

						//创建一个本地字节输入流,构造方法中绑定读取的html路径
						FileInputStream fis = new FileInputStream(htmlpath);
						//使用Socket中的方法获取网络字节输出流
						OutputStream os = socket.getOutputStream();

						//写入HTTP协议响应头,固定三行写法
						os.write("HTTP/.1 200 0k\r\n".getBytes());
						os.write("Content-Type:text/html\r\n".getBytes());
						//必须写入空行,否则浏览器不解析
						os.write("\r\n".getBytes());


						//一读一写,复制文件,把服务器读取的html文件回写到客户端
						byte[] bytes = new byte[1024];
						int len =0;
						while((len=fis.read(bytes))!=-1){
							os.write(bytes,0,len);
						}

						//释放资源
						fis.close();

					} catch (IOException e) {
						e.printStackTrace();
					}finally {
						try {
							socket.close();
						} catch (IOException e) {
							e.printStackTrace();
						}
					}
				}
			}).start();

		}

		//serverSocket.close();
	}
}