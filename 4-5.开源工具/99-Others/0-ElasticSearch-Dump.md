## ElasticSearch-Dump 安装使用
> 推荐参考[官方仓库](https://github.com/elasticsearch-dump/elasticsearch-dump)安装使用

### 1.安装NodeJS
- 推荐安装 `V10+` 以上的 `node` 版本
- Windows：下载安装即可：[下载地址](https://nodejs.org/en/download/releases/) `(推荐12以上版本)`
- Linux(CentOS7):  `curl -fsSL https://rpm.nodesource.com/setup_12.x | bash -` 
- Linux(Ubuntu):  `curl -sL https://deb.nodesource.com/setup_12.x | bash -`
- 安装完成后检验：

```
$ node -v
v12.22.8
$ npm -v
6.14.15

```  

- 配置国内`NPM`淘宝源：
- `npm config set registry https://registry.npm.taobao.org`
- `npm config get registry`

### 2.安装ElasticSearch-Dump
- `local`
  + npm install elasticdump
  + ./bin/elasticdump --version

- `global(推荐)`
  + npm install elasticdump -g
  + elasticdump --version


### 3.简单使用
- 推荐使用`tmux`来执行任务, 防止任务`异常中断`又找不到日志输出
- `elasticdump` 通过读取一个 `input` 来导入一个 `output`
- 命令格式: `elasticdump --input SOURCE --output DESTINATION [OPTIONS]`
  + `input SOURCE` 表示读取数据源
  + `output DESTINATION`表示将数据源传输到目的地
  + `SOURCE/DESTINATION`两者都可以是`ES集群`的URL或文件
  + `OPTIONS`是操作选项，比较常用有`type`和`limit`
- `OPTIONS`推荐:
  + `--limit`: 单次取多少个对象，默认100，可根据实际情况增加
  + `--noRefresh`:导入时禁用Refresh，根据实际情况是否使用
  + `--offset`: 任务异常中断时可以用，指定从什么位置重新开始

- 支持的`数据类型`如下:

|Type 类型| 说明|
| :-: | :-: |
| mapping| 索引映射结构|
| data| 索引数据|
| setting| 索引设置|
| analyzer| 分词器|
| template| 索引模板|
| alias| 别名|
- 官方示例:

```bash
# Copy an index from production to staging with analyzer and mapping:
elasticdump \
  --input=http://production.es.com:9200/my_index \
  --output=http://staging.es.com:9200/my_index \
  --type=analyzer
elasticdump \
  --input=http://production.es.com:9200/my_index \
  --output=http://staging.es.com:9200/my_index \
  --type=mapping
elasticdump \
  --input=http://production.es.com:9200/my_index \
  --output=http://staging.es.com:9200/my_index \
  --type=data

# Backup index data to a file:
elasticdump \
  --input=http://production.es.com:9200/my_index \
  --output=/data/my_index_mapping.json \
  --type=mapping
elasticdump \
  --input=http://production.es.com:9200/my_index \
  --output=/data/my_index.json \
  --type=data

# Backup and index to a gzip using stdout:
elasticdump \
  --input=http://production.es.com:9200/my_index \
  --output=$ \
  | gzip > /data/my_index.json.gz

# Backup the results of a query to a file
elasticdump \
  --input=http://production.es.com:9200/my_index \
  --output=query.json \
  --searchBody="{\"query\":{\"term\":{\"username\": \"admin\"}}}"
  
# Specify searchBody from a file
elasticdump \
  --input=http://production.es.com:9200/my_index \
  --output=query.json \
  --searchBody=@/data/searchbody.json  

# Copy a single shard data:
elasticdump \
  --input=http://es.com:9200/api \
  --output=http://es.com:9200/api2 \
  --input-params="{\"preference\":\"_shards:0\"}"

# Backup aliases to a file
elasticdump \
  --input=http://es.com:9200/index-name/alias-filter \
  --output=alias.json \
  --type=alias

# Import aliases into ES
elasticdump \
  --input=./alias.json \
  --output=http://es.com:9200 \
  --type=alias

# Backup templates to a file
elasticdump \
  --input=http://es.com:9200/template-filter \
  --output=templates.json \
  --type=template

# Import templates into ES
elasticdump \
  --input=./templates.json \
  --output=http://es.com:9200 \
  --type=template

# Split files into multiple parts
elasticdump \
  --input=http://production.es.com:9200/my_index \
  --output=/data/my_index.json \
  --fileSize=10mb

# Import data from S3 into ES (using s3urls)
elasticdump \
  --s3AccessKeyId "${access_key_id}" \
  --s3SecretAccessKey "${access_key_secret}" \
  --input "s3://${bucket_name}/${file_name}.json" \
  --output=http://production.es.com:9200/my_index

# Export ES data to S3 (using s3urls)
elasticdump \
  --s3AccessKeyId "${access_key_id}" \
  --s3SecretAccessKey "${access_key_secret}" \
  --input=http://production.es.com:9200/my_index \
  --output "s3://${bucket_name}/${file_name}.json"

# Import data from MINIO (s3 compatible) into ES (using s3urls)
elasticdump \
  --s3AccessKeyId "${access_key_id}" \
  --s3SecretAccessKey "${access_key_secret}" \
  --input "s3://${bucket_name}/${file_name}.json" \
  --output=http://production.es.com:9200/my_index
  --s3ForcePathStyle true
  --s3Endpoint https://production.minio.co

# Export ES data to MINIO (s3 compatible) (using s3urls)
elasticdump \
  --s3AccessKeyId "${access_key_id}" \
  --s3SecretAccessKey "${access_key_secret}" \
  --input=http://production.es.com:9200/my_index \
  --output "s3://${bucket_name}/${file_name}.json"
  --s3ForcePathStyle true
  --s3Endpoint https://production.minio.co

# Import data from CSV file into ES (using csvurls)
elasticdump \
  # csv:// prefix must be included to allow parsing of csv files
  # --input "csv://${file_path}.csv" \
  --input "csv:///data/cars.csv"
  --output=http://production.es.com:9200/my_index \
  --csvSkipRows 1    # used to skip parsed rows (this does not include the headers row)
  --csvDelimiter ";" # default csvDelimiter is ','
```
