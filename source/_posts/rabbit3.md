---
title: 【rabbitmq】rabbitmq BasicProperties一些参数说明
date: 2018-1-14 16:42:20
tags:  搞一波rabbitmq
reward: true
---

# 基础概念
BasicProperties 发送者的一些参数说明

<!--more-->

### rabbitmq的BasicProperties
rabbitmq 在Java客户端代码你可以创建AMQP.BasicProperties.Builder 然后使用他去build() 一个 AMQP.BasicProperties

然后可以将这个内置的属性实例用于各种重要的事情。在这个生成器类中有很多“构建器”风格的方法。 


	BasicProperties.Builder propsBuilder = new BasicProperties.Builder();
	propsBuilder
	    .appId(???)       // 产生消息的应用程序的表示符  比如你的计算机名称
    .clusterId(???)	//
    .contentEncoding(???) // 编码格式
    .contentType(???)  //消息内容类型
    .correlationId(???) //关联id 关于此消息 可用于标识客户端之间的的特定id
    .deliveryMode(2) //是否持久化属性
    .expiration(???) //消息过期时间
    .headers(???) //特定消息头
    .messageId(???) //消息表示符 用于标示消息
    .priority(???) //消息优先级
    .timestamp(???) //消息发送的时间戳
    .type(???) //消息类型，例如该消息代表什么类型的事件或命令
    .userId(???); //通过RabbitMQ针对实际连接的用户名 
	•replyTo：一般用来命名一个回调queue。
    •correlationId：用来关联RPC的请求和响应。

	
	for example:


	content_type     = <<"text/plain">>,
                content_encoding = <<"UTF-8">>,
                delivery_mode    = 2,
                priority         = 1,
                correlation_id   = <<"123">>,
                reply_to         = <<"something">>,
                expiration       = <<"my-expiration">>,
                message_id       = <<"M123">>,
                timestamp        = 123456,
                type             = <<"freshly-squeezed">>,
                user_id          = <<"joe">>,
                app_id           = <<"joe's app">>,
                headers          = [{<<"str">>, longstr, <<"foo">>},
                                    {<<"int">>, longstr, 	<<"123">>}]