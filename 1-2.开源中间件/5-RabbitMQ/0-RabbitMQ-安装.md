## RabbitMQ 安装

### 1.安装部署
- 官方安装指南: https://www.rabbitmq.com/download.html
- CloudAMQP的生产环境部署检查清单：https://www.cloudamqp.com/blog/rabbitmq-checklist-for-production-environments-a-complete-guide.html
- RabbitMQ社区最佳实践: https://www.rabbitmq.com/best-practices.html

#### 1.1传统方式
- https://github.com/rabbitmq/rabbitmq-server/releases/
- https://github.com/erlang/otp/releases
- [RabbitMQ和Erlang版本对照表](https://www.rabbitmq.com/which-erlang.html)
- 可采用`二进制`安装或者`rpm包`安装，推荐使用`rpm包`
- 强烈建议安装`erlang:24`和`rabbitmq:3.8.16`以上版本
- `erlang:24`引入了`JIT`来提高性能: https://blog.rabbitmq.com/posts/2021/03/erlang-24-support-roadmap
- 官方称有`35%~55%`的性能提升
- 但`CentOS7`需要自己编译`erlang:24`

#### 1.2容器方式
- 安装稳定版本的`Docker`:
  
```
curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun

# 配置国内源、存储驱动、日志大小限制
vim /etc/docker/daemon.json
{
    "exec-opts": ["native.cgroupdriver=systemd"],
    "log-driver": "json-file",
    "log-opts": {
        "max-size": "1000m"
        },
    "storage-driver": "overlay2",
    "storage-opts": [
        "overlay2.override_kernel_check=true"
        ],
    "registry-mirrors": [
        "https://registry.docker-cn.com",
        "http://hub-mirror.c.163.com",
        "https://docker.mirrors.ustc.edu.cn"
        ]
}


systemctl start docker
systemctl enable docker
```

- https://hub.docker.com/_/rabbitmq
- 拉取镜像：docker pull 3.8.28-management
- 运行

```bash
docker run --hostname mq01 \
           -p 15672:15672 -p 5672:5672 \
           -e RABBITMQ_DEFAULT_USER=admin \
           -e RABBITMQ_DEFAULT_PASS=admin \
           --name='mq' \
           -d rabbitmq:3.8.28-management
```

- 实际运行时，还应挂载具体的配置文件，数据目录等

### 修改配置
- 官方配置文档：https://www.rabbitmq.com/configure.html
- 修改默认的`存储`和`日志目录`

```
vim /etc/rabbitmq/rabbitmq-env.conf
RABBITMQ_MNESIA_BASE=/path/to/rabbitmq/mnesia
RABBITMQ_LOG_BASE=/path/to/rabbitmq/log
```

- 网络配置调优`可选`

```
# RabbitMQ config
# TCP
tcp_listen_options.backlog = 4096
tcp_listen_options.nodelay = true
tcp_listen_options.linger.on      = true
tcp_listen_options.linger.timeout = 0
tcp_listen_options.sndbuf  = 32768
tcp_listen_options.recbuf  = 32768

# MQTT
mqtt.tcp_listen_options.backlog = 4096
mqtt.tcp_listen_options.nodelay = true
mqtt.tcp_listen_options.linger.on = true
mqtt.tcp_listen_options.linger.timeout = 0
mqtt.tcp_listen_options.sndbuf  = 32768
mqtt.tcp_listen_options.recbuf  = 32768

#STOMP
stomp.tcp_listen_options.backlog = 4096
stomp.tcp_listen_options.nodelay = true
stomp.tcp_listen_options.linger.on  = true
stomp.tcp_listen_options.linger.timeout = 0
stomp.tcp_listen_options.sndbuf  = 32768
stomp.tcp_listen_options.recbuf  = 32768
```

### 控制命令

```bash
systemctl restart rabbitmq-server
systemctl enable rabbitmq-server
systemctl stop rabbitmq-server
systemctl restart rabbitmq-server
```

### 启用管理页面
- `rabbitmq-plugins enable rabbitmq_management`
- 随后在浏览器访问：`http://$ip:15672` 即可进入管理页面

### 创建Admin用户
- 默认只有一个`guest`用户，建议禁止该用户
- 创建`Admin`用户并赋权
  
```
rabbitmqctl add_user  admin  $paaswd
rabbitmqctl set_user_tags admin administrator
rabbitmqctl set_permissions -p / admin  ".*"  ".*"  ".*"
```

- 使用 `Admin` 用户在管理界面再创建其他用户