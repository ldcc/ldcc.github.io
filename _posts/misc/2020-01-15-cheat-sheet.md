---
layout: post
title: 面试小抄
categories: 荤菜
tags: cheat-sheet interview basic-computer-knowledge 
date: 2020-01-15
hide: false
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

SSL/TSL 的加密理论与技术支持最早可追溯到高斯、费马、欧拉等人，其安全由「素数因素分解」本身的数学难度所保障。

此处可用魔术师洗牌例子解释 SSL/TSL。 

此处可先实现高级语言层面的 HTTP 协议包的通信模拟与应用于该协议包的 SSL/TSL 加密算法再继续编写。

{% include brline %}

## SOCKETS

？？？？

{% include brline %}

## RESTFUL

RESTful 的哲学就是一切皆资源，而一切资源的发现都要通过一个「地址」和「路径」进行索引。
RESTful 原则下定位到某个资源后，需要通过一些约定成俗的表示方法才能对数据进行操作。

HTTP 报文中保留了四个字节用于表示一次 RESTful 中所执行的动作，该四个字节通常为被称为「HTTP 动作」。

常用的 HTTP 动作有 7 个：

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

## Peer to Peer

此处可先实现一个小型的分布式通信集群再继续编写。

此后可拓展实现分布式种子技术，DHT 资源索引技术等 P2P 应用。

加入一个分布式环境中存在两个胡不相连的集群，DHT 可能会失效，
所以一个完全去中心化集群的节点在启动时会需要一个初始节点作为枢纽去连接其它节点。
枢纽节点保证了分布式环境中不可能存在两个完全不相连的集群。

在 DHT 环境下，每当新加入一个节点，就有可能需要对大量的逻辑距离相近的节点进行检查，
并更新其中的一份或多份「资源元数据」储存位置，因为新加入的节点可能满足这些资源的最短逻辑距离。

{% include brline %}

## JSON-RPC

RPC 理论上只要有一个程序语言，你就可以发送这语言的代码到一个「数据服务器」。
接着服务器接受并执行这代码，对数据进行索引、查询或重构，最后返回结果给客户端。

JSON-RPC 是 RPC 的一种传输协议，其传递内容的主体为 JSON。
JSON 格式的数据嵌套语法可以非常方便地在内容中定义调用的函数名称、参数等数据。
不同语言接收到该 JSON 后再根据各自的实现处理该次调用，以此实现跨语言调用的效果。

{% include brline %}

## Thread & Coroutine & Continuation

Coroutine 本质上是一个能够储存「执行过程」的队列，在一些 Lisp-like 语言中通常又叫作 Continuation，
Golang 为了强调自己的 Coroutine 与其它传统 Coroutine 在实现上的某些区别，自己取了个名字叫 Goroutine。

Coroutine 或者 Goroutine 说白了就是些用户级的 Continuation，系统级的 Continuation 通常被叫做「线程」。

用过 Node.js 的人几乎都体验过那种需要你传入一个或多个函数，然后你的函数就在各种库之间跳来跳去的 Callback 地狱，
其实这只是是函数式语言里面常用的一种手法，叫做 Continuation Passing Style(CPS)。

由于 Scheme 有 call/cc，所以从理论上讲，它可以不通过 CPS 样式的代码而实现大并发。
换句话说函数式语言只要支持 Continuation，其实都可以很容易的实现大并发。

{% include brline %}

## Spring & IoC

IoC 字面意思即为「控制反转」，描述为获得依赖对象的过程被反转了，即“不用看着我了，好了我会告诉你”，
其本质就是一个基于消息驱动的 IO 调度机制。

Spring 是一个使用 IoC 进行管理的容器，其元素是一些被称为 Bean 的节点，此时 Spring 可以看作是一个映射：
键为 bean-id，值在初始化前是一棵 DOM 树，初始化后是一个根据 DOM 树中的全限定类名用反射 new 出来的对象。
该初始化过程也就是老生常谈的「依赖注入」了。

IoC 最重要的贡献在于，将各种访问、回调、监听、封装、单例等繁杂碎琐的工作伴随着去耦合自动化地给完成了。

Spring MVC 基于 Servlet API，着重于对前后端从视图、控制、业务三个方面进行解耦。
该模型使用同步阻塞式 I/O 架构，该架构下每条线程仅仅处理单条请求。

Spring WebFlux 是一套响应式流适配器，这意味着它是惰性的，它是一个异步非阻塞式的 Web 框架
