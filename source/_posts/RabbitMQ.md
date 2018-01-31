---
title: RabbitMQ
date: 2018-01-25 10:39:29
tags:
---
# <center>RabbitMQ基础</center>
---

## 1.RabbitMQ
    rabbitMQ是基于amqp协议的一种消息队列。
---
## 2.Amqp和jms(kafka,activeMQ)的区别

#####  1.通信机制的区别
    JMS:消息生产者和消息消费者必须知道对方的Queue
    AMQP: 消息生产者和消息消费者无须知道对方的Queue,消息生产者将Exchange通过Route key和任意Queue绑定。消息消费者通过Route key从任意Queue中获取Exchange.

#####  2.消息传输机制的区别
    JMS:只支持点对点的通信模式和订阅广播的通信模式。
    Amqp:有4中交换机分别代表着不同的通信模式
        1.Direct直连交换机：直连交换机是一种带路由功能的交互机，一个队列通过routing_key与一个交换机绑定，当消息被发送的时候，需要指定一个routing_key，这个消息被送达交换机的时候，就会被交换机送到指定的队列里面去。同样一个routing_key也是支持应用到多个队列中的，当一个交换机绑定多个队列时，消息就会被送到对应的队列去处理。
        2.Topic主题交换机：直连交换机的routing_key方案非常简单，如果我们希望一条消息发送给多个队列，那么这个交换机需要绑定上非常多的routing_key，假设每个交换机上都绑定一堆的routing_key连接到各个队列上。那么消息队列的管理就会异常的困难所以RabbitMQ提供了一种主题交换机，发送到主题交换机上的消息需要携带指定规则的routing_key，主题交换机会根据这个规则将数据发送到对应的(多个)队列上。
        3.Fanout扇形交换机：扇形交换机是最基本的交换机类型，它做的事情很简单--广播信息。Fanout交换机会把接收到的消息全部转发到绑定的队列上。因为广播不需要“思考”，所以Fanout交换机是四种交换机中速度最快的
        4.Header交换机：设置header attribute参数类型的交换机。(基本不用)
    
## 3.springBoot整合RabbitMQ
#####    1.添加依赖
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-amqp</artifactId>
    </dependency>

#####  2.代码实现（这里还没有具体弄懂exchange绑定queue，感觉怎么样都可以调成功）
