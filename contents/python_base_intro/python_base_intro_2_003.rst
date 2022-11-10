网络编程
##################################################################################

IP 地址的介绍
**********************************************************************************

* IP 地址的概念

IP 地址就是标识网络中设备的一个地址，好比现实生活中的家庭地址。

网络中的设备效果图:

|image0|

* IP 地址的表现形式

::

	IP 地址分为两类： IPv4 和 IPv6
	IPv4 是目前使用的ip地址
	IPv6 是未来使用的ip地址
	IPv4 是由点分十进制组成
	IPv6 是由冒号十六进制组成

* IP 地址的作用

IP 地址的作用是标识网络中唯一的一台设备的，也就是说通过IP地址能够找到网络中某台设备。

|image1|

* 查看 IP 地址

::

	Linux 和 mac OS 使用 ifconfig 这个命令
	Windows 使用 ipconfig 这个命令

ifconfig 和 ipconfig 都是查看网卡信息的，网卡信息中包括这个设备对应的IP地址

|image2|

.. tip::

	| 192.168.1.107是设备在网络中的IP地址
	| 127.0.0.1表示本机地址，提示：如果和自己的电脑通信就可以使用该地址。
	| 127.0.0.1该地址对应的域名是localhost，域名是 ip 地址的别名，通过域名能解析出一个对应的ip地址。

* 检查网络是否正常

检查网络是否正常使用 ping 命令, 检查网络是否正常效果图

|image3|

.. tip::

	| ping www.baidu.com 检查是否能上公网
	| ping 当前局域网的ip地址 检查是否在同一个局域网内
	| ping 127.0.0.1 检查本地网卡是否正常

* 小结

::

	IP 地址的作用是标识网络中唯一的一台设备的
	IP 地址的表现形式分为: IPv4 和 IPv6
	查看网卡信息：ifconfig
	检查网络： ping

端口和端口号的介绍
**********************************************************************************

* 问题思考

| 不同电脑上的飞秋之间进行数据通信，它是如何保证把数据给飞秋而不是给其它软件呢?
| 其实，每运行一个网络程序都会有一个端口，想要给对应的程序发送数据，找到对应的端口即可。

端口效果图: 

|image4|

* 什么是端口

| 端口是传输数据的通道，好比教室的门，是数据传输必经之路。
| 那么如何准确的找到对应的端口呢? 其实，每一个端口都会有一个对应的端口号，好比每个教室的门都有一个门牌号，想要找到端口通过端口号即可。

端口号效果图: 

|image5|

* 什么端口号

| 操作系统为了统一管理这么多端口，就对端口进行了编号，这就是端口号，端口号其实就是一个数字，好比我们现实生活中的门牌号, 端口号有65536个。
| 那么最终飞秋之间进行数据通信的流程是这样的，通过ip地址找到对应的设备，通过端口号找到对应的端口，然后通过端口把数据传输给应用程序。

最终通信流程效果图:

|image6|

* 端口和端口号的关系

端口号可以标识唯一的一个端口。

* 端口号的分类

::

	知名端口号
	动态端口号

知名端口号:

| 知名端口号是指众所周知的端口号，范围从0到1023。
| 这些端口号一般固定分配给一些服务，比如21端口分配给FTP(文件传输协议)服务，25端口分配给SMTP（简单邮件传输协议）服务，80端口分配给HTTP服务。

动态端口号:

| 一般程序员开发应用程序使用端口号称为动态端口号, 范围是从1024到65535。

| 如果程序员开发的程序没有设置端口号，操作系统会在动态端口号这个范围内随机生成一个给开发的应用程序使用。
| 当运行一个程序默认会有一个端口号，当这个程序退出时，所占用的这个端口号就会被释放。

* 小结

::

	端口的作用就是给运行的应用程序提供传输数据的通道。
	端口号的作用是用来区分和管理不同端口的，通过端口号能找到唯一个的一个端口。
	端口号可以分为两类： 知名端口号 和 动态端口号
	    知名端口号的范围是0到1023
	    动态端口号的范围是1024到65535

TCP 的介绍
**********************************************************************************

* 网络应用程序之间的通信流程

之前我们学习了 IP 地址和端口号，通过 IP 地址能够找到对应的设备，然后再通过端口号找到对应的端口，再通过端口把数据传输给应用程序，这里要注意，数据不能随便发送，在发送之前还需要选择一个对应的传输协议，保证程序之间按照指定的传输规则进行数据的通信， 而这个传输协议就是我们今天学习的 TCP。

* TCP 的概念

TCP 的英文全拼(Transmission Control Protocol)简称传输控制协议，它是一种面向连接的、可靠的、基于字节流的传输层通信协议。

面向连接的效果图:

|image7|

TCP 通信步骤:

::

	创建连接
	传输数据
	关闭连接

.. tip::
	
	TCP 通信模型相当于生活中的’打电话‘，在通信开始之前，一定要先建立好连接，才能发送数据，通信结束要关闭连接。

|image8|

* TCP 的特点

::

	面向连接
		通信双方必须先建立好连接才能进行数据的传输，数据传输完成后，双方必须断开此连接，以释放系统资源。

	可靠传输
	    TCP 采用发送应答机制
	    超时重传
	    错误校验
	    流量控制和阻塞管理

* 总结

TCP 是一个稳定、可靠的传输协议，常用于对数据进行准确无误的传输，比如: 文件下载，浏览器上网。

socket 的介绍
**********************************************************************************

* 问题思考

到目前为止我们学习了 ip 地址和端口号还有 tcp 传输协议，为了保证数据的完整性和可靠性我们使用 tcp 传输协议进行数据的传输，为了能够找到对应设备我们需要使用 ip 地址，为了区别某个端口的应用程序接收数据我们需要使用端口号，那么通信数据是如何完成传输的呢？ 

使用 socket 来完成

* socket 的概念

socket (简称 套接字) 是进程之间通信一个工具，好比现实生活中的插座，所有的家用电器要想工作都是基于插座进行，进程之间想要进行网络通信需要基于这个 socket。

socket 效果图:

|image9|

* socket 的作用

负责进程之间的网络数据传输，好比数据的搬运工。

* socket 使用场景

不夸张的说，只要跟网络相关的应用程序或者软件都使用到了 socket 。

* 小结

进程之间网络数据的传输可以通过 socket 来完成， socket 就是进程间网络数据通信的工具。

