### 文档
- https://www.elastic.co/guide/en/elasticsearch/reference/index.html
- https://www.elastic.co/guide/en/elasticsearch/reference/7.3/modules-snapshots.html
- https://www.elastic.co/guide/en/elasticsearch/plugins/7.3/repository-s3.html
- https://www.elastic.co/guide/en/elasticsearch/plugins/7.3/repository-s3-repository.html

### MiniO
- 完全兼容AWS S3的私有对象存储系统
- 安装：https://min.io/download#/kubernetes
- 安装完成后创建 Buckets 和对应的key、Secret 

### ES 集群
- 共享文件存储(NFS\Moosefs)等在数据量较大的情况下表现不佳，且扩容困难
- 公有云提供的对象存储和HDFS是较好的选择
- 安装插件 S3 插件： `bin/elasticsearch-plugin install repository-s3`
- 也可以插件目录放置对应安装目录的 `plugins` 目录
- 重启节点

### 导入秘钥
- 所有节点都需要安装插件和导入秘钥操作
```
bin/elasticsearch-keystore add s3.client.default.access_key
bin/elasticsearch-keystore add s3.client.default.secret_key
# MiniO还需要导入endpoint和protocol
bin/elasticsearch-keystore add s3.client.default.endpoint
bin/elasticsearch-keystore add s3.client.default.protocol
```
- 也可以不导入秘钥，但需在 `config/jvm.options` 中追加 `-Des.allow_insecure_settings=true`
- 但此方式不安全
- 使用该方式后，在Kibana可直接创建快照库

### 创建快照库
```
PUT _snapshot/s3-esbk-2022
{
   "type": "s3",
   "settings": {
      "bucket": "esbk-2022",
      "base_path": "2022",
      "access_key": "XXXXXXXXXXXXXX",
      "secret_key": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
      "endpoint": "XXXXXXXXX:9000",
      "path_style_access": "true",
      "protocol": "http"
   }
}
```