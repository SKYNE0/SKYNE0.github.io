## Systemd常用命令

### 1.Systemd
- `Systemd` 服务是一种以 `.service` 结尾的单元（unit）配置文件，用于控制由Systemd 控制或监视的进程。简单说，用于后台以守护精灵（daemon）的形式运行程序
- `Systemd` 主要是从来看系统状态、服务状态，以及管理系统和服务

### 2.配置文件
- `Systemd`的配置文件主要存放在三个地方:
  + /lib/systemd/system/: 软件安装的时候配置文件安装的位置，默认是不需要修改的。`(最常用)`
  + /run/systemd/system/: 系统执行过程中产生的服务脚本， 这个目录下的脚本内容可以覆盖上面那个脚本的内容
  + /etc/systemd/system/: 这个是systemd实际运行的脚本。一般自动运行的服务都会在这里创建一个链接指向前面两个的位置，systemd启动的时候会依旧当前目录的文件依次启动服务。

### 3.服务单元Unit分类
- `Systemd` 可以管理所有系统资源。不同的资源统称为 `Unit`（单位）
- `Unit` 一共分成12种，这里只介绍下 `Service unit`

| 类型 | 说明 |
| :- |  :- |
|Service unit|  系统服务|
|Target unit|  多个 Unit 构成的一个组|
|Device Unit|  硬件设备|
|Mount Unit|  文件系统的挂载点|
|Automount Unit|  自动挂载点|
|Path Unit|  文件或路径|
|Scope Unit|  不是由 Systemd 启动的外部进程|
|Slice Unit|  进程组|
|Snapshot Unit|  Systemd 快照，可以切回某个快照|
|Socket Unit|  进程间通信的 socket|
|Swap Unit|  swap 文件|
|Timer Unit|  定时器|

### 4.Service Unit Service Unit
- `Service Unit` 是最常见、最普遍、使用最多的
- 配置文件由几个区块 `Section` 组成，区块内是等号连接的`键值对`

```
[Section]
key1=value1
key2=value2
```

- `Service Unit` 通常由 `[Unit]`、`[Service]`、`[Install]` 组成
  + [Unit]: 区块通常是配置文件的第一个区块，用来定义 Unit 的元数据，以及配置与其他 Unit 的关系。它的主要字段如下

```
Description：简短描述
Documentation：文档地址
Requires：当前 Unit 依赖的其他 Unit，如果它们没有运行，当前 Unit 会启动失败
Wants：与当前 Unit 配合的其他 Unit，如果它们没有运行，当前 Unit 不会启动失败
BindsTo：与Requires类似，它指定的 Unit 如果退出，会导致当前 Unit 停止运行
Before：如果该字段指定的 Unit 也要启动，那么必须在当前 Unit 之后启动
After：如果该字段指定的 Unit 也要启动，那么必须在当前 Unit 之前启动
Conflicts：这里指定的 Unit 不能与当前 Unit 同时运行
Condition...：当前 Unit 运行必须满足的条件，否则不会运行
Assert...：当前 Unit 运行必须满足的条件，否则会报启动失败
```

  + [Service]: 区块用来 Service 的配置，只有 Service 类型的 Unit 才有这个区块。它的主要字段如下

```
Type：定义启动时的进程行为。它有以下几种值。
Type=simple：默认值，执行ExecStart指定的命令，启动主进程
Type=forking：以 fork 方式从父进程创建子进程，创建后父进程会立即退出
Type=oneshot：一次性进程，Systemd 会等当前服务退出，再继续往下执行
Type=dbus：当前服务通过D-Bus启动
Type=notify：当前服务启动完毕，会通知Systemd，再继续往下执行
Type=idle：若有其他任务执行完毕，当前服务才会运行
ExecStart：启动当前服务的命令
ExecStartPre：启动当前服务之前执行的命令
ExecStartPost：启动当前服务之后执行的命令
ExecReload：重启当前服务时执行的命令
ExecStop：停止当前服务时执行的命令
ExecStopPost：停止当其服务之后执行的命令
RestartSec：自动重启当前服务间隔的秒数
Restart：定义何种情况 Systemd 会自动重启当前服务，可能的值包括always（总是重启）、on-success、on-failure、on-abnormal、on-abort、on-watchdog
TimeoutSec：定义 Systemd 停止当前服务之前等待的秒数
Environment：指定环境变量
```

  + [Install]: 通常是配置文件的最后一个区块，用来定义如何启动，以及是否开机启动。它的主要字段如下