TCP 网络应用程序开发流程
**********************************************************************************

* TCP 网络应用程序开发流程的介绍

TCP 网络应用程序开发分为:

::

	TCP 客户端程序开发
	TCP 服务端程序开发

.. tip::

	客户端程序是指运行在用户设备上的程序 服务端程序是指运行在服务器设备上的程序，专门为客户端提供数据服务。

* TCP 客户端程序开发流程的介绍

|image10|

::

	创建客户端套接字对象
	和服务端套接字建立连接
	发送数据
	接收数据
	关闭客户端套接字

* TCP 服务端程序开发流程的介绍

|image11|

::

	创建服务端端套接字对象
	绑定端口号
	设置监听
	等待接受客户端的连接请求
	接收数据
	发送数据
	关闭套接字

* 小结

::

	TCP 网络应用程序开发分为客户端程序开发和服务端程序开发。
	主动发起建立连接请求的是客户端程序
	等待接受连接请求的是服务端程序

TCP 客户端程序开发
**********************************************************************************

* 开发 TCP 客户端程序开发步骤回顾

::

	创建客户端套接字对象
	和服务端套接字建立连接
	发送数据
	接收数据
	关闭客户端套接字

* socket 类的介绍

导入 socket 模块 import socket, 创建客户端 socket 对象 socket.socket(AddressFamily, Type) 

参数说明:

::

	AddressFamily 表示IP地址类型, 分为TPv4和IPv6
	Type 表示传输协议类型

方法说明:

::

	connect((host, port)) 表示和服务端套接字建立连接, host是服务器ip地址，port是应用程序的端口号
	send(data) 表示发送数据，data是二进制数据
	recv(buffersize) 表示接收数据, buffersize是每次接收数据的长度

* TCP 客户端程序开发示例代码

.. code-block:: python
    :linenos:

	import socket

	if __name__ == '__main__':
	    # 创建tcp客户端套接字
	    # 1. AF_INET：表示ipv4
	    # 2. SOCK_STREAM: tcp传输协议
	    tcp_client_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
	    # 和服务端应用程序建立连接
	    tcp_client_socket.connect(("192.168.131.62", 8080))
	    # 代码执行到此，说明连接建立成功
	    # 准备发送的数据
	    send_data = "你好服务端，我是客户端小黑!".encode("gbk")
	    # 发送数据
	    tcp_client_socket.send(send_data)
	    # 接收数据, 这次接收的数据最大字节数是1024
	    recv_data = tcp_client_socket.recv(1024)
	    # 返回的直接是服务端程序发送的二进制数据
	    print(recv_data)
	    # 对数据进行解码
	    recv_content = recv_data.decode("gbk")
	    print("接收服务端的数据为:", recv_content)
	    # 关闭套接字
	    tcp_client_socket.close()

	执行结果:

	b'hello'
	接收服务端的数据为: hello

.. tip::

	| str.encode(编码格式) 表示把字符串编码成为二进制
	| data.decode(编码格式) 表示把二进制解码成为字符串

	可以使用网络调试助手充当服务端程序。

* 小结

::

	导入socket模块
	创建TCP套接字‘socket’
	    参数1: ‘AF_INET’, 表示IPv4地址类型
	    参数2: ‘SOCK_STREAM’, 表示TCP传输协议类型
	发送数据‘send’
	    参数1: 要发送的二进制数据， 注意: 字符串需要使用encode()方法进行编码
	接收数据‘recv’
	    参数1: 表示每次接收数据的大小，单位是字节
	关闭套接字‘socket’表示通信完成

TCP服务端程序开发
**********************************************************************************

* 开发 TCP 服务端程序开发步骤回顾

::

	创建服务端端套接字对象
	绑定端口号
	设置监听
	等待接受客户端的连接请求
	接收数据
	发送数据
	关闭套接字

* socket 类的介绍

导入 socket 模块 ``import socket``

创建服务端 socket 对象 ``socket.socket(AddressFamily, Type)``

参数说明:

::

	AddressFamily 表示IP地址类型, 分为TPv4和IPv6
	Type 表示传输协议类型

方法说明:

::

	bind((host, port)) 表示绑定端口号, host 是 ip 地址，port 是端口号，ip 地址一般不指定，表示本机的任何一个ip地址都可以。
	listen (backlog) 表示设置监听，backlog参数表示最大等待建立连接的个数。
	accept() 表示等待接受客户端的连接请求
	send(data) 表示发送数据，data 是二进制数据
	recv(buffersize) 表示接收数据, buffersize 是每次接收数据的长度

* TCP 服务端程序开发示例代码

.. code-block:: python
    :linenos:

	import socket

	if __name__ == '__main__':
	    # 创建tcp服务端套接字
	    tcp_server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
	    # 设置端口号复用，让程序退出端口号立即释放
	    tcp_server_socket.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, True) 
	    # 给程序绑定端口号
	    tcp_server_socket.bind(("", 8989))
	    # 设置监听
	    # 128:最大等待建立连接的个数， 提示： 目前是单任务的服务端，同一时刻只能服务与一个客户端，后续使用多任务能够让服务端同时服务与多个客户端，
	    # 不需要让客户端进行等待建立连接
	    # listen后的这个套接字只负责接收客户端连接请求，不能收发消息，收发消息使用返回的这个新套接字来完成
	    tcp_server_socket.listen(128)
	    # 等待客户端建立连接的请求, 只有客户端和服务端建立连接成功代码才会解阻塞，代码才能继续往下执行
	    # 1. 专门和客户端通信的套接字： service_client_socket
	    # 2. 客户端的ip地址和端口号： ip_port
	    service_client_socket, ip_port = tcp_server_socket.accept()
	    # 代码执行到此说明连接建立成功
	    print("客户端的ip地址和端口号:", ip_port)
	    # 接收客户端发送的数据, 这次接收数据的最大字节数是1024
	    recv_data = service_client_socket.recv(1024)
	    # 获取数据的长度
	    recv_data_length = len(recv_data)
	    print("接收数据的长度为:", recv_data_length)
	    # 对二进制数据进行解码
	    recv_content = recv_data.decode("gbk")
	    print("接收客户端的数据为:", recv_content)
	    # 准备发送的数据
	    send_data = "ok, 问题正在处理中...".encode("gbk")
	    # 发送数据给客户端
	    service_client_socket.send(send_data)
	    # 关闭服务与客户端的套接字， 终止和客户端通信的服务
	    service_client_socket.close()
	    # 关闭服务端的套接字, 终止和客户端提供建立连接请求的服务
	    tcp_server_socket.close()

	执行结果:

	客户端的ip地址和端口号: ('172.16.47.209', 52472)
	接收数据的长度为: 5
	接收客户端的数据为: hello

