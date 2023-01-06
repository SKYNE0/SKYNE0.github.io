### 文档
- https://github.com/rclone/rclone
- https://rclone.org/
- https://rclone.org/install/
- https://rclone.org/docs/
- https://rclone.org/commands/

### 安装
- 略

### 配置
- `rclone config` 按提示配置 `source` 、 `target`
- 直接创建默认配置文件(推荐)：`mkdir $HOME/.config/rclone/rclone.conf`
- 参考示例：

```
[target]
type = s3
provider = Alibaba
env_auth = false
access_key_id = XXXXXXXXXXXX
secret_access_key = XXXXXXXXXXXXXX
endpoint = oss-cn-shanghai.aliyuncs.com

[source]
type = s3
provider = Minio
env_auth = false
access_key_id = XXXXXXXXXXXXXX
secret_access_key = XXXXXXXXXXXXXXXX
endpoint = http://XXXXXXXXXXXXXX:9000
region = cn-east-1
```
- 本地MiniO，同步至阿里云OSS
- rclone  -P sync --checksum --update --use-server-modtime source:bucket  target:bucket