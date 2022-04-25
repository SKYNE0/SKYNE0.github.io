## Firewalld常用命令

### 1.Firewalld介绍
- FirewallD 是 iptables 的一个封装，可以让你更容易地管理 iptables 规则
- CentOS 7开始，FirewallD取代iptables作为默认的防火墙管理工具
- FirewallD使用区域和服务的概念，而不是iptables链和规则。根据您将要配置的区域和服务，您可以控制系统允许或禁止的流量
- FirewallD可以使用firewall-cmd命令行实用程序进行配置和管理

### 2.区域(zone)
- 区域是预定义的规则集，用于基于计算机所连接的网络上的信任级别来指定应允许的流量

|  区域名称   | 规则策略  |
| :- | :- |
|public |表示公共区域，不信任网络内其他计算机，仅允许选定的传入连接drop最低级别的信任。所有传入连接都会被丢弃，并且没有回应，仅能有传出的网络连接|
|block |与 drop 类似，但不是简单地丢弃连接，由 icmp-host-prohibited 返回拒绝信息|
|external |通常是启用了 NAT 伪装的外部网络，不信任网络上其他计算机，只接受选定的传入连接internal用于内部网络。信任网络内的其他计算机，仅接受选定的传入连接|
|home |用于家庭区域。信任网络内的其他计算机，只接受选定的传入连接|
|work |用于工作区域。信任网络内的其他计算机，只接受选定的传入连接dmz处于隔离区域的计算机，可通过有限的内部网络进行公开访问。只接受选定的传入连接|
|trusted |最高级别的信任区域，可接受所有的网络连接，信任网络内其他计算机|

- zone相关命令

|  命令   | 说明  |
| :- | :- |
| firewall-cmd  --get-default-zone   | 获取默认 zone |
| firewall-cmd  --set-default-zone=<zone>  | 设置默认 zone |
| firewall-cmd  --get-active-zones     | 获取激活的 zone |
| firewall-cmd  --get-zones   | 获取所有 zone |
| firewall-cmd  --get-zone-of-interface   | 获取绑定指定网卡的 zone |
| firewall-cmd  --list-all-zones   | 获取所有 zone 的配置信息|
| firewall-cmd  --info-zone=<zone>   | 获取指定 zone 的配置信息|

### 2.服务(service)
- Firewalld 默认记录了常用服务所使用的tcp/udp端口
- 相关命令

|  命令   | 说明  |
| :- | :- |
| firewall-cmd  --get-service   | 获取所有 service |
| firewall-cmd   --add-service=<service>  | 添加 service |
| firewall-cmd  --remove-service=<service>     | 移除 service |
| firewall-cmd  --query-service=<service>   | 查询 service |


### 3.端口(port)
- 允许或者拒绝任意端口/协议
- 端口是一定范围，如：9200-9209
- 端口相关操作
- 
|  命令   | 说明  |
| :- | :- |
| firewall-cmd   --add-port=<port>/<protocol>   | 添加端口/协议（TCP/UDP） |
| firewall-cmd  --remove-port=<port>/<protocol>  | 移除端口/协议（TCP/UDP） |
| firewall-cmd  --list-ports  | 查看开放的端口 |

### 4.协议(protocol)
- 允许或者拒绝协议
- 协议相关操作
- 
|  命令   | 说明  |
| :- | :- |
| firewall-cmd   --add-protocol=<protocol>   | 允许协议 (例：icmp，即允许ping)|
| firewall-cmd  --remove-protocol=<protocol>  |  取消协议 |
| firewall-cmd  --list-protocols  | 查看允许的协议 |

### 5.常见场景示例
- 缺省默认的`zone`为`public` 
- 加`--permanent`为持久配置，否则为`运行时`配置,重启失效
- 新增配置后需要`reload`生效

```bash
# 允许指定ip的所有流量
firewall-cmd --add-rich-rule="rule family="ipv4" source address="<ip>" accept"
# 允许指定ip的指定协议
firewall-cmd --add-rich-rule="rule family="ipv4" source address="<ip>" protocol value="<protocol>" accept"
# 允许指定ip访问指定服务
firewall-cmd --add-rich-rule="rule family="ipv4" source address="<ip>" service name="<service name>" accept"
# 允许指定ip访问指定端口, ip可以是网段
firewall-cmd --add-rich-rule="rule family="ipv4" source address="<ip>" port protocol="<port protocol>" port="<port>" accept"
#端口转发
# 开启 NAT 功能
firewall-cmd --zone=public --add-masquerade
# 设置端口转发
firewall-cmd --zone=public --add-forward-port=port="<port>":proto="<protocol>":toport="<port>"
```