* 小结

::

	导入socket模块
	创建TCP套接字‘socket’
	    参数1: ‘AF_INET’, 表示IPv4地址类型
	    参数2: ‘SOCK_STREAM’, 表示TCP传输协议类型
	绑定端口号‘bind’
	    参数: 元组, 比如:(ip地址, 端口号)
	设置监听‘listen’
	    参数: 最大等待建立连接的个数
	等待接受客户端的连接请求‘accept’
	发送数据‘send’
	    参数: 要发送的二进制数据， 注意: 字符串需要使用encode()方法进行编码
	接收数据‘recv’
	    参数: 表示每次接收数据的大小，单位是字节，注意: 解码成字符串使用decode()方法
	关闭套接字‘socket’表示通信完成

TCP网络应用程序的注意点
**********************************************************************************

::

	当 TCP 客户端程序想要和 TCP 服务端程序进行通信的时候必须要先建立连接
	TCP 客户端程序一般不需要绑定端口号，因为客户端是主动发起建立连接的。
	TCP 服务端程序必须绑定端口号，否则客户端找不到这个 TCP 服务端程序。
	listen 后的套接字是被动套接字，只负责接收新的客户端的连接请求，不能收发消息。
	当 TCP 客户端程序和 TCP 服务端程序连接成功后， TCP 服务器端程序会产生一个新的套接字，收发客户端消息使用该套接字。
	关闭 accept 返回的套接字意味着和这个客户端已经通信完毕。
	关闭 listen 后的套接字意味着服务端的套接字关闭了，会导致新的客户端不能连接服务端，但是之前已经接成功的客户端还能正常通信。
	当客户端的套接字调用 close 后，服务器端的 recv 会解阻塞，返回的数据长度为0，服务端可以通过返回数据的长度来判断客户端是否已经下线，反之服务端关闭套接字，客户端的 recv 也会解阻塞，返回的数据长度也为0。

案例-多任务版TCP服务端程序开发
**********************************************************************************

* 需求

| 目前我们开发的TCP服务端程序只能服务于一个客户端，如何开发一个多任务版的TCP服务端程序能够服务于多个客户端呢?
| 完成多任务，可以使用线程，比进程更加节省内存资源。

* 具体实现步骤

::

	编写一个TCP服务端程序，循环等待接受客户端的连接请求
	当客户端和服务端建立连接成功，创建子线程，使用子线程专门处理客户端的请求，防止主线程阻塞
	把创建的子线程设置成为守护主线程，防止主线程无法退出。

* 多任务版TCP服务端程序的示例代码:

.. code-block:: python
    :linenos:

	import socket
	import threading

	# 处理客户端的请求操作
	def handle_client_request(service_client_socket, ip_port):
	    # 循环接收客户端发送的数据
	    while True:
	        # 接收客户端发送的数据
	        recv_data = service_client_socket.recv(1024)
	        # 容器类型判断是否有数据可以直接使用if语句进行判断，如果容器类型里面有数据表示条件成立，否则条件失败
	        # 容器类型: 列表、字典、元组、字符串、set、range、二进制数据
	        if recv_data:
	            print(recv_data.decode("gbk"), ip_port)
	            # 回复
	            service_client_socket.send("ok，问题正在处理中...".encode("gbk"))

	        else:
	            print("客户端下线了:", ip_port)
	            break
	    # 终止和客户端进行通信
	    service_client_socket.close()

	if __name__ == '__main__':
	    # 创建tcp服务端套接字
	    tcp_server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
	    # 设置端口号复用，让程序退出端口号立即释放
	    tcp_server_socket.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, True)
	    # 绑定端口号
	    tcp_server_socket.bind(("", 9090))
	    # 设置监听, listen后的套接字是被动套接字，只负责接收客户端的连接请求
	    tcp_server_socket.listen(128)
	    # 循环等待接收客户端的连接请求
	    while True:
	        # 等待接收客户端的连接请求
	        service_client_socket, ip_port = tcp_server_socket.accept()
	        print("客户端连接成功:", ip_port)
	        # 当客户端和服务端建立连接成功以后，需要创建一个子线程，不同子线程负责接收不同客户端的消息
	        sub_thread = threading.Thread(target=handle_client_request, args=(service_client_socket, ip_port))
	        # 设置守护主线程
	        sub_thread.setDaemon(True)
	        # 启动子线程
	        sub_thread.start()

	    # tcp服务端套接字可以不需要关闭，因为服务端程序需要一直运行
	    # tcp_server_socket.close()

	执行结果:

	客户端连接成功: ('172.16.47.209', 51528)
	客户端连接成功: ('172.16.47.209', 51714)
	hello1 ('172.16.47.209', 51528)
	hello2 ('172.16.47.209', 51714)

* 小结
	
编写一个TCP服务端程序，循环等待接受客户端的连接请求

::

	while True:
	     service_client_socket, ip_port = tcp_server_socket.accept()

当客户端和服务端建立连接成功，创建子线程，使用子线程专门处理客户端的请求，防止主线程阻塞

::

	 while True:
	     service_client_socket, ip_port = tcp_server_socket.accept() 
	     sub_thread = threading.Thread(target=handle_client_request, args=(service_client_socket, ip_port))
	     sub_thread.start()

把创建的子线程设置成为守护主线程，防止主线程无法退出。

::

	 while True:
	     service_client_socket, ip_port = tcp_server_socket.accept() 
	     sub_thread = threading.Thread(target=handle_client_request, args=(service_client_socket, ip_port))
	     sub_thread.setDaemon(True) 
	     sub_thread.start()

socket 之 send 和 recv 原理剖析
**********************************************************************************

* 认识TCP socket的发送和接收缓冲区

当创建一个TCP socket对象的时候会有一个发送缓冲区和一个接收缓冲区，这个发送和接收缓冲区指的就是内存中的一片空间。

* send原理剖析

send是不是直接把数据发给服务端?

