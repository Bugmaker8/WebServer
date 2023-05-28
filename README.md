

TinyWebServer
===============
Linux下C++轻量级Web服务器,巩固linux网络编程

* 使用 **线程池 + 非阻塞socket + epoll(ET和LT均实现) + 事件处理(Reactor和模拟Proactor均实现)** 的并发模型
* 使用**状态机**解析HTTP请求报文，支持解析**GET和POST**请求
* 访问服务器数据库实现web端用户**注册、登录**功能，可以请求服务器**图片和视频文件**
* 使用**MySql**进行数据存储，并且使用**数据库连接池**对连接进行管理，减少连接创建销毁的开销
* 实现**同步/异步日志系统**，记录服务器运行状态
* 使用**升序链表定时器**将非活跃玩家和断线连接超时的用户踢出服务器
* 经Webbench压力测试可以实现**上万的并发连接**数据交换




目录
-----

| [框架](#框架)  | [压力测试](#压力测试)  | [个性化运行](#个性化运行)  | 



框架
-------------
<div align=center><img src="http://ww1.sinaimg.cn/large/005TJ2c7ly1ge0j1atq5hj30g60lm0w4.jpg" height="765"/> </div>



压力测试
-------------
在关闭日志后，使用Webbench对服务器进行压力测试，对listenfd和connfd分别采用ET和LT模式，均可实现上万的并发连接，下面列出的是两者组合后的测试结果. 

> * Proactor，LT + LT，93251 QPS

<div align=center><img src="http://ww1.sinaimg.cn/large/005TJ2c7ly1gfjqu2hptkj30gz07474n.jpg" height="201"/> </div>

> * Proactor，LT + ET，97459 QPS

<div align=center><img src="http://ww1.sinaimg.cn/large/005TJ2c7ly1gfjr1xppdgj30h206zdg6.jpg" height="201"/> </div>

> * Proactor，ET + LT，80498 QPS

<div align=center><img src="http://ww1.sinaimg.cn/large/005TJ2c7ly1gfjr24vmjtj30gz0720t3.jpg" height="201"/> </div>

> * Proactor，ET + ET，92167 QPS

<div align=center><img src="http://ww1.sinaimg.cn/large/005TJ2c7ly1gfjrflrebdj30gz06z0t3.jpg" height="201"/> </div>

> * Reactor，LT + ET，69175 QPS

<div align=center><img src="http://ww1.sinaimg.cn/large/005TJ2c7ly1gfjr1humcbj30h207474n.jpg" height="201"/> </div>

> * 并发连接总数：10500
> * 访问服务器时间：5s
> * 所有访问均成功


个性化运行
------

```C++
./server [-p port] [-l LOGWrite] [-m TRIGMode] [-o OPT_LINGER] [-s sql_num] [-t thread_num] [-c close_log] [-a actor_model]
```

温馨提示:以上参数不是非必须，不用全部使用，根据个人情况搭配选用即可.

* -p，自定义端口号
	* 默认9006
* -l，选择日志写入方式，默认同步写入
	* 0，同步写入
	* 1，异步写入
* -m，listenfd和connfd的模式组合，默认使用LT + LT
	* 0，表示使用LT + LT
	* 1，表示使用LT + ET
    * 2，表示使用ET + LT
    * 3，表示使用ET + ET
* -o，优雅关闭连接，默认不使用
	* 0，不使用
	* 1，使用
* -s，数据库连接数量
	* 默认为8
* -t，线程数量
	* 默认为8
* -c，关闭日志，默认打开
	* 0，打开日志
	* 1，关闭日志
* -a，选择反应堆模型，默认Proactor
	* 0，Proactor模型
	* 1，Reactor模型

测试示例命令与含义

```C++
./server -p 9007 -l 1 -m 0 -o 1 -s 10 -t 10 -c 1 -a 1
```

- [x] 端口9007
- [x] 异步写入日志
- [x] 使用LT + LT组合
- [x] 使用优雅关闭连接
- [x] 数据库连接池内有10条连接
- [x] 线程池内有10条线程
- [x] 关闭日志
- [x] Reactor反应堆模型

