## RabbitMQ-镜像集群搭建

### 1.镜像集群、Federation、Shovel
- 官方文档：https://www.rabbitmq.com/clustering.html
- 普通镜像可以提高系统的吞吐量，但不能实现系统的高可用
- 镜像集群是在普通集群的基础上通过设置HA策略实现队列的高可用
- 镜像集群是最常用的，集群是将多个节点连接在一起提高整体的吞吐量和整体的高可用，集群对网络要求较高，常在局域网内组建集群
- Federation(联邦)可以通过点对点的连接进行通信在多个 Broker 节点（或者集群）之间为单个队列提供均衡负载的功能，常用来连接广域网的节点，比如跨机房、跨地域的节点
- Shovel(铲子)与Federation类似，但Shovel工作在更低的层级，Shovel只是简单的从一个Broker上转发消息到另外一个Broker上
- Shovel 的行为就像优秀的客户端应用程序，能够负责连接源和目的地、负责消息的读写及负责连接失败问题的处理

|Federation/Shovel | 集群 |
| :- | :- |
|各个 Broke 节点之间逻缉分离|   逻辑上是个 Broker 节点|
|各个 Broker 节点之间可以运行不同版本的 Erlang 和 RabbitMQ|   各个 Broker 节点之间必须运行相同版本的 Erlang 和 RabbitMQ|
|各个 Broker 节点之间可以在广域网中相连，当然必须要授予适当的用户和权限| 各个 Broker 节点之间必须在可信赖的局域网中相连，通过 Erlang 内部节点传递消息，但节点问需要有相同的 Erlang cookie|
|各个 Broker 节点之间能以任何拓扑逻辑部署，连接可以是单向的或者是双向的 所有 Broker |节点都双向连续所有其他节点|
|从 CAP 理论中选择可用性和分区耐受性，即 AP   |从 CAP 理论中选择致性和可用性，CA|
|一个 Broker 中的交换器可以是 Federation 生成的或者是本地的 |集群中所有 Broker 节点中的交换器都是一样的，要么全有要么全无|
|客户端所能看到它所连接的 Broker 节点上的队列  |客户端连接到集群中的任何 Broker 节点都可以看到所有的队列

### 2.镜像集群
- 单个节点的吞吐量是有限的，简单的提升节点的物理资源并不是最好的办法
- 通过镜像集群是较好的办法，镜像集群极大的提高了系统的可用性、可靠性
- 镜像队列跟其他分布式集群中的副本等概念类似，会有一个Master队列负责消息写入
- 其他的一个或者多个slave队列跟Master队列保持同步
- 当Master队列因其他原因失效时，那么资历最老（基于slave加入cluster的时间排序）的slave会被提升为新的master
  
### 3.集群搭建
- 官方建议镜像集群最少三个节点，我们以三个节点简述集群搭建过程

```
ip1  mqnode1
ip2  mqnode2
ip3  mqnode3
```

#### 3.1 搭建单节点
- 首先搭建好单节点环境，然后将上述对应关系写入hosts中

#### 3.2 配置相同的Erlang Cookie
- 默认在/var/lib/rabbitmq/.erlang.cookie，读取其中一个的值，复制到另外两个节点

#### 3.3 节点类型
- 节点类型分为：磁盘和内存两种
- 磁盘类型将集群所有原数据(队列、 交换器、绑定关系、用户、权限和 vhost等)保存在磁盘上，集群中必须有一个磁盘类型的节点
- 内存类型的所有原数据则保留在内存中
- 内存节点可以提供出色的性能，磁盘节点能够保证集群配置信息的高可靠性
- 加入节点时通过加`--ram`指定内存类型，默认是磁盘类型
- 也可以改变节点类型：`rabbitmqctl stop_app; rabbitmqctl change_cluster_node_type ram`

#### 3.4 加入集群
- 以加入mqnode1为例
- 首先停止mqnode2：rabbitmqctl stop_app
- 重置该节点：rabbitmqctl reset
- 加入mqnode1：rabbitmqctl join_cluster rabbit@mqnode1
- 此时可以在mqnode1的管理页面上看到mqnode2
- 然后启动mqnode2: rabbitmqctl start_app
- mqnode3与此操作相同
- 可通过管理页面或者：rabbitmqctl cluster_status 查看集群状态
  
#### 3.5 剔除节点
- 方法一：集群中任意节点执行：rabbitmqctl forget_cluster_node rabbit@mqnode2 （可以添加 -offline 即使非运行状态也可以生效）
- 方法二：手动重置被剔除节点
  
#### 3.6 集群重启
- 集群停止时应先停止内存节点，最后停止磁盘节点
- 集群启动时与停止时相反，应先启动磁盘节点，最后启动内存节点，最好是先启动最后停止的磁盘节点
- 最后关闭必须是磁盘节点，不然可能回造成集群启动失败、数据丢失等异常情况

### 4. 镜像策略