不是，要想发数据，必须得通过网卡发送数据，应用程序是无法直接通过网卡发送数据的，它需要调用操作系统接口，也就是说，应用程序把发送的数据先写入到发送缓冲区(内存中的一片空间)，再由操作系统控制网卡把发送缓冲区的数据发送给服务端网卡 。

* recv原理剖析

recv是不是直接从客户端接收数据?

不是，应用软件是无法直接通过网卡接收数据的，它需要调用操作系统接口，由操作系统通过网卡接收数据，把接收的数据写入到接收缓冲区(内存中的一片空间），应用程序再从接收缓存区获取客户端发送的数据。

* send和recv原理剖析图

|image12|

.. note::

	| 发送数据是发送到发送缓冲区
	| 接收数据是从接收缓冲区 获取

* 小结

不管是recv还是send都不是直接接收到对方的数据和发送数据到对方，发送数据会写入到发送缓冲区，接收数据是从接收缓冲区来读取，发送数据和接收数据最终是由操作系统控制网卡来完成。

HTTP 协议和静态 Web 服务器
##################################################################################

HTTP 协议
**********************************************************************************

* HTTP 协议的介绍

| HTTP 协议的全称是(HyperText Transfer Protocol)，翻译过来就是超文本传输协议。
| 超文本是超级文本的缩写，是指超越文本限制或者超链接，比如:图片、音乐、视频、超链接等等都属于超文本。

| HTTP 协议的制作者是蒂姆·伯纳斯-李，1991年设计出来的，HTTP 协议设计之前目的是传输网页数据的，现在允许传输任意类型的数据。
| 传输 HTTP 协议格式的数据是基于 TCP 传输协议的，发送数据之前需要先建立连接。

* HTTP 协议的作用

它规定了浏览器和 Web 服务器通信数据的格式，也就是说浏览器和web服务器通信需要使用http协议。

* 浏览器访问web服务器的通信过程

通信效果图:

|image13|

* 小结

::

	HTTP协议是一个超文本传输协议
	HTTP协议是一个基于TCP传输协议传输数据的
	HTTP协议规定了浏览器和 Web 服务器通信数据的格式

URL
**********************************************************************************

* URL的概念

URL的英文全拼是(Uniform Resoure Locator),表达的意思是统一资源定位符，通俗理解就是网络资源地址，也就是我们常说的网址。

* URL 的组成

URL的样子: https://news.163.com/18/1122/10/E178J2O4000189FH.html

URL的组成部分:

::

	协议部分: https://、http://、ftp://
	域名部分: news.163.com
	资源路径部分: /18/1122/10/E178J2O4000189FH.html

域名: 域名就是IP地址的别名，它是用点进行分割使用英文字母和数字组成的名字，使用域名目的就是方便的记住某台主机IP地址。

URL的扩展:

https://news.163.com/hello.html?page=1&count=10  查询参数部分: ?page=1&count=10

参数说明: ? 后面的 page 表示第一个参数，后面的参数都使用 & 进行连接

* 小结

::

	URL就是网络资源的地址，简称网址，通过URL能够找到网络中对应的资源数据。
	URL组成部分
	    协议部分
	    域名部分
	    资源路径部分
	    查询参数部分 [可选]

查看 HTTP 协议的通信过程
**********************************************************************************

* 谷歌浏览器开发者工具的使用

首先需要安装Google Chrome浏览器，然后Windows和Linux平台按F12调出开发者工具, mac OS选择 视图 -> 开发者 -> 开发者工具或者直接使用 alt+command+i 这个快捷键，还有一个多平台通用的操作就是在网页右击选择检查。

开发者工具的效果图:

|image14|

开发者工具的标签选项说明:

::

	元素（Elements）：用于查看或修改HTML标签
	控制台（Console）：执行js代码
	源代码（Sources）：查看静态资源文件，断点调试JS代码
	网络（Network）：查看http协议的通信过程

开发者工具使用效果图:

|image15|

开发者工具的使用说明:

::

	点击Network标签选项
	在浏览器的地址栏输入百度的网址，就能看到请求百度首页的http的通信过程
	这里的每项记录都是请求+响应的一次过程

* 查看HTTP协议的通信过程

|image16|

* 小结

::

	谷歌浏览器的开发者工具是查看http协议的通信过程利器，通过Network标签选项可以查看每一次的请求和响应的通信过程，调出开发者工具的通用方法是在网页右击选择检查。
	开发者工具的Headers选项总共有三部分组成:
	    General: 主要信息
	    Response Headers: 响应头
	    Request Headers: 请求头
	Response选项是查看响应体信息的

HTTP 请求报文
**********************************************************************************

* HTTP 请求报文介绍

HTTP最常见的请求报文有两种:

::

	GET 方式的请求报文
	POST 方式的请求报文

.. tip::

	| GET: 获取web服务器数据
	| POST: 向web服务器提交数据

* HTTP GET 请求报文分析

HTTP GET 请求报文效果图:

|image17|

GET 请求报文说明:

::

	---- 请求行 ----
	GET / HTTP/1.1  # GET请求方式 请求资源路径 HTTP协议版本
	---- 请求头 -----
	Host: www.itcast.cn  # 服务器的主机地址和端口号,默认是80
	Connection: keep-alive # 和服务端保持长连接
	Upgrade-Insecure-Requests: 1 # 让浏览器升级不安全请求，使用https请求
	User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_4) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36  # 用户代理，也就是客户端的名称
	Accept:text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8 # 可接受的数据类型
	Accept-Encoding: gzip, deflate # 可接受的压缩格式
	Accept-Language: zh-CN,zh;q=0.9 #可接受的语言
	Cookie: pgv_pvi=1246921728; # 登录用户的身份标识

	---- 空行 ----

GET 请求原始报文说明:

::

	GET / HTTP/1.1\r\n
	Host: www.itcast.cn\r\n  
	Connection: keep-alive\r\n
	Upgrade-Insecure-Requests: 1\r\n
	User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_4) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36\r\n
	Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8\r\n
	Accept-Encoding: gzip, deflate\r\n
	Accept-Language: zh-CN,zh;q=0.9\r\n
	Cookie: pgv_pvi=1246921728; \r\n
	\r\n  (请求头信息后面还有一个单独的’\r\n’不能省略)

.. note::

	每项数据之间使用:\r\n

* HTTP POST 请求报文分析

HTTP POST 请求报文效果图:

|image18|

请求体效果图:

|image19|

