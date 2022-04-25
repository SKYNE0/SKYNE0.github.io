## RabbitMQ-基础概念

### 1.介绍
- RabbitMQ 是一个由 Relang 语言开发的 AMQP 的开源实现。AMQP（Advanced Message Queue：高级消息队列协议）它是应用层协议的一个开放标准，为面向消息的中间件设计，基于此协议的客户端与消息中间件可传递消息，并不受产品、开发语言等条件的限制。
- 关于AMQP：https://www.rabbitmq.com/tutorials/amqp-concepts.html

### 2.架构
![](/styles/images/mq-1.png)

### 3.基础概念
- 一篇好文：[CloudAMQP: 面向初学者的 RabbitMQ - 什么是 RabbitMQ](https://www.cloudamqp.com/blog/part1-rabbitmq-for-beginners-what-is-rabbitmq.html)

- Broker: 简单理解为: RabbitMQ Server
- Message: 消息由消息头和消息体组成。消息体是不透明的，而消息头则是由一系列的可选属性组成，这些属性包括 routing-key（路由键）、priority（相对于其它消息的优先权）、delivery-mode（指出该消息可能需要持久性存储）等
- Producer: 消息生产者
- Consumer: 消息消费者
- Connection: 生产者或消费者与 `Broker` 之间建立的`TCP`连接
- Channel: 信道是在`connection`基础上建立的逻辑连接，建立和销毁`TCP`连接是非常昂贵的操作，因此引入信道的概念以复用`TCP`连接
- Virtual host: 虚拟主机，一个 VirtualHost 下面有一组不同 Exchnage 与 Queue，不同的 Virtual host 的 Exchnage 与 Queue 之间互相不影响。应用隔离与权限划分，Virtual host 是 RabbitMQ 中最小颗粒的权限单位划分。
- Queue: 消息的容器，一个消息可投入一个或多个队列，消息一直在队列里面，等待消费者连接到这个队列进行消费。
- Binding: exchange 和queue 之间的虚拟连接，binding 中可以包含routingkey。Binding 信息被保存到 exchange 中的查询表中，用于 message 的分发依据
- RabbitMQ Exchange类型: fanout、direct、topic、headers

### 4.Exchange类型
- 消息队列`MQ`的一个重要场景是`解耦合`
- 因此在`RabbitMQ`内部，生产者和消费者也是解耦合的，并通过`Exchange`来传递消息
- `Exchange`根据`Binding Key`、`Routing Key`以及`Headers`属性路由消息。
- 一个`Exchange` 可以绑定多个`Queue`，一个`Queue` 可以绑定多个`Exchange`

#### 4.1 Default Exchange(默认交换机)
- 如果创建队列时未声明交换机，则会使用默认的交换机，并以该队列名作为`Routing Key`,严格匹配
- 可在管理页面查看队列的绑定关系
  
#### 4.2 Fanout Exchange(扇形交换机)
- 扇形交换机不需要根据`Routing Key`路由消息，默认会将消息发送到所有与之绑定的`Queue`上，类似广播
- `Fanout Exchange`的消息投递效率是最高的
- ![](/styles/images/mq-fanout.png)

#### 4.3 Direct Exchange(直连交换机)
- 直连交换机根据路由键传递消息到队列，即消息中的路由键`Routing Key`与交换机中`Binding Key`完全匹配
- ![](/styles/images/mq-direct.jpg)

#### 4.4 Topic Exchange(话题交换机)
- 话题交换机跟直连交换机类似，也是根据路由键传递消息到队列，但`direct`类型的`Routing Key`是完全匹配，`topic`类型则可以模糊匹配
- 支持`#`、`*`两个通配符，`*`匹配一个单词，`#`匹配没有或者多个单词
- demo.*.log: demo.error.log，demo.info.log，demo.warning.log等，不能匹配demo.a.b.log
- demo.#.log: demo.web.error.log，demo.mq.error.log等
- ![](/styles/images/mq-topic.jpg)

#### 4.5 Header Exchange(头信息交换机)
- 头信息交换机使用`Headers`属性代替`Routing Key`进行路由匹配,使用不多，效率较低
- ![](/styles/images/mq-header.jpg)

#### 4.6开发示例
- 官方开发示例：https://www.rabbitmq.com/tutorials/tutorial-four-python.html