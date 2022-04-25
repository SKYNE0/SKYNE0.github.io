## RabbitMQ-压测

### 压测工具
- 官方压测工具：Perf-Test
- 下载二进制文件：https://github.com/rabbitmq/rabbitmq-perf-test/releases
- 官方文档：https://rabbitmq.github.io/rabbitmq-perf-test/stable/htmlsingle/#from-binary

### 使用方式
- 常用参数说明

```bash
-x: （生产者数量）
-y: （消费者数量）
-u: （队列名称）
-a:    autoack auto ack，客户端在处理完messages之后会给服务端返回一个ack确认信息，服务端在收到该ack信息之后才会把messages删除；
-d: （本次测试的编号，身份标识）
-s: （消息大小，单位是字节）
-f: （消息标志，多个可以用逗号隔开，支持的值：persistent 和 mandatory）
-A: （每多少条消息返回一次ack信息给服务器端）
-q: （消费者预读取的数量）
-c: （未确认消息发布的最大值）
-C: （生产者要生成的消息的数量，译者注：在此次性能测试中，生产者一旦生成了指定数量的消息，就会停止。）
-r: （生产者速度限制）
-R:（消费者速度限制）
-z: （运行时间，单位是秒（默认没有限制））。
-p: （允许使用预定义的对象）
-D: （消费者要消费的消息数量，也就是指定这次测试中消费者一共要消费多少条消息，一旦消费者消费了这么多条消息，消费者就会被停止）
-qa:queue-args <arg> queue  队列参数键值对，使用逗号隔开，例如x-max-length=10
-mp:  消息属性以逗号隔开的键值对方式，比如priority=5.
-B: 使用逗号分隔的文件列表，用在消息的内容中（这里是多个由逗号分隔开的文件名，把文件中的内容作为消息体写入到指定queue中）
-T: 消息体内容类型
-qp:  按顺序依次创建的队列名称模式
-F:  队列模式的开始（包含）
-T: 队列模式的结束（包含）
-hst:heartbeat-sender-threads <arg>  生产者和消费者的心跳发送者的线程数量
-ms: 是否收集延迟时间，单位毫秒。默认是关闭的。如果生产者和消费者在不同的主机上运行，那么就将其设置成为true。
-p: 允许使用预先声明的对象
```

### 使用示例
- [官方文档](https://rabbitmq.github.io/rabbitmq-perf-test/stable/htmlsingle/#from-binary)有详细的使用示例
