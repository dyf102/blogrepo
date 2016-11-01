layout: blog
title: Interview Summary
date: 2016-10-31 15:18:18
tags: interview, 中文
---
##  Programming Language
C++

1.	什么是虚函数 
当从父类继承的时候，虚函数和被继承的函数有相同的签名，但在运行过程中，会自动的选择适当的具体实现运行。虚函数是面向对象编程实现多态的具体手段。
2.	Static 静态变量和静态函数
静态变量：
		作用域：全局变量：文件内访问
		函数内变量： 函数内访问
		类内变量：可以通过类或者实例访问
		生命周期：文件内
	静态函数：
		只能在同一个翻译单元内访问
		目的：隔离函数，防止被其他编译单元调用
		解决同名函数，在不同编译单元
3. Inline 函数内链
优点：1.	减小函数调用的开销，参数拷贝pop,push stack，返回值的拷贝 	
	2.	增加函数指令访问的局部局部性
	3.	放入头文件里	

缺点:1.		增加执行文件的大小
		2.改变内连函数，其他文件需要重新编译

JAVA

1. HashMap 和 ConcurrentHashMap 的区别然后和hashTable 
	HashMap 线程不安全
	concurrentHashMap  用来替代HashTable，无锁，通过分配更大的空间来实现
2. JAVA 有什么其他的同步方法除了Synchronize
4. 设计模式
5. Colloection.sort 为什么用merge sort 作为排序object
6. Java GC
	引用计数的缺点：
	1.	只保存了引用的数量但是没有存引用他的对象，问题循环引用
	2.	增减开销
	mark-sweep:标记清除: 两个阶段
	发生条件：所有线程到达安全点，不在分配内存。
	标记阶段：从跟节点标记所有能够达到的节点，
	清除阶段：清楚所有不能够达到的节点
	标记和清除阶段都会暂停程序
	缺点：内存碎片化 http://www.jianshu.com/p/778dd3848196
	解决方法：标记-压缩-清除 算法：将所有存活对象拷贝到堆的顶部
7.	引用 1.强应用（不会被GC） 2.软引用（内存不够的时候GC） 3. 弱引用（gc回收）
8.	双亲委派模型是通过loadClass()方法来实现的	
	先检查是否已经被加载过，如果没有则调用父加载器的loadClass()方法，如果父加载器为空则默认使用启动类加载器作为父加载器。如果父类加载器加载失败，则先抛出ClassNotFoundException，然后再调用自己的findClass()方法进行加载。
9.	Object 的method
	clone, equal, wait, notify, toString, getClass,hashCode

```java
	public String toString() {
        return getClass().getName() + "@" + Integer.toHexString(hashCode());
    }
```
identity hash code实现用的是一个伪随机数生成器
10.	对象逃逸


### Algorithm
1.	Quick sort(NS) insertion sort(S) 
	merge sort (S)
	bubble sort(S) selection sort(NS)

### Network
1. TCP/UDP
TCP 是一个可靠，有序的，错误检查的，基于字节流的传输层协议
	开始三次握手：SYN SYN-ACK ACK
	结束四次握手：FIN ACK and FIN ACK
	慢速开始，快速重传，快速恢复
	TIME_WAIT:2MST maximum segment time
[](http://images0.cnblogs.com/blog2015/731178/201508/012351586269072.png)
UDP 用户数据报文协议，不可靠，没有阻塞控制
基于UDP：DNS，HDCP，RIP(从IP得到Mac)

2. Socket

```c
int socket(int domain, int type, int protocol);

client side:
int connect(int s, const struct sockaddr *name, socklen_t namelen);
while ((bytes = read(s, buffer, BUFSIZ)) > 0)
    write(1, buffer, bytes);
```
---
Server side:
绑定端口

```c
int bind(int s, const struct sockaddr *addr, socklen_t addrlen);

int listen(int s, int backlog); //blacklog: maximum size of the queue of pending connections.

int accept(int s, struct sockaddr *addr, socklen_t *addrlen);
client = fdopen(c, "w")
```

## Operation System

1.	线程和进程的区别
	申请资源的基本单位是进程，在CPU得到执行的基本单位是线程（调度实体），进程是由程序段、数据段、PCB组成的
	一个进程有一个或多个线程
	进程之间有内存保护，但是同一进程之中的线程没有内存保护
	线程的上下文切换开销小，进程的上下文切换开销大
2.	进程通信方式有哪些？
	信号，管道，消息队列，socket 
3.	线程通讯方式有哪些？
	共享内存
4.	线程同步方式
	锁（互斥量），信号量，临界区， 信号
5.	什么是锁mutax 确保多线程访问资源互斥的对象
6.	死锁
	必要条件：互斥条件，请求和保持条件，非剥夺条件，循环等待
7.	线程池的作用
	1.	重复利用已经创建的县城
	2.	提高响应时间，避免等待建立新线程的
	线程池的大小取决于任务是IO密集型还是计算密集型
8.	长连接池：节省长连接的开销
9.	epoll, select, poll 的区别
	都是IO多路复用，同步IO
	区别：select／poll 需要遍历来寻找就绪的描述符，而且最大数量有限
	epoll :
	//创建一个epoll的句柄，size用来告诉内核这个监听的数目一共有多大
	
```c
int epoll_create(int size)；
// 通过epoll 的fd 来注册事件，避免二次拷贝
int epoll_ctl(int epfd, int op, int fd, struct epoll_event *event)；
// 阻塞当前线程，等待epfd上注册的事件
int epoll_wait(int epfd, struct epoll_event * events, int maxevents, int timeout);
```

## Database
1.	Mysql 行最大长度
	
4096 Columns， less than 65,535 bytes (2^16 - 1)
InnoDB restricts row size to about 16000 bytes
MyISAM 和InnoDB 的区别
mysql isolation 和 oracle 的区别
B-tree 和 B+ tree 
B+ tree 所有叶子结点是相连的，非叶子结点不储存数据

## OOP
什么是继承
什么是单例 Singleton
什么是工厂模式 Factory

腾讯一面题
1.	除法不用运算符号
2.	折半查找
3.  虚函数是用什么实现的
4.	如何设计一个高可用高并发高可用的服务器
	高可用：
5.	Linux 内核计时器 
	数据结构：双向循环定时器队列

跟谁学
1.	实现平衡二叉树
2.	strstr:KMP

滴滴面试
1.	square of root
2.	sharding
3.	consistent hashing/virtual Node
4.	traffic meter(大量请求):令牌桶算法
5.	gevent 底层实现
6.	什么场景下用MongoDB
7.	Ngnix reverse proxy algorithm
	1. 轮训 2. 随机 3. response time 4. IP hashing(batter for clients Session)


网易游戏
1.	函数指针 int (*funPtr) (const void *a, const void *b)
2.	Docker 的底层实现
3.	MongoDB的索引的问题 

Redis 相关问题
1.	CAP consistent, Availability, Partition Fault Tolerence
2. 	Master-Slave
3.	The data structure behind the five data type
4.	redis string how to store the int, float, even binary data
5.	How to store the different type key/value pair?

进入头条笔试题
1. 	count right shifted string is same as original string
2.	sort the integer lexicographically(easy time out/ OOM)

百度笔试
1.	count next permutation until a sorted list
2.	count the number of component
3.	Minimum Spanning Tree

美团点评面试
1.	redis build-in data structure
2.	mysql inndeDB and MyISAM difference
3.	HTTP keep-alive 
4.	What is the isolation of relational database(Mysql)
5.	The data structure of red-black tree
6.	difference between B-tree and B+ tree 
