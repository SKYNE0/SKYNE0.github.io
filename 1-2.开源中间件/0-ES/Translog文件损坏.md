## Translog文件损坏

### 相关文档
- [elasticsearch-shard](https://www.elastic.co/guide/en/elasticsearch/reference/current/shard-tool.html)

### 故障原因
- 机房突然断电导致 `es` 的 `translog` 文件损坏，分片无法分片
- 通过 `_cluster/allocation/explain` 可以看到具体损坏的 `translog` 文件
- 出现这种情况如果无法从副本或者快照恢复数据，就意味着可能会丢失部分数据甚至是该分片的全部数据

### 故障解决
- 官方用处理 `translog` 文件损坏的工具 `elasticsearch-shard`
- 该工具会将损坏的部分删除

```
es$ bin/elasticsearch-shard remove-corrupted-data -h
Removes corrupted shard files

This tool attempts to detect and remove unrecoverable corrupted data in a shard.
Option                Description
------                -----------
-E <KeyValuePair>     Configure a setting
-d, --dir             Index directory location on disk
-h, --help            show help
--index               Index name
-s, --silent          show minimal output
--shard-id <Integer>  Shard id
-v, --verbose         show verbose output
```

- 有两种方式处理`translog` 文件：
	+ 一是：指定索引和分片号
	+ 二是：指定`translog` 文件完整路径

```
1. bin/elasticsearch-shard remove-corrupted-data --index index_name --shard-id num
2. bin/elasticsearch-shard remove-corrupted-data --dir  translog_file_path
```

- 该工具会将损坏的文件列出来，然后让你选择是否 `remove` 

```
........

  --> translog-1799.ckp
  --> translog-1799.tlog
  --> translog-1800.ckp
  --> translog-1800.tlog
  --> translog-1801.ckp
  --> translog-1801.tlog
  --> translog-1802.ckp
  --> translog-1802.tlog
  --> translog-1803.tlog
  --> translog.ckp

            WARNING:              YOU MAY LOSE DATA.
-----------------------------------------------------------------------
Continue and remove corrupted data from the shard ?
Confirm [y/N] y
Checking existing translog files
```

- 然后根据最后的提示对该分片执行重路由

```
POST /_cluster/reroute'
{
  "commands" : [
    {
      "allocate_stale_primary" : {
        "index" : "index_name",
        "shard" : num,
        "node" : "c7aAKt_2QCC0RwZCj9ZL1Q",
        "accept_data_loss" : false
      }
    }
  ]
}'

You must accept the possibility of data loss by changing parameter `accept_data_loss` to `true`.

```