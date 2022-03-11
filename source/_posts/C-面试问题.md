---
title: C++面试问题
date: 2022-03-11 12:40:21
tags:
- c++
- 面试
categories:
- 个人博客
- 招聘面试经历整理
---





# c/ c++面试笔试

### 1.c++的知道哪些C语言字符串拷贝的方法
####(1) 第一个strcpy()
使用头文件：#include <string.h>  

    定义：char *strcpy(char *dest, const char *src);
    
    参数：
    destinin：目标字符数组；
    source：源字符数组；
    
    函数说明：  
	strcpy()会将参数src 字符串拷贝至参数dest 所指的地址。 
	用于对字符串进行复制，识别到字符串的结束符号‘\0’自动停止
    
    返回值：
	返回参数dest 的字符串起始地址。
    
    注意：
    参数 dest 的内存空间要足够大，否则拷贝可能会造成缓冲溢出。

    strcpy() 在复制结束后会添加结束符\0，这点和strncpy()不同

#### (2) memcpy()
    使用头文件：C语言：#include <string.h> C++：#include<cstring>
    
    定义：void memcpy(void *dest, const void *src, size_t n);
    
    参数：
    
    destinin：目标地址；
    source：源地址；
    n：复制的字节长度。
    
    函数说明：
	memcpy()复制 src 所指的内存数据的 n 个字节到 dest所指的内存地址上。也就是从源地址复制n 个字节到目标地址

第一个和第二个指针都是void型且第二个指针不能被修改，第三个参数是需要拷贝的内存长度按字节记。

返回值：返回指向 dest 的指针。返回的指针类型是void。

注意：

**memcpy()并不限制被复制的数据类型，只是逐字节地进行复制，任何数据类型都可以进行复制，例如字符数组、整型、结构体、类等**

memcpy() 会完整的复制 num个字节，**不会遇到‘\0’而结束**，这点与 strcpy() 不同

dest 和 src所指的内存空间地址不能重叠

参数 dest 的内存空间要足够大，起码要大于等于 num个字节

**通常在复制字符串时用strcpy，而需要复制其他类型数据时则一般用memcpy**

#### (3)strncpy()
    使用头文件：#include <string.h>
    
    定义：char *strncpy(char *dest, const char *src, size_t len);
    
    参数：
    
    destinin：目标字符数组；
    source：源字符数组；
    len：复制的字符串长度。
    
    函数说明：strncpy()复制字符串 src 的前 len 个字节到 dest所指的内存地址上。
    
    返回值：返回字符串dest

注意：

- strncpy()在复制结束后不会向dest结尾添加’\0’结束符 这个是很重要的一个点，要记住

- 如果source(源字符数组)的长度>复制的字符串数len，则只复制source(源字符数组)的前len个字符，不会自动添加结束符\0 

- 如果source(源字符数组)的长度<复制的字符串数len，则以NULL填充dest(目标字符数组),直到复制完n个字节

- 参数 dest 的内存空间要足够大，起码要大于等于 num个字节

- 在使用strncpy()的时候，拷贝长度最好为strlen(src)+1，以保证最后的结束符\0也能被复制
#### (3) memmove()
    使用头文件：#include <string.h>
    
    定义：void *memmove( void* dest, const void* src, size_t count );
    
    参数：
    
    destinin：目标地址；
    source：源地址；
    count：复制的字节长度。

函数说明：memmove()复制 src 所指的内存数据的 n 个字节到 dest所指的内存地址上。也就是从源地址复制n 个字节到目标地址。  
**如果目标区域和源区域有重叠的话，memmove能够保证源串在被覆盖之前将重叠区域的字节拷贝到目标区域中，但复制后源内容会被更改。但是当目标区域与源区域没有重叠则和memcpy函数功能相同。**


### 2.new 和 malloc的区别
#### (1). 申请的内存所在位置
 new操作符从自由存储区（free store）上为对象动态分配内存空间，  
 malloc函数从堆上动态分配内存。  
 自由存储区是C++基于new操作符的一个抽象概念，凡是通过new操作符进行内存申请，该内存即为自由存储区。而堆是操作系统中的术语，是操作系统所维护的一块特殊内存，用于程序的内存动态分配，C语言使用malloc从堆上分配内存，使用free释放已分配的对应内存。

