## RabbitMQ-权限体系

#### 用户角色分类
- 官方文档：https://www.rabbitmq.com/management.html#permissions
- 简述：
	+ none：无法登录控制台,不能访问 management plugin，通常就是普通的生产者和消费者
	+ management：普通管理者, 仅可登陆管理控制台,但无法看到节点信息,也无法对policies进行管理
	+ policymaker：策略制定者, management可以做的任何事外加查看、创建和删除自己的virtual hosts所属的policies和parameters
	+ monitoring：监控者, management可以做的任何事外加：列出所有virtual hosts，包括他们不能登录的virtual hosts、查看其他用户的connections和channels、查看节点级别的数据如clustering和memory使用情况、查看真正的关于所有virtual hosts的全局的统计信息,同时可以查看rabbitmq节点的相关信息(进程数，内存使用情况，磁盘使用情况等)
	+ administrator：超级管理员

- 添加 `vhosts`： `rabbitmqctl add_vhost  vhost`
- 创建用户：`rabbitmqctl add_user user passwd`
- 设置用户角色： `rabbitmqctl set_user_tags administrator`
- 设置用户权限： `rabbitmqctl  set_permissions -p vhost user '.*' '.*' '.*' `
	+ 三个 `.*` 分别对应：资源的配置、写、读权限

- 删除用户： `rabbitmqctl delete_user user`
- 修改密码： `rabbitmqctl change_password user new_passwd`

### 启用常用插件
- 文档：https://www.rabbitmq.com/plugins.html
- 管理界面： `rabbitmq-plugins enable rabbitmq_management`
- 状态监控： `rabbitmq-plugins enable rabbitmq_top`

### 其他
- 其他命令请参考： `rabbitmqctl -h`

```
Users:

   add_user                      Creates a new user in the internal database. This user will have no permissions for any virtual hosts by default.
   authenticate_user             Attempts to authenticate a user. Exits with a non-zero code if authentication fails.
   change_password               Changes the user password
   clear_password                Clears (resets) password and disables password login for a user
   clear_user_limits             Clears user connection/channel limits
   delete_user                   Removes a user from the internal database. Has no effect on users provided by external backends such as LDAP
   list_user_limits              Displays configured user limits
   list_users                    List user names and tags
   set_user_limits               Sets user limits
   set_user_tags                 Sets user tags

Access Control:

   clear_permissions             Revokes user permissions for a vhost
   clear_topic_permissions       Clears user topic permissions for a vhost or exchange
   list_permissions              Lists user permissions in a virtual host
   list_topic_permissions        Lists topic permissions in a virtual host
   list_user_permissions         Lists permissions of a user across all virtual hosts
   list_user_topic_permissions   Lists user topic permissions
   list_vhosts                   Lists virtual hosts
   set_permissions               Sets user permissions for a vhost
   set_topic_permissions         Sets user topic permissions for an exchange

Monitoring, observability and health checks:

   list_bindings                 Lists all bindings on a vhost
   list_channels                 Lists all channels in the node
   list_ciphers                  Lists cipher suites supported by encoding commands
   list_connections              Lists AMQP 0.9.1 connections for the node
   list_consumers                Lists all consumers for a vhost
   list_exchanges                Lists exchanges
   list_hashes                   Lists hash functions supported by encoding commands
   list_node_auth_attempt_stats  Lists authentication attempts on the target node
   list_queues                   Lists queues and their properties
   list_unresponsive_queues      Tests queues to respond within timeout. Lists those which did not respond
   ping                          Checks that the node OS process is up, registered with EPMD and CLI tools can authenticate with it
   report                        Generate a server status report containing a concatenation of all server status information for support purposes
   schema_info                   Lists schema database tables and their properties
   status                        Displays status of a node

Parameters:

   clear_global_parameter        Clears a global runtime parameter
   clear_parameter               Clears a runtime parameter.
   list_global_parameters        Lists global runtime parameters
   list_parameters               Lists runtime parameters for a virtual host
   set_global_parameter          Sets a runtime parameter.
   set_parameter                 Sets a runtime parameter.

Policies:

   clear_operator_policy         Clears an operator policy
   clear_policy                  Clears (removes) a policy
   list_operator_policies        Lists operator policy overrides for a virtual host
   list_policies                 Lists all policies in a virtual host
   set_operator_policy           Sets an operator policy that overrides a subset of arguments in user policies
   set_policy                    Sets or updates a policy

Virtual hosts:

   add_vhost                     Creates a virtual host
   clear_vhost_limits            Clears virtual host limits
   delete_vhost                  Deletes a virtual host
   list_vhost_limits             Displays configured virtual host limits
   restart_vhost                 Restarts a failed vhost data stores and queues
   set_vhost_limits              Sets virtual host limits
   trace_off
   trace_on
```