POST 请求报文说明:

::

	---- 请求行 ----
	POST /xmweb?host=mail.itcast.cn&_t=1542884567319 HTTP/1.1 # POST请求方式 请求资源路径 HTTP协议版本
	---- 请求头 ----
	Host: mail.itcast.cn # 服务器的主机地址和端口号,默认是80
	Connection: keep-alive # 和服务端保持长连接
	Content-Type: application/x-www-form-urlencoded  # 告诉服务端请求的数据类型
	User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_4) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36 # 客户端的名称
	---- 空行 ----
	---- 请求体 ----
	username=hello&pass=hello # 请求参数

POST 请求原始报文说明:

::

	POST /xmweb?host=mail.itcast.cn&_t=1542884567319 HTTP/1.1\r\n
	Host: mail.itcast.cn\r\n
	Connection: keep-alive\r\n
	Content-Type: application/x-www-form-urlencoded\r\n
	User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_4) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36\r\n
	\r\n(请求头信息后面还有一个单独的’\r\n’不能省略)
	username=hello&pass=hello

.. note::

	每项数据之间使用:\r\n

* 小结

::

	一个HTTP请求报文可以由请求行、请求头、空行和请求体4个部分组成。
	请求行是由三部分组成:
	    请求方式
	    请求资源路径
	    HTTP协议版本
	GET方式的请求报文没有请求体，只有请求行、请求头、空行组成。
	POST方式的请求报文可以有请求行、请求头、空行、请求体四部分组成，注意:POST方式可以允许没有请求体，但是这种格式很少见。

GET 和 POST 请求对比效果图:

|image20|

HTTP 响应报文
**********************************************************************************

* HTTP响应报文分析

|image21|

|image22|

响应报文说明:

::

	--- 响应行/状态行 ---
	HTTP/1.1 200 OK # HTTP协议版本 状态码 状态描述
	--- 响应头 ---
	Server: Tengine # 服务器名称
	Content-Type: text/html; charset=UTF-8 # 内容类型
	Transfer-Encoding: chunked # 发送给客户端内容不确定内容长度，发送结束的标记是0\r\n, Content-Length表示服务端确定发送给客户端的内容大小，但是二者只能用其一。
	Connection: keep-alive # 和客户端保持长连接
	Date: Fri, 23 Nov 2018 02:01:05 GMT # 服务端的响应时间
	--- 空行 ---
	--- 响应体 ---
	<!DOCTYPE html><html lang=“en”> …</html> # 响应给客户端的数据

原始响应报文说明:

::

	HTTP/1.1 200 OK\r\n
	Server: Tengine\r\n
	Content-Type: text/html; charset=UTF-8\r\n
	Transfer-Encoding: chunked\r\n
	Connection: keep-alive\r\n
	Date: Fri, 23 Nov 2018 02:01:05 GMT\r\n
	\r\n(响应头信息后面还有一个单独的’\r\n’不能省略)
	<!DOCTYPE html><html lang=“en”> …</html>

.. tip::

	每项数据之间使用:\r\n

* HTTP 状态码介绍

HTTP 状态码是用于表示web服务器响应状态的3位数字代码。

::

	状态码 	说明
	200 	请求成功
	307 	重定向
	400 	错误的请求，请求地址或者参数有误
	404 	请求资源在服务器不存在
	500 	服务器内部源代码出现错误

* 小结

|image23|

::

	一个HTTP响应报文是由响应行、响应头、空行和响应体4个部分组成。 HTTP 响应报文
	响应行是由三部分组成：HTTP协议版本 状态码 状态描述，最常见的状态码是200

搭建 Python 自带静态 Web 服务器
**********************************************************************************

* 静态Web服务器是什么？

可以为发出请求的浏览器提供静态文档的程序。

平时我们浏览百度新闻数据的时候，每天的新闻数据都会发生变化，那访问的这个页面就是动态的，而我们开发的是静态的，页面的数据不会发生变化。

* 如何搭建Python自带的静态Web服务器

搭建Python自带的静态Web服务器使用 python3 -m http.server 端口号, 效果如下:

::

	➜ .xxx.note git:(master) ✗ python3  -m http.server 9000
	Serving HTTP on 0.0.0.0 port 9000 (http://0.0.0.0:9000/) ...
	127.0.0.1 - - [15/Nov/2020 17:03:09] "GET / HTTP/1.1" 200 -

-m选项说明:

-m表示运行包里面的模块，执行这个命令的时候，需要进入你自己指定静态文件的目录，然后通过浏览器就能访问对应的 html文件了，这样一个静态的web服务器就搭建好了。

* 查看浏览器和搭建的静态Web服务器的通信过程

查看http的通信过程,效果图如下:

|image24|

* 小结

::

	静态Web服务器是为发出请求的浏览器提供静态文档的程序，
	搭建Python自带的Web服务器使用python3 –m http.server 端口号 这个命令即可，端口号不指定默认是8000

静态 Web 服务器-返回固定页面数据
**********************************************************************************

* 开发自己的静态Web服务器

实现步骤:

::

	编写一个TCP服务端程序
	获取浏览器发送的http请求报文数据
	读取固定页面数据，把页面数据组装成HTTP响应报文数据发送给浏览器。
	HTTP响应报文数据发送完成以后，关闭服务于客户端的套接字。

* 静态 Web 服务器-返回固定页面数据的示例代码

.. code-block:: python
    :linenos:

	import socket

	if __name__ == '__main__':
	    # 创建tcp服务端套接字
	    tcp_server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
	    # 设置端口号复用, 程序退出端口立即释放
	    tcp_server_socket.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, True)
	    # 绑定端口号
	    tcp_server_socket.bind(("", 9000))
	    # 设置监听
	    tcp_server_socket.listen(128)
	    while True:
	        # 等待接受客户端的连接请求
	        new_socket, ip_port = tcp_server_socket.accept()
	        # 代码执行到此，说明连接建立成功
	        recv_client_data = new_socket.recv(4096)
	        # 对二进制数据进行解码
	        recv_client_content = recv_client_data.decode("utf-8")
	        print(recv_client_content)

	        with open("static/index.html", "rb") as file:
	            # 读取文件数据
	            file_data = file.read()

	        # 响应行
	        response_line = "HTTP/1.1 200 OK\r\n"
	        # 响应头
	        response_header = "Server: PWS1.0\r\n"

	        # 响应体
	        response_body = file_data

	        # 拼接响应报文
	        response_data = (response_line + response_header + "\r\n").encode("utf-8") + response_body
	        # 发送数据
	        new_socket.send(response_data)

	        # 关闭服务与客户端的套接字
	        new_socket.close()

