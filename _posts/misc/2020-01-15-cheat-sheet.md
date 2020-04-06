---
layout: post
title: 面试小抄
categories: miscellaneous
tags: cheat-sheet interview basic-computer-knowledge 
date: 2020-01-15
---

路漫漫其修远兮。。。

{% include brline %}

## TCP/IP

三次握手及四次挥手与拜占庭将军问题。

此处可先实现高级语言层面的 TPC/IP 协议包的通信模拟再继续编写。

{% include brline %}

## UDP vs TCP

UDP 由于无法保证消息发送的顺序以及数据的可靠性、安全性，一般用作「消息广播」或进行「DNS索引」之类的操作。

此处可先实现高级语言层面的 UDP 协议包的通信模拟再继续编写。

{% include brline %}

## HTTP/HTTPS

HTTPS 就是发送的 HTTP 数据包使用了 SSL/TLS 等公私钥技术对报文内容进行加密的一种应用协议，「S」意味着它是安全的。

SSL/TSL 的加密方案与技术支持可追溯到高斯、费马、欧拉等人，其安全由「素数因素分解」本身的数学难度所保障。

此处可用魔术师洗牌例子解释 SSL/TSL。 

此处可先实现高级语言层面的 HTTP 协议包的通信模拟与应用于该协议包的 SSL/TSL 加密算法再继续编写。

{% include brline %}

## SOCKETS

？？？？

{% include brline %}

## RESTFUL

RESTful 的哲学就是一切皆资源，而一切资源的发现都要通过一个「地址」和「路径」进行索引。
RESTful 原则下定位到某个资源后，需要通过一些约定成俗的表示方法才能对数据进行操作。

HTTP 报文中保留了四个字节用于表示一次 RESTful 中所执行的动作，该四个字节通常为被称为「HTTP 谓词」。

常用的 HTTP 谓词有 7 个：

- GET：从服务器取出资源。
- POST：在服务器创建资源。
- PUT：在服务器插入资源。
- PATCH：在服务器更新资源。
- DELETE：从服务器删除资源。
- HEAD：获取资源的元数据。
- OPTIONS：获取提供给客户端的资源属性信息。

{% include brline %}

## DNS

？？？？

{% include brline %}

## P2P

？？？？

{% include brline %}

## JSON-RPC

RPC 理论上只要有一个程序语言，你就可以发送这语言的代码到一个「数据服务器」。
接着服务器接受并执行这代码，对数据进行索引、查询或重构，最后返回结果给客户端。

JSON-RPC 是 RPC 的一种传输协议，其传递内容的主体为 JSON。
JSON 格式的数据嵌套语法可以非常方便地在内容中定义调用的函数名称、参数等数据。
不同语言接收到该 JSON 后再根据各自的实现处理该次调用，以此实现跨语言调用的效果。

{% include brline %}

## MySQL

### CONNECTION

{% highlight bnf %}
$ mysql -u [username] <-h [hostname]> <-P [port]> <-p [password]>
{% endhighlight %}

### SHOW [option]

{% highlight bnf %}
option ::=
    | DATABASES
    | TABLES
    | PRIVILEGES
{% endhighlight %}

### DESCRIBE `table-name` <`column-name`>

### GRANT [privileges] ON [object].[level] TO [grantee]

{% highlight bnf %}
privileges ::=
    | ALL PRIVILEGES
    | [privilege] <, ...>    

object ::=
    | `table-name`
    | `function`
    | `procedure`

grantee ::=
    | [username]@[hostname] <IDENTIFIED BY [password]>

privilege ::=
    | ALTER <ROUTINE>
    | create <[create-body]>
    | DELETE
    | DROP
    | EVENT
    | EXECUTE
    | FILE
    | GRANT OPTION
    | INDEX
    | INSERT
    | LOCK TABLES
    | PROCESS
    | PROXY
    | REFERENCES
    | RELOAD
    | REPLICATION <[replication-body]>
    | SELECT
    | SHOW <[show-body]>
    | SHUTDOWN
    | SUPER
    | TRIGGER
    | CREATE TABLESPACE
    | UPDATE
    | USAGE

create-body ::=
    | ROUTINE
    | TEMPORARY TABLES
    | VIEW
    | USER

replication-body ::=
    | CLIENT
    | SLAVE

show-body ::=
    | DATABASES
    | SHOW VIEW
{% endhighlight %} 
    
### FLUSH PRIVILEGES

### CREATE [option] [body]

{% highlight bnf %}
option ::=
    | DATABASE
    | TABLE

body ::=
    | `database-name`
    | `table-name` <([pattern] <, ...>)>

pattern ::=
    | `field` [type]

type ::=
    | VARCHAR(length)
    | CHAR(length)
    | DATE
    | ...
{% endhighlight %}

### LOAD DATA [option] 

### sql build-in function or field 

{% highlight bnf %}
VERSION();
NOW();
CURRENT_DATE;
USER()
{% endhighlight %}

{% include brline %}

## MongoDB

？？？？

{% include brline %}

## Java

### Spring & IoC

IoC 字面意思即为「控制反转」，描述为获得依赖对象的过程被反转了，即“不用看着我了，好了我会告诉你”，
其本质就是一个基于消息驱动的 IO 调度机制。

Spring 是一个使用 IoC 进行管理的容器，其元素是一些被称为 Bean 的节点，此时 Spring 可以看作是一个映射：
键为 bean-id，值在初始化前是一棵 DOM 树，初始化后是一个根据 DOM 树中的全限定类名用反射 new 出来的对象。
该初始化过程也就是老生常谈的「依赖注入」了。

IoC 最重要的贡献在于，将各种访问、回调、监听、封装、单例等繁杂碎琐的工作伴随着去耦合自动化地给完成了。

Spring MVC 基于 Servlet API，着重于对前后端从视图、控制、业务三个方面进行解耦。
该模型使用同步阻塞式 I/O 架构，该架构下每条线程仅仅处理单条请求。

Spring WebFlux 是一套响应式流适配器，这意味着它是惰性的，它是一个异步非阻塞式的 Web 框架

{% include brline %}

## Golang

### gin

gin 本身基于 http-router 进行开发，而 http-router 的体量非常轻量级，核心实现只有寥寥几个 go 文件。
可先从 http-router 的源码入手，可以提供对 gin 这个框架的理解思路。

### xorm

此处描述在程序语言中处理结构化数据对比直接编写 SQL 语句的好处，进而对 xorm 进行评价。

### gRPC

？？？？

## Erlang

OTP 架构与 RPC、有限状态机的相似之处。

gen_server 与 RPC_server 的相似之处。
gen_fsm 与传统 有限状态自动机 的区别。

Erlang processes 与 Golang Goroutine vs Kotlin Coroutine。

Erlang receive 与 Golang select 与 Unix 文件描述符 与 C 句柄 的相似之处。