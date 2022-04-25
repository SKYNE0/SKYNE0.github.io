## Spuervisor

### 1.介绍
- [官方仓库](https://github.com/Supervisor/supervisor)
- [官方文档](http://supervisord.org/)
- `Supervisor`是用`Python`开发的一套通用的进程管理程序，能将一个普通的命令行进程变为后台`daemon`，并监控进程状态，异常退出时能自动重启
- 它是通过`fork/exec`的方式把这些被管理的进程当作`supervisor`的子进程
- Supervisor用途:
  + 管理进程，对进程进行开启、关闭、重启等服务
  + 守护服务进程。当进程关闭后，可以自动重启
  + 管理一组进程，对一组进程同时启动，关闭，重启等服务
  + 提供事件管理，用于管理的进程触发的事件进行报警的功能
  + 提供了对监听同一个unix socket 文件的cgi服务管理
  + 提供web服务界面做服务管理
  + 提供xmlrpc接口做二次开发


### 2.安装
- 包管理工具安装`(推荐)`: 
  + `sudo yum install epel-release`
  + `sudo yum install supervisor`
  + 使用包管理工具安装的, systemd托管、配置文件、logrotate等都会有, pip安装的需要自己手动创建
- pip安装: `sudo pip install supervisor`
- `CentOS7.X`的`epel`源中版本为: `3.4.0`, `pip`安装的则为最新版本`4.2.X`
- 具体需要安装什么版本, 根据自己实际情况安装
- 但`pip`安装没有systemd托管, 需要自己手动创建一下
- 官方的各种发行版的托管文件参考: [Init Scripts](https://github.com/Supervisor/initscripts)
- 控制命令：`systemctl [start | stop | restart | status] supervisord`

```bash
vim /usr/lib/systemd/system/supervisord.service

[Unit]
Description=Supervisor daemon
[Service]
Type=forking
ExecStart=/usr/bin/supervisord -c /etc/supervisord.conf
ExecStop=/usr/bin/supervisord shutdown
ExecReload=/usr/bin/supervisord reload
KillMode=process
Restart=on-failure
RestartSec=42s
[Install]
WantedBy=multi-user.target
```

### 3.配置
- `yum`安装的默认配置文件为 `/etc/supervisord.conf`, 子配置文件`/etc/supervisord.d`
- `pip`安装需要自己手动创建下配置文件目录, 可以使用`echo_supervisord_conf  >  /etc/supervisord/supervisord.conf`生成默认的配置文件

#### 3.1配置文件
- `特别注意`: 使用`echo_supervisord_conf`生成的配置中有很多`/tmp/`目录的, 很明显是不能使用这个路径的，使用`pip`安装一定要改掉这些路径，最好使用`yum`安装
- 主配置文件大致分为几个 `section`, 具体参考官方文档中的[Configuration File](http://supervisord.org/configuration.html)部分
  + `[unix_http_server]`: 配置 `socket` 文件路径和认证、权限, `supervisorctl` 通过该文件与 `supervisord` 通信
  + `[inet_http_server]`: 配置是否启用`HTTP服务器`，默认不启用
  + `[supervisord]`: `supervisord` 进程相关的全局设置
  + `[supervisorctl]`: `supervisorctl` 相关的全局设置
  + `[program:x]`: 子进程相关的设置 `(最好以子文件的方式分门别类的配置, 类似于Nginx)`
  + `[include]`: 配置子配置文件的路径
  + `[group:x]`: 进程组的相关配置 `(好以子文件的方式)`
  + 其他的不再介绍

#### 3.2子进程
- 建议以子配置文件的方式配置单独的`[program:x]`
- 或者将互相关联的配置成进程组`[group:x]`
- 控制命令：`supervisorctl  [start | stop | restart | status] program_name`

```
;[program:theprogramname]
;command=/bin/cat              启动子进程的命令，可以带参数  
;process_name=%(program_name)s 子进程名，默认取`theprogramname`的值
;numprocs=1                    启动子进程的数目
;directory=/tmp                子进程运行目录
;umask=022                     子进程的掩码
;priority=999                  子进程优先级, 数值越小优先级越高
;autostart=true                子进程将在supervisord启动后自动启动
;autorestart=true              子进程挂掉后自动重启, 三个选项，false(不启动),unexpected(取决于exitcodes)和true(重启)
;startsecs=10                  子进程启动的超时时间，超时认为启动失败
;startretries=3                子进程启动失败后的重试次数
;exitcodes=0,2                 子进程退出码
;stopsignal=QUIT               子进程停止信号，可以为TERM, HUP, INT, QUIT, KILL, USR1, or USR2等信号，默认为TERM
;stopwaitsecs=10               向子进程发送stopsignal后，子进程停止超时时间，超时后会直接发送强制kill信号
;stopasgroup=false             向子进程的子进程发送stop信号，避免子进程的子进程变为孤儿进程
;killasgroup=false             与 stopasgroup 类似，不过发送的是 kill 信号
;user=chrism                   子进程启动运行用户
;redirect_stderr=true          将进程的标准输出重定向为标准输出
;stdout_logfile=/a/path        子进程标准输出
;stdout_logfile_maxbytes=1MB   标准输出日志滚动大小，默认50M
;stdout_logfile_backups=10     日志最多保留个数
;stdout_capture_maxbytes=1MB   捕获输出的日志，当事件开启时发送给event_listener （也就是事件监听进程）
;stdout_events_enabled=false   对于标准输出的情况是否发送event
;stdout_syslog=false           是否发送到syslog
;stderr_logfile=/a/path        标准错误输出路径
;stderr_logfile_maxbytes=1MB   最大标准错误输出的日志文件大小（默认1M）
;stderr_logfile_backups=10     日志最多保留个数 （10个）
;stderr_capture_maxbytes=1MB   捕获标准错误输出的日志，当 stderr_events_enabled 开启时发送给event_listener
;stderr_events_enabled=false   对于标准错误输出的情况是否发送event
;stderr_syslog=false           是否发送错误输出日志到syslog
;environment=A="1",B="2"       子进程的环境变量设置，可用的变量有 `group_name`, `host_node_name`, `process_num`, `program_name`, `here`
```

- 如果子进程中还会启动子进程，则必须设置 `stopasgroup=true` 或者 `killasgroup=true`，例如 `Flask` 和 Python 启动的 `multiprocessing` 多进程
  
- 单进程示例:
  
```bash
[program:single]
command=/usr/bin/python single.py
process_name=%(program_name)s
numprocs=1
directory=/opt/pytest/
umask=022
autostart=true
autorestart=true
startsecs=10
startretries=3
exitcodes=0
stopsignal=TERM
stopwaitsecs=10
stopasgroup=false
killasgroup=false
user=pyuser
stdout_logfile=/opt/pytest/pyout.log
stderr_logfile_maxbytes=100MB
stderr_logfile=/opt/pytest/pyout-err.log
stderr_logfile_maxbytes=10MB
```

- 多进程(进程池)示例:
  
```bash
[program:multiple]
command=/usr/bin/python multiple.py
process_name=%(program_name)s_%(process_num)02d  # 进程名类似于：multiple_01  multiple_02 multiple_03
numprocs=3
directory=/opt/pytest/
umask=022
autostart=true
autorestart=true
startsecs=10
startretries=3
exitcodes=0
stopsignal=TERM
stopwaitsecs=10
stopasgroup=false
killasgroup=false
user=pyuser
stdout_logfile=/opt/pytest/pyout.log
stderr_logfile_maxbytes=100MB
stderr_logfile=/opt/pytest/pyout-err.log
stderr_logfile_maxbytes=10MB
```

#### 3.3进程组
- 可以将相互关联的多个`program`分组进行控制
- 控制命令：`supervisorctl  [start | stop | restart | status] group_name:program_name`
- `group_name`后面必须带`:`, 可以使用`*`
- 格式如下：

```bash
vim simple_group.ini

[group:simple_groups]
programs = a_program, b_program, c_program

[program:a_program]
command=/bin/cat               
process_name=%(program_name)s 
numprocs=1                    
directory=/tmp   
...

[program:b_program]
command=/bin/cat               
process_name=%(program_name)s 
numprocs=1                    
directory=/tmp   
...

[program:c_program]
command=/bin/cat               
process_name=%(program_name)s 
numprocs=1                    
directory=/tmp   
...
```

### 4.Supervisorctl常用命令
- 进程管理: `supervisorctl  [start | stop | restart | status] program_name`
- 组管理:`supervisorctl  [start | stop | restart | status] group_name:program_name`
- 查看状态: `supervisorctl status`
- 查看状态: `supervisorctl status`
- 重新启动配置中的所有程序: `supervisorctl reload`
- 更新新的配置到supervisord: `supervisorctl update`

### 5.Web界面
- `supervisor` 在启用 `[inet_http_server]` 时可以通过 `URL` 来访问控制 `Program`
- `supervisorctl` 也可以通过 `-s/--serverurl URL` 来连接到 `supervisord`