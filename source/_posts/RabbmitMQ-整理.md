---
title: RabbmitMQ 整理
date: 2019-02-18 10:50:52
categories:
    - 工具
tags:
---

## 基础概念

Broker - 消息队列服务器的实体

Connection - 建立一个 TCP 连接

Channel - 虚拟连接，消息通道，数据流动都是在Channel中进行的，每个 Channel 代表一个会话

Exchange - 接收消息，转发消息到绑定的队列上，指定消息按什么规则，路由到哪个队列

Queue - 消息队列载体，用来存储消息，有自己的 erlang 进程

Binding - 绑定，它的作用就是把 Exchange 和 Queue 按照路由规则绑定起来

Routing Key - 路由关键字，Exchange 根据这个关键字进行消息投递

Vhost - 虚拟主机，一个broker里可以开设多个vhost，用作不同用户的权限分离

Producter - 消息生产者，产生消息的程序

Consumer - 消息消费者，接收消息的程序

Prefetch  Count - 限制 Queue 每次发送给每个消费者的消息数（待消费处理完再重新发送）

MessageQueue - 队列声明
- 消费者是无法订阅或者获取不存在的 MessageQueue 中信息；
- 消息被 Exchange 接受以后，如果没有匹配的 Queue，则会被丢弃；
- 队列属性：Exclusive：排他队列、Auto-delete：自动删除、Durable：持久化。

Message Acknowledgment - 消息回执
- Pub Message 没有 ack
- 如果未启用持久化，服务重启消息会丢失

**备注：**

1. 多个消费者可以订阅同一个 Queue，这时 Queue 中的消息会被平均分摊给多个消费者进行处理；
2. 即使使用持久化，也存在小概率丢失事件，需要在业务逻辑上加上事务或者重试机制；
3. 每个 Queue 中存放的 message 理论上无上限，取决于机器的内存；
4. Channel 是真实 TCP 连接之上的虚拟连接，所有 AMQP 命令都是通过 Channel 发送的；
5. 一个 Channel 只能被单独一个操作系统线程使用，一个操作系统线程上允许使用多个 Channel 。

**为什么使用 Channel，而不是直接使用 TCP 连接？**

建立和关闭 TCP 连接是有代价的，频繁的建立关闭 TCP 连接对于系统的性能有很大的影响，而且 TCP 的连接数也有限制，这也限制了系统处理高并发的能力。在 TCP 连接中建立 Channel 是没有上述代价的。对于 Producer 或者 Consumer 来说，可以并发的使用多个 Channel 进行 Publish 或者 Receive。

大部分的业务操作是在 Channel 这个接口中完成的，包括定义 Queue、定义 Exchange、绑定 Queue 与 Exchange、发布消息等。Channel 之间是完全隔离的。


## 关键组件 - Exchange

Exchange - 接受生产者消息并将消息路由到消息队列（生产者不会直接把消息投给 Queue）

- Routing Key - 指定消息路由规则，决定消息流向哪里；
- Binding - 将 Exchange 与 Queue 关联起来，将消息路由到指定的 Queue；
- Binding Key - 当 binding key 与 routing key 相匹配时，消息将会被路由到对应的 Queue；
- Exchange Type：
  - fanout：广播是式交换器，把所有发送到该 Exchange 的消息投递到所有与它绑定的队列
  - direct：直接交换器，把消息投递到那些 binding key 与 routing key 完全匹配的队列
  - topic：主题交换器，将消息路由到 binding key 与 routing key 模式匹配的队列

**Exchange、Binding**

生产者将消息投递到队列中，实际的情况是，生产者将消息发送到 Exchange（交换器），再通过 Binding 将 Exchange 与 Queue 关联起来。

在绑定（Binding）Exchange 与 Queue 的同时，一般会指定一个 binding key。在绑定多个 Queue 到同一个Exchange 的时候，这些 Binding 允许使用相同的 binding key。

生产者在将消息发送给 Exchange 的时候，一般会指定一个 routing key，来指定这个消息的路由规则，生产者就可以在发送消息给 Exchange 时，通过指定 routing key 来决定消息流向哪里。

## 流程

1. 客户端连接（Connection）到消息队列服务器，打开一个 Channel；
2. 客户端声明一个 Exchange，并设置相关属性；
3. 客户端声明一个 Queue，并设置相关属性；
4. 客户端使用 Routing Key，通过 Bind 在 Exchange 和 Queue 之间建立好绑定关系；
5. 客户端投递消息到 Exchange。


![](https://ws1.sinaimg.cn/large/006tNc79gy1fz8jwvlnjjj315e0m91kx.jpg)


![](https://ws2.sinaimg.cn/large/006tNc79gy1fz8jqc012fj30rs0jj75b.jpg)



## Web 管理说明

引用：https://www.jianshu.com/p/7b6e575fd451

### overview -> Totals

![](https://ws1.sinaimg.cn/large/006tNc79gy1fz8kjtgsfqj314l0e1my0.jpg)


Ready：待消费的消息总数
Unacked：待应答的消息总数
Total：Ready + Unacked


Publish：producter pub 消息的速率
Publisher confirm：broker 确认 pub 消息的速率
Deliver(manual ack)：customer 手动确认的速率
Deliver( auto ack)：customer 自动确认的速率
Consumer ack：customer 正在确认的速率
Redelivered：正在传递 'redelivered' 标志集的消息的速率
Get (manual ack)：响应 basic.get 而要求确认的消息的传输速率
Get (auto ack)：响应于 basic.get 而发送不需要确认的消息的速率
Return：将 basic.return 发送给 producter 的速率
Disk read：queue 从磁盘读取消息的速率
Disk write：queue 从磁盘写入消息的速率


Connections：client 的 tcp 连接的总数
Channels：通道的总数
Exchange：交换器的总数
Queues：队列的总数
Consumers：消费者的总数

### Overview -> Nodes

启动一个 broker 都会产生一个 node

