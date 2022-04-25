## RabbitMQ-PrefetchCount(预取数)

### PrefetchCount
- 下面这两篇文章很好的解释了`prefetch_count`的作用
- 在多消费者情况建议客户端设置`prefetch_count`参数优化消费者消费

- 如何优化 RabbitMQ 预取计数: https://www.cloudamqp.com/blog/how-to-optimize-the-rabbitmq-prefetch-count.html
- RabbitMQ Prefetch Count: https://www.rabbitmq.com/consumer-prefetch.html
- Finding bottlenecks with RabbitMQ: https://blog.rabbitmq.com/posts/2014/04/finding-bottlenecks-with-rabbitmq-3-3

### 全局设置PrefetchCount
- 除了客户端可以自定义`prefetch_count`外，也可以在服务端设置默认缺省的`prefetch_count`
- 有两种方式：
  + 临时: `rabbitmqctl eval 'application:set_env(rabbit, default_consumer_prefetch, {false, 200}).'`
  + 配置文件:
    - 在主配置文件目录
  
```bash
vim  advanced.config
[
 {rabbit, [
       {default_consumer_prefetch, {false,200}}
     ]
 }
].
```
- `advanced.config`文件可配置经典格式的配置：https://www.rabbitmq.com/configure.html
- 通过该方式即可设置缺省的`prefetch_count`，但具体需要设置什么样的值，需要自己根据自己的场景和实际情况