* 小结

::

	编写一个TCP服务端程序

	tcp_server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
	# 循环接受客户端的连接请求
	while True:
	    conn_socket, ip_port = tcp_server_socket.accept()

	获取浏览器发送的http请求报文数据

	client_request_data = conn_socket.recv(4096)

	读取固定页面数据，把页面数据组装成HTTP响应报文数据发送给浏览器。

	response_data = (response_line + response_header + "\r\n").encode("utf-8") + response_body
	conn_socket.send(response_data)

	HTTP响应报文数据发送完成以后，关闭服务于客户端的套接字。

	conn_socket.close()

静态 Web 服务器-返回指定页面数据
**********************************************************************************

* 静态 Web 服务器的问题

|image25|

目前的Web服务器，不管用户访问什么页面，返回的都是固定页面的数据，接下来需要根据用户的请求返回指定页面的数据

返回指定页面数据的实现步骤:

::

	获取用户请求资源的路径
	根据请求资源的路径，读取指定文件的数据
	组装指定文件数据的响应报文，发送给浏览器
	判断请求的文件在服务端不存在，组装404状态的响应报文，发送给浏览器

* 静态 Web 服务器-返回指定页面数据的示例代码

.. code-block:: python
    :linenos:

	import socket

	def main():
	    # 创建tcp服务端套接字
	    tcp_server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
	    # 设置端口号复用, 程序退出端口立即释放
	    tcp_server_socket.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, True)
	    # 绑定端口号
	    tcp_server_socket.bind(("", 9000))
	    # 设置监听
	    tcp_server_socket.listen(128)
	    while True:
	        # 等待接受客户端的连接请求
	        new_socket, ip_port = tcp_server_socket.accept()
	        # 代码执行到此，说明连接建立成功
	        recv_client_data = new_socket.recv(4096)
	        if len(recv_client_data) == 0:
	            print("关闭浏览器了")
	            new_socket.close()
	            return

	        # 对二进制数据进行解码
	        recv_client_content = recv_client_data.decode("utf-8")
	        print(recv_client_content)
	        # 根据指定字符串进行分割， 最大分割次数指定2
	        request_list = recv_client_content.split(" ", maxsplit=2)

	        # 获取请求资源路径
	        request_path = request_list[1]
	        print(request_path)

	        # 判断请求的是否是根目录，如果条件成立，指定首页数据返回
	        if request_path == "/":
	            request_path = "/index.html"

	        try:
	            # 动态打开指定文件
	            with open("static" + request_path, "rb") as file:
	                # 读取文件数据
	                file_data = file.read()
	        except Exception as e:
	            # 请求资源不存在，返回404数据
	            # 响应行
	            response_line = "HTTP/1.1 404 Not Found\r\n"
	            # 响应头
	            response_header = "Server: PWS1.0\r\n"
	            with open("static/error.html", "rb") as file:
	                file_data = file.read()
	            # 响应体
	            response_body = file_data

	            # 拼接响应报文
	            response_data = (response_line + response_header + "\r\n").encode("utf-8") + response_body
	            # 发送数据
	            new_socket.send(response_data)
	        else:
	            # 响应行
	            response_line = "HTTP/1.1 200 OK\r\n"
	            # 响应头
	            response_header = "Server: PWS1.0\r\n"

	            # 响应体
	            response_body = file_data

	            # 拼接响应报文
	            response_data = (response_line + response_header + "\r\n").encode("utf-8") + response_body
	            # 发送数据
	            new_socket.send(response_data)
	        finally:
	            # 关闭服务与客户端的套接字
	            new_socket.close()

	if __name__ == '__main__':
	    main()

* 小结

::

	获取用户请求资源的路径

	 request_list = client_request_conent.split(” ”,  maxsplit=2)
	 request_path = request_list[1]

	根据请求资源的路径，读取请求指定文件的数据

	 with open("static" + request_path, "rb") as file:
	 file_data = file.read()

	组装指定文件数据的响应报文，发送给浏览器

	 response_data = (response_line + response_header + "\r\n").encode("utf-8") + response_body
	 conn_socket.send(response_data)

	判断请求的文件在服务端不存在，组装404状态的响应报文，发送给浏览器

	 try:
	     # 打开指定文件,代码省略...
	 except Exception as e:
	     conn_socket.send(404响应报文数据)

静态 Web 服务器-多任务版
**********************************************************************************

* 静态Web服务器的问题

目前的Web服务器，不能支持多用户同时访问，只能一个一个的处理客户端的请求，那么如何开发多任务版的web服务器同时处理 多个客户端的请求?

可以使用多线程，比进程更加节省内存资源。多任务版web服务器程序的实现步骤:

::

	当客户端和服务端建立连接成功，创建子线程，使用子线程专门处理客户端的请求，防止主线程阻塞。
	把创建的子线程设置成为守护主线程，防止主线程无法退出。

* 静态Web服务器-多任务版的示例代码

