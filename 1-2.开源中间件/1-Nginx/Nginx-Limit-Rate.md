## Nginx 限流限速

### 限流限速
- [官方文档](https://www.nginx.com/blog/rate-limiting-nginx)
- 模块: http_limit_conn_module, http_limit_req_module
- 上述模块默认已启用,无需额外编译安装
- 原理参考文档
- 默认返回`503`状态码
- 最好结合`burst` 和 `nodelay` 参数合理应对突发流量

### 示例

```
limit_req_zone $binary_remote_addr zone=limit_req:20m rate=20r/s;
limit_req zone=limit_req burst=10 nodelay;
```