---
title: 【rabbitmq】rabbitmq之事务与确认机制
date: 2018-1-14 16:42:20
tags:  搞一波rabbitmq
reward: true
---

# 基础概念
rabbitmq的事务机制是为了什么产生的？

① 发送的消息到底是否正确的到达了代理服务器

②如果在到达代理服务器之前就出现了问题那么持久化也就无法弥补损失


我们如何处理？？

<!--more-->

### rabbitmq的事务机制

与rabbitmq相关的事务处理方法有三个：

	① amqp channel.txselect  作用 ： 开启事务
	
	② channel.txcommit 作用 ：提交事务
	
	③ channel.rollback 作用： 回滚事务

通过txselect开始事务后，便可以交换机发送消息，如果txcommit提交成功则消息一定可以到达broker ， 如果在txcommit执行之前崩溃或者其他原因抛出异常，我们便可以捕捉异常回滚！

完整代码：

	try {
    channel.txSelect();
    channel.basicPublish(exchange, routingKey, MessageProperties.PERSISTENT_TEXT_PLAIN, msg.getBytes());
    int result = 1 / 0;
    channel.txCommit();
	} catch (Exception e) {
    e.printStackTrace();
    channel.txRollback();
	}


步骤：

	可以看到带事务的多了四个步骤：
	client发送Tx.Select
	broker发送Tx.Select-Ok(之后publish)
	client发送Tx.Commit
	broker发送Tx.Commit-Ok

如果发生异常事务就会滚，解决了在未到达交换机之前就出现错误，只有消息被成功接收事务才能提交成功否则我们便可以在捕捉异常后进行回滚重发，但是使用事务机制的话会降低rabbit的性能所有mq提供了一个更好的方案！

### confirm机制

生产者将信息通道设置成confirm 的模式，该信息在上面发布的消息都会被指派一个唯一的ID，一旦消息被投递到所有匹配的队列之后，broker就会发送一个确认给生产者(包含消息的唯一id） 这就使得生产者已经正确的到了目的地，如果消息和队列是可持久化的，那么确认消息会将消息写入磁盘之后发出，broker回传给生产者的确认消息中deliver-tag域包含了确认消息的序列号，此外broker也可以设置basic.ack的multiple域，表示到这个序列号之前的所有消息都已经得到了处理。


最大的好处就是异步：

 如果RabbitMQ因为自身内部错误导致消息丢失，就会发送一条nack消息，生产者应用程序同样可以在回调方法中处理该nack消息。

	try {
			channel = this.createChannel();

			channel.confirmSelect();
			String messageId = this.sendMessage(routingKey,messageBody,channel);
			channel.waitForConfirmsOrDie();
			RabbitLOG.log(this.businessType.getRabbitKey(), messageId, true, messageBody);
		} catch (InterruptedException e) {
			RabbitLOG.log(this.businessType.getRabbitKey(), "", false, messageBody);
			throw new MQException("发送后,等待确认消息时报错", e);
		} catch (IOException e) {
			throw new MQException("发送前，设置confirm监听器时出错", e);
		} finally {
			try {
				channel.close();
			} catch (IOException e) {
				LOGGER.error("关闭channel时出现错误", e);
			}
		}

消费者还有一个确认机制：

消费者在声明队列时，可以指定noAck参数，当noAck=false时，RabbitMQ会等待消费者显式发回ack信号后才从内存(和磁盘，如果是持久化消息的话)中移去消息。否则，RabbitMQ会在队列中消息被消费后立即删除它。


如果noack=false 的话那么，队列中的消息分成了两部分：一部分是等待投递给消费者的消息；一部分是已经投递给消费者，但是还没有收到消费者ack信号的消息。如果服务器端一直没有收到消费者的ack信号，并且消费此消息的消费者已经断开连接，则服务器端会安排该消息重新进入队列，等待投递给下一个消费者（也可能还是原来的那个消费者）



有些时候我们应该把它设置为true 这样就不会报错后一直等待然后打印日志导致内存饱满，可以写一个邮件提醒挥着直接记录日志然后找到错误的原因！