# muduo_chat
基于muduo网络库的集群聊天系统

该网络项目基于muduo网络库实现，应用到的知识点主要有
1.json序列化反序列化
2.muduo网络库开发
3.nginx源码编译安装和环境部署
4.nginx的tcp负载均衡器配置
5.redis缓存服务器编程实践
6.基于发布-订阅的服务器中间件redis消息队列编程实践
7.mysql数据库编程
8.CMake构建编译环境
9.Github托管项目

项目需求
1.分为客户端和服务器即CS架构
2.客户端用户登录 注销操作（登录后数据库中用户状态为在线，执行注销操作后用户为不在线状态）
3.添加好友和添加群组
4.好友聊天
5.群主聊天
6.离线消息
7.nginx配置负载均衡
8.集群聊天系统支持跨服务器通信


# json介绍
json主要是用来对数据进行序列化，使其便于在网络中传输
在网络中，常用的数据传输序列化格式有XML,JSON,PROTOBU

使用时需要使用第三方库，将json.hpp移植到工程文件夹下
···
#include "json.hpp"
using json = nlohmann::json;
···

# muduo网络模型介绍
采用main reactor和subreactor模型设计，其中main reactor负责accept连接（新用户连接），然后把连接分发到子反应堆中进行具体业务处理。多个连接可能被分发到多个线程中，以充分利用CPU。
muduo网络库为用户提供了两个主要的类

TCPSERVER 用于编写服务器程序

TCPCLIENT 用于编写客户端程序

核心是epoll+线程池

好处：能够把网络io的代码和业务代码区分开

muduo网络库开发服务程序的步骤为

组合TCP服务对象

创建EVENTLOOP事件循环对象的指针

明确TCP服务构造函数需要什么参数，输出CHATSERV设ER的构造函数

在当前服务器的构造函数中，注册处理连接的回调函数和处理读写的回调函数



设置合适的服务器线程数量,muduo会自己划分io线程和worker线程


# 回调函数介绍
# handler处理器介绍


自己踩到的坑
1.想增加一个“用户登录客户端后，客户端异常退出时，数据库对应的用户状态也应该由online状态变为offline状态”功能，刚开始想的是用一个signal函数来捕捉ctrl+c信号，然后直接执行操作数据库命令，后来想了想，这个是客户端，客户端不应该直接操作数据库，这样会造成危险，想了想客户端应该通过服务器来间接操作数据库来实现状态更新操作。于是写了一个sighandler函数来跟服务器通信，执行服务器中的loginout函数，实现了客户端异常退出时，用户在线状态的切换。

2.
