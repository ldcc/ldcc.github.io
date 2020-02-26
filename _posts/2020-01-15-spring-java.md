---
layout: post
title: 面试小抄
category: HIDE
date: 2020-01-15
---

在这个喧哗的快节奏时代，无论你身处哪个行业，无论你申请什么 offer，有一个广为人知的面试诀窍那就是：
你的技术有多牛都是次要的，你能在面试官前吹出多少能唬到人的 B 才是最重要的。

<br/>

---

<br/>

<!--
熟悉 Java 主流框架，包括 SpringBoot、MyBatis 等；
熟悉 Dubbo 或 SpringCloud 微服务框架与相关技术；
熟悉主流数据库 mysql、sqlserver、redis、MongoDB 或至少一种数据库
-->

## TCP/IP

<br/>

---

<br/>

## HTTP

<br/>

---

<br/>

## JSON-RPC

<br/>

---

<br/>

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

### DESCRIBE `table-name`

### GRANT [privileges] ON [body] TO [grantee]

{% highlight bnf %}

privileges ::=
    | ALL PRIVILEGES
    | [privilege] <, ...>    

body ::=
    | ['database-name']<.['table-name']>

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
    | field type

type ::=
    | VARCHAR(length)
    | CHAR(length)
    | DATE
    | ...

{% endhighlight %}


### sql build-in function or field 

{% highlight bnf %}

VERSION();
NOW();
CURRENT_DATE;
USER()

{% endhighlight %}

<br/>

---

<br/>

## Spring && IoC

IoC 字面意思即为「控制反转」，描述为获得依赖对象的过程被反转了，即“不用看着我了，好了我会告诉你”，
其本质就是一个基于消息驱动的 IO 调度机制。
Spring 可以看作是一个使用 IoC 进行管理的容器，直觉上可以将 Spring 当作是一个映射。

Spring 中的元素是一些被称为 Bean 的节点，这时 Spring 又可以看作是一个映射：
键是 bean-id，值在初始化前是一棵 DOM 树，初始化后是一个根据 DOM 树中的全限定类名使用反射 new 出来的对象。
这个初始化的过程其实就是「依赖注入」。

IoC 最重要的贡献在于，将各种访问、回调、监听、封装、单例等繁杂碎琐的工作伴随着去耦合自动化地给完成了。

Spring MVC 基于 Servlet API，着重于对前后端从视图、控制、业务三个方面进行解耦。
该模型使用同步阻塞式 I/O 架构，是一种每个线程对应一个请求的模型。

Spring WebFlux 是一套响应式流适配器，这意味着它是惰性的，它是一个异步非阻塞式的 Web 框架

<br/>

---

<br/>

## 同步、异步、阻塞、非阻塞、抢占、非抢占、并发、并行

同步和异步的关系和 IoC 有异曲同工之妙，异步能够让一个执行过程从无法计算的等待时间中解放出来。

阻塞和非阻塞描述的是一个同步执行的过程在等待的过程时的状态：它是否会挂起。

可抢占式内核是指内核是否有权利强行夺回被用户进程占用的 CPU 执行权。内核首先会抢占任务优先级较低的用户进程。

并发是指一个程序被设计出来的性质，并行描述了该程序的执行过程

<br/>

---

<br/>

## 进程、线程、协程
