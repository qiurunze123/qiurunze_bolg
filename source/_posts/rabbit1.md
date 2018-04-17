---
title: 【rabbitmq】rabbitmq之路由讲解
date: 2018-1-14 16:42:20
tags:  搞一波rabbitmq
reward: true
---

# 基础概念

RabbitMQ是一个由erlang开发的基于AMQP（Advanced Message Queue）协议的开源实现。用于在分布式系统中存储转发消息，在易用性、扩展性、高可用性等方面都非常的优秀。是当前最主流的消息中间件之一。

<!--more-->

### 基础信息概念

	Brocker：消息队列服务器实体。
	Exchange：消息交换机，指定消息按什么规则，路由到哪个队列。
	Queue：消息队列，每个消息都会被投入到一个或者多个队列里。
	Binding：绑定，它的作用是把exchange和queue按照路由规则binding起来。
	Routing Key：路由关键字，exchange根据这个关键字进行消息投递。
	Vhost：虚拟主机，一个broker里可以开设多个vhost，用作不用用户的权限分离。
	Producer：消息生产者，就是投递消息的程序。
	Consumer：消息消费者，就是接受消息的程序。
	Channel：消息通道，在客户端的每个连接里，可建立多个channel，每个channel代表一个会话任务。

好处：
1.生产者投递的消息都是面向交换机的。如果没有交换机你至少要维护一个十分庞大的路由表然后从路由表里就会被拆分到多个交换机里面，效果非常好
2.高度的解耦不同的交换机就有不同种的交换机

在rabbitmq里面有四种投递方式：

1.fanout

  路由规则：会把所有的消息发送到该exchange的消息路由到所有与它绑定的queue中相当于广播功能。

2.direct 

  路由规则：该类型消息会把消息发送到routing key 与bindingkey 完全一样的交换机

![](https://i.imgur.com/QEJmDSx.png)
规则：
![](https://i.imgur.com/DiQdqiy.png)

3.topic

  路由规则： 这个模式的是支持正则匹配的，消息发送到topic类型的exchange上时不能随意指定routing_key（一定是指由一系列由点号连接单词的字符串，单词可以是任意的，但一般都会与消息或多或少的有些关联）。Routing key的长度不能超过255个字节。

Binding key也一定要是同样的方式。Topic类型的exchange就像一个直接的交换：一个由生产者指定了确定routing key的消息将会被推送给所有Binding key能与之匹配的消费者。然而这种绑定有两种特殊的情况：

*（星号）：可以（只能）匹配一个单词

*（#井号）：可以匹配多个单词（或者零个）

![](https://i.imgur.com/x65WxSh.png)

路由模式匹配：
![](https://i.imgur.com/YrQK72Z.png)

![](https://i.imgur.com/5OScEj5.png)


在这个例子中，我们将会发送一些描述动物的消息。Routing key的第一个单词是描述速度的，第二个单词是描述颜色的，第三个是描述物种的：“<speed>.<colour>.<species>”。

这里我们创建三个Binding：Binding key为”*.orange.*”的Q1，和binding key为”*.*.rabbit”和”lazy.#”的Q2。

这些binding可以总结为：

Q1对所有橘色的（orange）的动物感兴趣；
Q2希望能拿到所有兔子的（rabbit）信息，还有比较懒惰的（lazy.#）动物信息。

一条以” quick.orange.rabbit”为routing key的消息将会推送到Q1和Q2两个queue上，routing key为“lazy.orange.elephant”的消息同样会被推送到Q1和Q2上

但如果routing key为”quick.orange.fox”的话，消息只会被推送到Q1上；routing key为”lazy.brown.fox”的消息会被推送到Q2上，routing key为"lazy.pink.rabbit”的消息也会被推送到Q2上，但同一条消息只会被推送到Q2上一次。

如果在发送消息时所指定的exchange和routing key在消费者端没有对应的exchange和binding key与之绑定的话，那么这条消息将会被丢弃掉。例如："orange"和"quick.orange.male.rabbit"。但是routing为”lazy.orange.male.rabbit”的消息，将会被推到Q2上。

	Topic类型的exchange：
	
	Topic类型的exchange是很强大的，也可以实现其它类型的exchange。
	
	当一个队列被绑定为binding key为”#”时，它将会接收所有的消息，此时和fanout类型的exchange很像。
	当binding key不包含”*”和”#”时，这时候就很像direct类型的exchange。


4.Headers exchange

首部交换机是忽略routing_key的一种路由方式。路由器和交换机路由的规则是通过Headers信息来交换的，这个有点像HTTP的Headers。将一个交换机声明成首部交换机，绑定一个队列的时候，定义一个Hash的数据结构，消息发送的时候，会携带一组hash数据结构的信息，当Hash的内容匹配上的时候，消息就会被写入队列。

绑定交换机和队列的时候，Hash结构中要求携带一个键“x-match”，这个键的Value可以是any或者all，这代表消息携带的Hash是需要全部匹配(all)，还是仅匹配一个键(any)就可以了。相比直连交换机，首部交换机的优势是匹配的规则不被限定为字符串(string)。