.. code-block:: python
    :linenos:

	import socket
	import threading

	# 处理客户端的请求
	def handle_client_request(new_socket):
	    # 代码执行到此，说明连接建立成功
	    recv_client_data = new_socket.recv(4096)
	    if len(recv_client_data) == 0:
	        print("关闭浏览器了")
	        new_socket.close()
	        return

	    # 对二进制数据进行解码
	    recv_client_content = recv_client_data.decode("utf-8")
	    print(recv_client_content)
	    # 根据指定字符串进行分割， 最大分割次数指定2
	    request_list = recv_client_content.split(" ", maxsplit=2)

	    # 获取请求资源路径
	    request_path = request_list[1]
	    print(request_path)

	    # 判断请求的是否是根目录，如果条件成立，指定首页数据返回
	    if request_path == "/":
	        request_path = "/index.html"

	    try:
	        # 动态打开指定文件
	        with open("static" + request_path, "rb") as file:
	            # 读取文件数据
	            file_data = file.read()
	    except Exception as e:
	        # 请求资源不存在，返回404数据
	        # 响应行
	        response_line = "HTTP/1.1 404 Not Found\r\n"
	        # 响应头
	        response_header = "Server: PWS1.0\r\n"
	        with open("static/error.html", "rb") as file:
	            file_data = file.read()
	        # 响应体
	        response_body = file_data

	        # 拼接响应报文
	        response_data = (response_line + response_header + "\r\n").encode("utf-8") + response_body
	        # 发送数据
	        new_socket.send(response_data)
	    else:
	        # 响应行
	        response_line = "HTTP/1.1 200 OK\r\n"
	        # 响应头
	        response_header = "Server: PWS1.0\r\n"

	        # 响应体
	        response_body = file_data

	        # 拼接响应报文
	        response_data = (response_line + response_header + "\r\n").encode("utf-8") + response_body
	        # 发送数据
	        new_socket.send(response_data)
	    finally:
	        # 关闭服务与客户端的套接字
	        new_socket.close()

	# 程序入口函数
	def main():
	    # 创建tcp服务端套接字
	    tcp_server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
	    # 设置端口号复用, 程序退出端口立即释放
	    tcp_server_socket.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, True)
	    # 绑定端口号
	    tcp_server_socket.bind(("", 9000))
	    # 设置监听
	    tcp_server_socket.listen(128)

	    while True:
	        # 等待接受客户端的连接请求
	        new_socket, ip_port = tcp_server_socket.accept()
	        print(ip_port)
	        # 当客户端和服务器建立连接程，创建子线程
	        sub_thread = threading.Thread(target=handle_client_request, args=(new_socket,))
	        # 设置守护主线程
	        sub_thread.setDaemon(True)
	        # 启动子线程执行对应的任务
	        sub_thread.start()


	if __name__ == '__main__':
	    main()

* 小结

::

	当客户端和服务端建立连接成功，创建子线程，使用子线程专门处理客户端的请求，防止主线程阻塞。

	 while True:
	     conn_socket, ip_port = tcp_server_socket.accept()
	     # 开辟子线程并执行对应的任务
	     sub_thread = threading.Thread(target=handle_client_request, args=(conn_socket,))

	把创建的子线程设置成为守护主线程，防止主线程无法退出。

	 # 开辟子线程并执行对应的任务
	 sub_thread = threading.Thread(target=handle_client_request, args=(conn_socket,))
	 sub_thread.setDaemon(True) # 设置守护主线程
	 sub_thread.start()

静态 Web 服务器-面向对象开发
**********************************************************************************

* 以面向对象的方式开发静态Web服务器

::

	把提供服务的Web服务器抽象成一个类(HTTPWebServer)
	提供Web服务器的初始化方法，在初始化方法里面创建socket对象
	提供一个开启Web服务器的方法，让Web服务器处理客户端请求操作。

* 静态Web服务器-面向对象开发的示例代码

.. code-block:: python
    :linenos:

	import socket
	import threading

	# 定义web服务器类
	class HttpWebServer(object):
	    def __init__(self):
	        # 创建tcp服务端套接字
	        tcp_server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
	        # 设置端口号复用, 程序退出端口立即释放
	        tcp_server_socket.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, True)
	        # 绑定端口号
	        tcp_server_socket.bind(("", 9000))
	        # 设置监听
	        tcp_server_socket.listen(128)
	        # 保存创建成功的服务器套接字
	        self.tcp_server_socket = tcp_server_socket

	    # 处理客户端的请求
	    @staticmethod
	    def handle_client_request(new_socket):
	        # 代码执行到此，说明连接建立成功
	        recv_client_data = new_socket.recv(4096)
	        if len(recv_client_data) == 0:
	            print("关闭浏览器了")
	            new_socket.close()
	            return

	        # 对二进制数据进行解码
	        recv_client_content = recv_client_data.decode("utf-8")
	        print(recv_client_content)
	        # 根据指定字符串进行分割， 最大分割次数指定2
	        request_list = recv_client_content.split(" ", maxsplit=2)

	        # 获取请求资源路径
	        request_path = request_list[1]
	        print(request_path)

	        # 判断请求的是否是根目录，如果条件成立，指定首页数据返回
	        if request_path == "/":
	            request_path = "/index.html"

	        try:
	            # 动态打开指定文件
	            with open("static" + request_path, "rb") as file:
	                # 读取文件数据
	                file_data = file.read()
	        except Exception as e:
	            # 请求资源不存在，返回404数据
	            # 响应行
	            response_line = "HTTP/1.1 404 Not Found\r\n"
	            # 响应头
	            response_header = "Server: PWS1.0\r\n"
	            with open("static/error.html", "rb") as file:
	                file_data = file.read()
	            # 响应体
	            response_body = file_data

	            # 拼接响应报文
	            response_data = (response_line + response_header + "\r\n").encode("utf-8") + response_body
	            # 发送数据
	            new_socket.send(response_data)
	        else:
	            # 响应行
	            response_line = "HTTP/1.1 200 OK\r\n"
	            # 响应头
	            response_header = "Server: PWS1.0\r\n"

	            # 响应体
	            response_body = file_data

	            # 拼接响应报文
	            response_data = (response_line + response_header + "\r\n").encode("utf-8") + response_body
	            # 发送数据
	            new_socket.send(response_data)
	        finally:
	            # 关闭服务与客户端的套接字
	            new_socket.close()

	    # 启动web服务器进行工作
	    def start(self):
	        while True:
	            # 等待接受客户端的连接请求
	            new_socket, ip_port = self.tcp_server_socket.accept()
	            # 当客户端和服务器建立连接程，创建子线程
	            sub_thread = threading.Thread(target=self.handle_client_request, args=(new_socket,))
	            # 设置守护主线程
	            sub_thread.setDaemon(True)
	            # 启动子线程执行对应的任务
	            sub_thread.start()

	# 程序入口函数
	def main():
	    # 创建web服务器对象
	    web_server = HttpWebServer()
	    # 启动web服务器进行工作
	    web_server.start()

	if __name__ == '__main__':
	    main()

* 小结

::

	把提供服务的Web服务器抽象成一个类(HTTPWebServer)

	 class HttpWebServer(object):

	提供Web服务器的初始化方法，在初始化方法里面创建socket对象

	 def __init__(self):
	 # 初始化服务端套接字，设置监听，代码省略..

	提供一个开启Web服务器的方法，让Web服务器处理客户端请求操作。

	 def start(self):
	 while True:
	     service_client_socket, ip_port = self.tcp_server_socket.accept()
	     # 连接建立成功，开辟子线程处理客户端的请求
	     sub_thread = threading.Thread(target=self.handle_client_request, args=(service_client_socket,))
	     sub_thread.start()