**那么自由存储区是否能够是堆（问题等价于new是否能在堆上动态分配内存），这取决于operator new 的实现细节。自由存储区不仅可以是堆，还可以是静态存储区，这都看operator new在哪里为对象分配内存。**


####(2).返回类型安全性
**new操作符内存分配成功时，返回的是对象类型的指针**，类型严格与对象匹配，无须进行类型转换，故new是符合类型安全性的操作符。  
**而malloc内存分配成功则是返回void 星** ，需要通过强制类型转换将void*指针转换成我们需要的类型。

####(3).内存分配失败时的返回值
**new内存分配失败时，会抛出bac_alloc异常，它不会返回NULL；malloc分配内存失败时返回NULL。**

    try
    {
    int *a = new int();
    }
    catch (bad_alloc)
    {
    ...
    }
如果你想顺便了解下异常基础，可以看[http://www.cnblogs.com/QG-whz/p/5136883.html](http://www.cnblogs.com/QG-whz/p/5136883.html) C++ 异常机制分析。

####(4).是否需要指定内存大小
使用new操作符申请内存分配时无须指定内存块的大小，编译器会根据类型信息自行计算，而malloc则需要显式地指出所需内存的尺寸。

####(5).是否调用构造函数/析构函数
使用new操作符来分配对象内存时会经历三个步骤：

	第一步：调用operator new 函数（对于数组是operator new[]）分配一块足够大的，原始的，未命名的内存空间以便存储特定类型的对象。
    第二步：编译器运行相应的构造函数以构造对象，并为其传入初值。
    第三部：对象构造完成后，返回一个指向该对象的指针。
使用delete操作符来释放对象内存时会经历两个步骤：

    第一步：调用对象的析构函数。
    第二步：编译器调用operator delete(或operator delete[])函数释放内存空间。  
总之来说，**new/delete会调用对象的构造函数/析构函数以完成对象的构造/析构。而malloc则不会。**



####(6).对数组的处理

new对数组的支持体现在它会分别调用构造函数函数初始化每一个数组元素，释放对象时为每个对象调用析构函数。


- 注意delete[]要与new[]配套使用，不然会找出数组对象部分释放的现象，造成内存泄漏。

- 至于malloc，它并知道你在这块内存上要放的数组还是啥别的东西，反正它就给你一块原始的内存，在给你个内存的地址就完事。所以如果要动态分配一个数组的内存，还需要我们手动自定数组的大小：


####(7).new与malloc是否可以相互调用
operator new /operator delete的实现可以基于malloc，而malloc的实现不可以去调用new。下面是编写operator new /operator delete 的一种简单方式，其他版本也与之类似：

####(8).是否可以被重载
opeartor new /operator delete可以被重载。标准库是定义了operator new函数和operator delete函数的8个重载版本：而malloc/free并不允许重载。

####(9). 能够直观地重新分配内存
使用malloc分配的内存后，如果在使用过程中发现内存不足，**可以使用realloc函数进行内存重新分配实现内存的扩充**。realloc先判断当前的指针所指内存是否有足够的连续空间，如果有，原地扩大可分配的内存地址，并且返回原来的地址指针；如果空间不够，先按照新指定的大小分配空间，将原有数据从头到尾拷贝到新分配的内存区域，而后释放原来的内存区域。

new没有这样直观的配套设施来扩充内存。

#### (10). 客户处理内存分配不足
在operator new抛出异常以反映一个未获得满足的需求之前，它会先调用一个用户指定的错误处理函数，这就是new-handler。对于malloc，客户并不能够去编程决定内存不足以分配时要干什么事，只能看着malloc返回NULL。

### 3. unix 的初始化和启动

#### (1)unix系统的初始引导过程  
    unix从硬件加电到-->ROM BIOS（检查硬件系统的配置，找到硬盘0号扇区）-->将0号扇区读入内存

#### (2)unix系统的初始化  
    ROM BIOS --> mboot/pboot -->boot -->stand/unix -->init0 -->init1  
    init -- RC.sysint -- rc.x --login
    在/etc/inittab 中获取initdefault值进入默认的运行级

    Unix的8个运行级
	0 停止操作系统
	1 单用户
	2 多用户
	3 加网络
	4 多用户配置环境
	5固件诊断，关闭所有服务，关机
	6 重启
	S/s 安装、root目录外 其他目录不安装，运行系统内部程序，单用户
	Abc 伪运行级 运行用户自定义出的程序系统的运行级不会发生变化
/etc/RC N N为上述1~6

- 检查修复root文件系统  

- 设置内部时钟

- 清理和安装所有文件系统

- 清理临时文件 /tnp和/var/tmp

- 设置网络端口、接口、路由

- 启动日志文件调度进程syslogd

- 后台作业调度程序cron

- 启动必要的终端服务进程或应用特定的服务进程

- 用户注册程序

#### (3)unix的启动顺序

通过/boot/vm进行启动 vmlinuz

init /etc/inittab

启动相应的脚本，并且打开终端

    rc.sysinit
    rc.d(里面的脚本）
    rc.local

启动login登录界面 login

在用户登录的时候执行sh脚本的顺序(每次登录的时候都会完全执行的)

    /etc/profile.d/file
    /etc/profile
    /etc/bashrc
    /root/.bashrc
    /root/.bash_profile

### 4. linux系统初始化和启动服务

linux启动服务详见:[https://blog.csdn.net/chinaren0001/article/details/6901454?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_title~default-0.queryctrv2&spm=1001.2101.3001.4242.1&utm_relevant_index=3](https://blog.csdn.net/chinaren0001/article/details/6901454?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_title~default-0.queryctrv2&spm=1001.2101.3001.4242.1&utm_relevant_index=3 "Linux如何初始化和启动服务")
Linux 系统启动过程

linux启动时我们会看到许多启动信息。

Linux系统的启动过程并不是大家想象中的那么复杂，其过程可以分为5个阶段：  

    操作系统--> 
    /boot(MBR)-->
	init进程-->
	运行级别(在/etc/inittab文件中)-->
	初始化系统(/etc/RC.d/RC.sysint) --> 
	/etc/rc.d/rc x.d(根据载入的run level值载入对应目录的程序,决定开启哪些服务)-->
	/sbin/minggetty(启动六个虚拟控制台)-->
	用户登录系统(login)

  

    内核的引导。
    运行init。
    系统初始化。
    建立终端 。
    用户登录系统。

#### (1) BIOS-->MBR-->KERNEL-->INIT进程(一切进程的起点)



1. 当 电脑一打开 电源时电脑就会进入BIOS（BIOS的工作主要是检测一些硬件设备）；


2. 检测完后会进入MBR也就是boot loader（MBR位于硬盘的第一个扇区总共512bytes，其中前446bytes里面的编码是在选择引导分区也就是决定要由哪个分区来引导）；


3. 载入系统的Kernel(核心)，在Kernel里主要是载入电脑设备的驱动程序，以便可以控制电脑上的设备，并且以只读方式来挂载根目录，也就是一 开始只能读取到根目录所对应的那个分区，所以/etc、/bin、/sbin、/dev、/lib这五个目录必须同根目录在一个分区中；


4. 最后启动init这个程序，所以init这个程序的进程编号为1，是Linux中第一个执行的程序；


init这个程序会根据 Run level来执行以下这些程序：

    ·/etc/rc.d/rc.sysinit;  
    ·/etc/rc.d/rc 和 etc/rc.d/rc?.d/  
    ·/etc/rc.d/rc.local  

#### (2) 介绍 /etc/rc.d/rc.sysinit 这个程序主要做哪些工作（init初始化流程第二步）  
1. 启动 udev ，也就是启用热插拔的设备，例如：USB，并且也会启动SELinux；  
2. 会把kernel的参数设定在/etc/sysctl.conf配置文件里；这个配置文件下下单元详细说明；  
3. 设定系统时间；  
4. 载入 keymaps 设定， keymap设定是在定义 键盘，这样电脑开机时才能找到相对应的键盘设定；  
5. 启用swap这个虚拟内存的分区；  
6. 设定主机名称，主机名称设定在 /etc/sysconfig/network 配置文件中的 HOSTNAME= 项下；  
7.  检查根目录有没有问题，并且重新挂载成为可读可写的状态；  
8. 启用RAID磁盘阵列，以及LVM的设备；
9. 启用磁盘配额的功能，就是限制使用者最多可以使用多少硬盘空间；  
10. 检查其它的文件系统，并且把它们挂载进来；  
11. 最后会清除被修改过的locks及PID files，其实就是清除一些开机时的缓存文件，以及一些没有用的信息及文件； 

### 5.三次握手 和四次挥手
详解看这个 结合计算机网络 408 教材
[https://blog.csdn.net/qq_44443986/article/details/115966274?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522164644652416781685335366%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=164644652416781685335366&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-115966274.pc_search_result_control_group&utm_term=tcp%E4%B8%89%E6%AC%A1%E6%8F%A1%E6%89%8B%E5%9B%9B%E6%AC%A1%E6%8C%A5%E6%89%8B&spm=1018.2226.3001.4187](https://blog.csdn.net/qq_44443986/article/details/115966274?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522164644652416781685335366%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=164644652416781685335366&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-115966274.pc_search_result_control_group&utm_term=tcp%E4%B8%89%E6%AC%A1%E6%8F%A1%E6%89%8B%E5%9B%9B%E6%AC%A1%E6%8C%A5%E6%89%8B&spm=1018.2226.3001.4187 "三次握手和四次挥手详解")
### 6.操作系统内存管理
分段 分页 段页式

### 7.new 的底层实现
operator_num malloc 


### 8.stl的vector 和 map/set 无序的map怎么排序 
key 和 value 对键值和value的排序通过修改compare的函数
### 9. 多线程 的同步方式,多进程的 通信方式 
####(1) 进程间通讯：
1. 管道( pipe )：管道是一种半双工的通信方式，数据只能单向流动，而且只能在具有亲缘关系的进程间使用。进程的亲缘关系通常是指父子进程关系。

2. 有名管道 (namedpipe) ： 有名管道也是半双工的通信方式，但是它允许无亲缘关系进程间的通信。

3. 高级管道(popen)：将另一个程序当做一个新的进程在当前程序进程中启动，则它算是当前程序的子进程，这种方式我们成为高级管道方式。

4. 信号量( semophore ) ：信号量是一个计数器，可以用来控制多个进程对共享资源的访问。它常作为一种锁机制，防止某进程正在访问共享资源时，其他进程也访问该资源。因此，主要作为进程间以及同一进程内不同线程之间的同步手段。

5. 消息队列( messagequeue ) ： 消息队列是由消息的链表，存放在内核中并由消息队列标识符标识。消息队列克服了信号传递信息少、管道只能承载无格式字节流以及缓冲区大小受限等缺点。

6. 信号 ( sinal ) ：信号是一种比较复杂的通信方式，用于通知接收进程某个事件已经发生。

7. 共享内存( sharedmemory ) ：共享内存就是映射一段能被其他进程所访问的内存，这段共享内存由一个进程创建，但多个进程都可以访问。共享内存是最快的 IPC 方式，它是针对其他进程间通信方式运行效率低而专门设计的。它往往与其他通信机制，如信号两，配合使用，来实现进程间的同步和通信。

8. 套接字( socket ) ： 套解口也是一种进程间通信机制，与其他通信机制不同的是，它可用于不同及其间的进程通信。

注意：临界区则是一种概念，指的是访问公共资源的程序片段，并不是一种通信方式。

####(2) 线程通讯：  
1. 互斥锁提供了以排他方式防止数据结构被并发修改的方法。  
2. 读写锁允许多个线程同时读共享数据，而对写操作是互斥的。    
3. 条件变量可以以原子的方式阻塞进程，直到某个特定条件为真为止。对条件的测试是在互斥锁的保护下进行的。条件变量始终与互斥锁一起使用。  
4. 信号量机制(Semaphore)：包括无名线程信号量和命名线程信号量  
5. 信号机制(Signal)： 类似进程间的信号处理
  
**提问：互斥锁与信号量的区别？**
答：互斥锁用于线程的互斥，信号量用于线程的同步。这是互斥锁和信号量的根本区别，也就是互斥和同步之间的区别。同时互斥锁的作用域仅仅在于线程，信号量可以作用于线程和进程。

### 10 .虚函数  虚函数表 虚函数指针 
(1)父类的析构函数不是虚函数会怎样?
(2)所有的函数都可以定义为虚函数吗？为什么？

### 11.main函数执行前后会做什么\
main函数执行前：  
		__start:
 	
	　　 :
	　　 init stack;
	　　 init heap;
	　　 open stdin;
	　　 open stdout;
	　　 open stderr;
	　　 :
	　　 push argv;
	　　 push argc;

	　　 call _main;    (调用 main)

	调用main函数之前，

    - 创建进程后，把控制权交给程序的入口函数，即为运行时库的某个入口函数。
    
    ​- glibc的入口函数是_start
    
    - msvc(vc 6.0)的是mainCRTStartup
    
    - 入口函数对运行库和运行环境进行初始化，包括堆、I/O、线程、全局变量构造
    
    - 初始化堆，栈（设置栈指针）
    
    - 全局变量、对象和静态变量、对象的空间分配和初始化。
    
    - 初始化C/C++库
    
    - 参数压栈  argc,argv ,获取环境变量
    
    - 打开标准输入，输出、错误流
    
    - 注册析构函数，使用atexit注册析构函数（注册时在链表头插入链接，main函数退出也从链表头开始获取链表函数，进行调用）


- 设置栈指针  
- 初始化static静态和global全局变量，即data段的内容  
- 将未初始化部分的全局变量赋初值：数值型short，int，long等为0，bool为FALSE，指针为NULL，等等，即.bss段的内容
- 运行全局构造器，估计是C++中构造函数之类的吧
- 将main函数的参数，argc，argv等传递给main函数，然后才真正运行main函数  

main函数执行后：

- 全局对象的析构函数会在main函数之后执行；  
- 可以用_onexit 注册一个函数，它会在main 之后执行;    


	调用main函数之后，  
    
    返回入口函数，进行清理工作  

    包括全局变量析构、堆销毁、关闭I/O  

    销毁堆内存  
    
    关闭标准输入、输出、关闭错误流  
    
    调用exit系统API退出进程 

 用于注册终止函数

##### atexit()  
int atexit (void (*func) (void) )  

c库中的atexit()函数，当程序正常中止时，调用指定的func函数。当然，你可以在任何地方注册终止函数，但它会在程序终止的时候被调用。

（终止函数func不接受任何参数）

_onexit() 

### 12.c++ 内存 分区管理
[https://blog.csdn.net/weixin_48953972/article/details/121402222](https://blog.csdn.net/weixin_48953972/article/details/121402222)
详见
![](p1.png)

![](p2.png)

栈帧
每个函数发生调用时，都会有一块栈空间，这块栈空间称为栈帧。

栈帧的结构如下：
![](p3.png)
这里要注意的是：栈空间是从高地址到低地址分配的。

- rbp：基址指针寄存器(reextended base pointer)，其内存放着一个指针，该指针永远指向系统栈最上面一个栈帧的底部。

- rsp：栈指针寄存器，其内存放着一个指针，该指针永远指向系统栈最上面一个栈帧的栈顶。

栈帧保存了一个函数调用所需要的维护信息：

1. 函数的返回地址和参数。  
2. 临时变量：包括函数的非静态局部变量以及编译器自动生成的其他临时变量。
保存的上下文：包括在函数调用前后需要保持不变的寄存器。

总结一下，当函数A调用函数B时：

    1. 将函数入参保存要寄存器中。（在函数A的调用栈中）
    2. 将函数B的返回地址压入栈，即将callq <B>的下一行指令地址压入栈，然后调用函数B。（在函数A的调用栈中）
    3. 函数参数入栈。（在函数B的调用栈中）
    4. 局部变量入栈。（在函数B的调用栈中）
    5. 执行一些运算指令。（在函数B的调用栈中）
    6. 将返回结果保存到寄存器eax中。（在函数B的调用栈中）
    7. 从寄存器eax中获得函数B的返回值。（在函数A的调用栈中）
### 13.c++string的构造,析构和赋值函数,拷贝函数
    class String 
	{
	public:

    	String(const char *str=NULL);//构造函数
    	String(const String &other);//拷贝构造函数
    	~String(void);//析构函数
    	String& operator=(const String &other);//等号操作符重载
    	void ShowString();
	private:
    	char *m_data;//指针
	};

	String::~String()
	{
		delete[] m_data;//析构函数，释放地址空间
	}

	String::String(const char *str)
	{
    	if (str==NULL)//当初始化串不存在的时候，为m_data申请
    	{
       		m_data = new char[1];
       		*m_data='\0';
    	}else
    	{
        	int length = strlen(str);
        	m_data = new char[length+1];
        	strcpy(m_data,str);
    	}
	}

	String::String(const String &other)//拷贝构造函数
	{
    	int length = strlen(other.m_data);
    	m_data = new char[length+1];
    	strcpy(m_data,other.m_data);
	}

	String& String::operator=(const String &other)
	{
    	if (this==&other)
    	{
        	return *this;
    	}
    	delete []m_data;
    	int length = strlen(other.m_data);
    	m_data=new char[length+1];
    	strlen(m_data,other.m_data);
    	return *this;
	}

### 14.交换函数数值"int*int*int&int&"
- int a 值传递 c++拷贝数据  
- int *a 地址传递 可以通过地址修改  
- int &a 引用传递 直接将这两个参数传过去 可以修改  
- const int & a 不改变值的引用传递

### 15.给定一个链表，和一个数字K返回链表倒数第K个节点的值。后面又基于这个题的思想改进了几个题，如一个链表，如何找到它的中间节点？找它的3/4节点呢？（这里面有很多的解法，较优的用双指针）
双指针 i走k-1步 j开始走 中间节点 i走一步 j走两步  四分之三 找到中间再来一次

### 16.vector 的size 可以等于 capacity 吗？如果vector 存储满了，还能再插入元素吗？为什么？
不可以 可以 size是当前存的个数 capcity是可以存的


### 17.知道哪些排序算法？手写冒泡排序算法，并对冒泡排序进行优化
//第一种 添加flag 设flag=0 当发生交换则置为1 检测为0则return   
//第二种 记录上一次交换的位置 此时1~lastpos是无序的  
//第三种 每次向前一个向后一个

### 18. 快速排序的优化
//第一种 序列长度达到一定大小时,使用插入排序
	
    int pivotPos;
    if (high - low + 1 < 10)
    {
        InsertSort(arr,low,high);
        return;
    }
    if(low < high)
    {
        pivotPos = Partition(arr,low,high);
        QSort(arr,low,pivotPos-1);
        QSort(arr,pivotPos+1,high);
    }

	//第二种
	尾递归优化  
	template <class T>
	void QSort(T arr[],int low,int high)
	{
    	int pivotPos;
    	if (high - low + 1 < 10)
    	{
        	InsertSort(arr,low,high);
        	return;
    	}
    	while(low < high)
    	{
        	pivotPos = Partition(arr,low,high);
        	QSort(arr,low,pivotPos-1);
        	low = pivotPos + 1;
    	}
	}

	//第三种聚集元素 三元选中的基准 聚集
	
### 19.TCP 和 IP 的区别
- ip是网络层点到点的协议 专注于原机到目标机 使用ip地址作为通讯地址 采用无连接经历交付方式   
- tcp是传输层进程到进程的协议 采用连接交付  套接字为 
### 20.TCP 的特性
三次握手 四次挥手 全双工 可靠 的 传输层协议

### 21.如何在一万个字符串中查找某一个字符串，说出算法思路。
kmp字符串匹配算法   求得前缀和后缀 的最大匹配

### 22.哈希冲突怎么解决
十字链表法 开放地址法(线性探测,平方探测,再哈希)

### Linux的常用指令。