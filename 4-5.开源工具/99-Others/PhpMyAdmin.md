## PhpMyadmin

### 参考文档
- https://github.com/phpmyadmin/phpmyadmin
- https://hub.docker.com/phpmyadmin
- https://www.phpmyadmin.net/
- https://docs.phpmyadmin.net/zh_CN/latest/
- https://docs.phpmyadmin.net/zh_CN/latest/setup.html#securing-your-phpmyadmin-installation

### 安装方式
- 推荐使用 `Docker` 部署
- `docker run --name pma -d -e PMA_ARBITRARY=1 -p 8080:80 phpmyadmin`
- 已有 `Php `环境的可以源码部署

### 安装环境
- CentOS: 7.5
- PHP: 7.3
- Nginx： 1.20+
- Firewalld: enable

### 安装步骤
- `Php`和`Nginx`安装过程：略
- 下载最新 `stable版本`, [下载地址](https://www.phpmyadmin.net/downloads/)
- 源代码解压后放置固定目录
- 添加`Nginx`配置文件

```bash
 server {
      listen 8088;
      server_name 127.0.0.1;
      index index.html index.php;
      root   /alidata/www/phpMyAdmin;

      try_files $uri $uri/ @rewrite;
      location @rewrite {
         rewrite ^/(.*)$ /index.php/$1 last;
      }

      access_log  /alidata/logs/nginx/access/t-phpMyAdmin.log;
      error_log /alidata/logs/nginx/error/t-phpMyAdmin-error.log;

      location ~ \.php {
         fastcgi_index  /index.php;
         fastcgi_pass   127.0.0.1:9000;
         include fastcgi_params;
         fastcgi_split_path_info       ^(.+\.php)(/.+)$;
         fastcgi_param PATH_INFO       $fastcgi_path_info;
         fastcgi_param PATH_TRANSLATED $document_root$fastcgi_path_info;
         fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
      }

      location ^~ /(.ht|.htaccess|.git|README.md|composer.json) {
           deny all;
      }

    }
```

### 认证方式
- HTTP: 类似于`Cookie`，基于`php`的`http`认证机制
- Cookie: 基于`Cookie/Session` 来存储用户会话，用户退出后，会话过期
- Signon: 单点登录方式
- Config: 配置文件中配置`MySQL`连接信息
- `安全性`： Signon > Cookie > HTTP > Config

### Cookie配置方式
- `phpMyAdmin`的默认配置文件为: `config.inc.php`
- `cp config.sample.inc.php config.inc.php`
- 默认是`Cookie`认证方式, 需要输入`MySQL`用户名密码登录, 默认是连接`本机MySQL`
- `$cfg['Servers'][$i]['auth_type'] = 'cookie';`
- 同时用于`Session`加密的短语需要设置一下
- `$cfg['blowfish_secret'] = 'asda23123123asdfasd123123123sdasdasd';`
- 如果需要使之可以连接`任意地址`的`MySQL`, 则需要启用 `允许自定义服务器`
```
vim libraries/config.default.php
$cfg['AllowArbitraryServer'] = true;
```

### 安全加固
- 参考[官方文档](https://docs.phpmyadmin.net/zh_CN/latest/setup.html#securing-your-phpmyadmin-installation)对其加固, 特别是放于公网的

### 高级配置
- 高级配置创建了一个单独的库来记录相关数据
- 自己根据需要是否启用高级配置