```
WantedBy：它的值是一个或多个 Target，当前 Unit 激活时（enable）符号链接会放入/etc/systemd/system目录下面以 Target 名 + .wants后缀构成的子目录中
RequiredBy：它的值是一个或多个 Target，当前 Unit 激活时，符号链接会放入/etc/systemd/system目录下面以 Target 名 + .required后缀构成的子目录中
Alias：当前 Unit 可用于启动的别名
Also：当前 Unit 激活（enable）时，会被同时激活的其他 Unit
```
- 示例:

```
[Unit]
Description=ssdb persistent key-value database
After=network.target

[Service]
Type=simple
ExecStart=/alidata/server/ssdb/bin/ssdb-server  /alidata/server/ssdb/conf/ssdb.conf
User=root
Group=root
# open files
LimitNOFILE=64000
# processes/threads
LimitNPROC=64000

[Install]
WantedBy=multi-user.target
```

### 5.常用命令
- 1.电源管理

```
systemctl reboot         # 重启系统
systemctl poweroff       # 关闭系统，切断电源
systemctl halt           # CPU停止工作
systemctl suspend        # 暂停系统
systemctl hibernate      # 让系统进入冬眠状态
systemctl hybrid-sleep   # 让系统进入交互式休眠状态
systemctl rescue         # 启动进入救援状态（单用户状态）
```

- 2.启动耗时查询

```
systemd-analyze               # 查看启动耗时
systemd-analyze blame         # 查看每个服务的启动耗时
systemd-analyze critical-chain    # 显示瀑布状的启动过程流
systemd-analyze critical-chain unit.service    # 显示指定服务的启动流
```

- 3.服务管理

```
# 启动服务
systemctl start unit.service

# 查看服务状态
systemctl status unit.service

# 停止服务
systemctl stop unit.service

# 重启服务
systemctl restart unit.service

# 显示某个 Unit 是否正在运行
systemctl is-active unit.service

# 显示某个 Unit 是否处于启动失败状态
systemctl is-failed unit.service

# 显示某个 Unit 服务是否建立了启动链接
systemctl is-enabled unit.service

# 重新读取配置文件(如果该服务不能重启，但又必须使用新的配置，这条命令会很有用)
systemctl reload unit.service

# 使服务开机自启动
systemctl enable unit.service

# 使服务不要开机自启动
systemctl disable unit.service

# 禁用服务(这可以防止服务被其他服务间接启动，也无法通过 start 或 restart 命令来启动服务)
systemctl mask unit.service

# 启用服务(仅针对于已禁用的服务)
systemctl unmask unit.service

# 重新读取所有服务项(修改、添加、删除服务项之后需要执行以下命令)
systemctl daemon-reload

```

### 6.日志管理
- `Systemd` 统一管理所有 `Unit` 的启动日志。带来的好处就是，可以只用`journalctl`一个命令，查看所有日志（内核日志和应用日志）。日志的配置文件是 `/etc/systemd/journald.conf`
- 常见用法

```
# 查看所有日志（默认情况下 ，只保存本次启动的日志）
journalctl

# 查看内核日志（不显示应用日志）
journalctl -k

# 查看系统本次启动的日志
journalctl -b
journalctl -b -0

# 查看上一次启动的日志（需更改设置）
journalctl -b -1

# 查看指定时间的日志
journalctl --since="2012-10-30 18:17:16"
journalctl --since "20 min ago"
journalctl --since yesterday
journalctl --since "2015-01-10" --until "2015-01-11 03:00"
journalctl --since 09:00 --until "1 hour ago"

# 显示尾部的最新10行日志
journalctl -n

# 显示尾部指定行数的日志
journalctl -n 20

# 实时滚动显示最新日志
journalctl -f

# 查看指定服务的日志
journalctl /usr/lib/systemd/systemd

# 查看指定进程的日志
journalctl _PID=1

# 查看某个路径的脚本的日志
journalctl /usr/bin/bash

# 查看指定用户的日志
journalctl _UID=33 --since today

# 查看某个 Unit 的日志
journalctl -u nginx.service
journalctl -u nginx.service --since today

# 实时滚动显示某个 Unit 的最新日志
journalctl -u nginx.service -f
```
