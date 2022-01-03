## Nginx Basic Auth 认证
- [官方文档说明](https://docs.nginx.com/nginx/admin-guide/security-controls/configuring-http-basic-authentication/)
- ngx_http_auth_basic_module模块实现让访问着，只有输入正确的用户密码才允许访问web内容
- 默认情况下nginx已经安装了ngx_http_auth_basic_module模块

### 生成密码
- 可以使用 `htpasswd` 或者 `openssl` 生成，推荐使用 `openssl`
- `htpasswd -c .htpasswd username`
- `openssl passwd -crypt password`
```
cat .htpasswd
user1:$apr1$/woC1jnP$KAh0SsVn5qeSMjTtn0E9Q0
user2:$apr1$QdR8fNLT$vbCEEzDj7LyqCMyNpSoBh/
user3:$apr1$Mr5A0e.U$0j39Hp5FfxRkneklXaMrr/
```

### 修改配置文件
- 可配置段: http, server, location, limit_except
- 示例：
```
server {
    ...
    auth_basic           "Administrator’s Area";
    auth_basic_user_file conf.d/.htpasswd;

    location /public/ {
        auth_basic off;
    }
}
```