静态 Web 服务器-命令行启动动态绑定端口号
**********************************************************************************

* 开发命令行启动动态绑定端口号的静态 web 服务器

::

	获取执行python程序的终端命令行参数
	判断参数的类型，设置端口号必须是整型
	给Web服务器类的初始化方法添加一个端口号参数，用于绑定端口号

* 静态Web服务器-命令行启动动态绑定端口号的示例代码

.. code-block:: python
    :linenos:

	import socket
	import threading
	import sys

	# 定义web服务器类
	class HttpWebServer(object):
	    def __init__(self, port):
	        # 创建tcp服务端套接字
	        tcp_server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
	        # 设置端口号复用, 程序退出端口立即释放
	        tcp_server_socket.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, True)
	        # 绑定端口号
	        tcp_server_socket.bind(("", port))
	        # 设置监听
	        tcp_server_socket.listen(128)
	        # 保存创建成功的服务器套接字
	        self.tcp_server_socket = tcp_server_socket

	    # 处理客户端的请求
	    @staticmethod
	    def handle_client_request(new_socket):
	        # 代码执行到此，说明连接建立成功
	        recv_client_data = new_socket.recv(4096)
	        if len(recv_client_data) == 0:
	            print("关闭浏览器了")
	            new_socket.close()
	            return

	        # 对二进制数据进行解码
	        recv_client_content = recv_client_data.decode("utf-8")
	        print(recv_client_content)
	        # 根据指定字符串进行分割， 最大分割次数指定2
	        request_list = recv_client_content.split(" ", maxsplit=2)

	        # 获取请求资源路径
	        request_path = request_list[1]
	        print(request_path)

	        # 判断请求的是否是根目录，如果条件成立，指定首页数据返回
	        if request_path == "/":
	            request_path = "/index.html"

	        try:
	            # 动态打开指定文件
	            with open("static" + request_path, "rb") as file:
	                # 读取文件数据
	                file_data = file.read()
	        except Exception as e:
	            # 请求资源不存在，返回404数据
	            # 响应行
	            response_line = "HTTP/1.1 404 Not Found\r\n"
	            # 响应头
	            response_header = "Server: PWS1.0\r\n"
	            with open("static/error.html", "rb") as file:
	                file_data = file.read()
	            # 响应体
	            response_body = file_data

	            # 拼接响应报文
	            response_data = (response_line + response_header + "\r\n").encode("utf-8") + response_body
	            # 发送数据
	            new_socket.send(response_data)
	        else:
	            # 响应行
	            response_line = "HTTP/1.1 200 OK\r\n"
	            # 响应头
	            response_header = "Server: PWS1.0\r\n"

	            # 响应体
	            response_body = file_data

	            # 拼接响应报文
	            response_data = (response_line + response_header + "\r\n").encode("utf-8") + response_body
	            # 发送数据
	            new_socket.send(response_data)
	        finally:
	            # 关闭服务与客户端的套接字
	            new_socket.close()

	    # 启动web服务器进行工作
	    def start(self):
	        while True:
	            # 等待接受客户端的连接请求
	            new_socket, ip_port = self.tcp_server_socket.accept()
	            # 当客户端和服务器建立连接程，创建子线程
	            sub_thread = threading.Thread(target=self.handle_client_request, args=(new_socket,))
	            # 设置守护主线程
	            sub_thread.setDaemon(True)
	            # 启动子线程执行对应的任务
	            sub_thread.start()

	# 程序入口函数
	def main():
	    print(sys.argv)
	    # 判断命令行参数是否等于2,
	    if len(sys.argv) != 2:
	        print("执行命令如下: python3 xxx.py 8000")
	        return

	    # 判断字符串是否都是数字组成
	    if not sys.argv[1].isdigit():
	        print("执行命令如下: python3 xxx.py 8000")
	        return

	    # 获取终端命令行参数
	    port = int(sys.argv[1])
	    # 创建web服务器对象
	    web_server = HttpWebServer(port)
	    # 启动web服务器进行工作
	    web_server.start()

	if __name__ == '__main__':
	    main()

* 小结

::

	获取执行python程序的终端命令行参数

	 sys.argv

	判断参数的类型，设置端口号必须是整型

	 if not sys.argv[1].isdigit():
	     print("启动命令如下: python3 xxx.py 9090")
	     return
	 port = int(sys.argv[1])

	给Web服务器类的初始化方法添加一个端口号参数，用于绑定端口号

	 def __init__(self, port):
	     self.tcp_server_socket.bind((“”, port))

.. |image0| image:: /_static/python_ai/ip-1.png
.. |image1| image:: /_static/python_ai/ip-3.png
.. |image2| image:: /_static/python_ai/netinfo.png
.. |image3| image:: /_static/python_ai/net_vaild.png
.. |image4| image:: /_static/python_ai/port-1.png
.. |image5| image:: /_static/python_ai/port_2.png
.. |image6| image:: /_static/python_ai/tongxin_process.png
.. |image7| image:: /_static/python_ai/face_conn.png
.. |image8| image:: /_static/python_ai/sjy_timg.jpg
.. |image9| image:: /_static/python_ai/socket201.png
.. |image10| image:: /_static/python_ai/tcp_1222.png
.. |image11| image:: /_static/python_ai/tcp_099.png
.. |image12| image:: /_static/python_ai/send_recv.png
.. |image13| image:: /_static/python_ai/web0099.png
.. |image14| image:: /_static/python_ai/tongxin-100.png
.. |image15| image:: /_static/python_ai/tongxin_process-2.png
.. |image16| image:: /_static/python_ai/tongxin_process-3.png
.. |image17| image:: /_static/python_ai/get_info_anly.png
.. |image18| image:: /_static/python_ai/post_009.png
.. |image19| image:: /_static/python_ai/post-01.png
.. |image20| image:: /_static/python_ai/get_post.png
.. |image21| image:: /_static/python_ai/response-001.png
.. |image22| image:: /_static/python_ai/response_info-2.png
.. |image23| image:: /_static/python_ai/response_info_999.png
.. |image24| image:: /_static/python_ai/web_tongxin_process.png
.. |image25| image:: /_static/python_ai/web_server_faq.png















































































































