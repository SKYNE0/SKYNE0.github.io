PUT _snapshot/hdfs
{
  "type": "hdfs",
  "settings": {
    "uri": "hdfs://10.0.8.102:8020/",
    "path": "/esbk-2022",
    "conf.dfs.client.read.shortcircuit": "false"
  }
}



https://www.alibabacloud.com/help/zh/object-storage-service/latest/migrate-data-from-hdfs-to-oss
hadoop jar jindo-distcp-3.7.3.jar \
           --src /esbk-hdfs/ \
           --dest oss://tar-es-backup/esbk-hdfs \
           --ossKey LTAI5tCMdQYXoNgyzGPmcdX3 \
           --ossSecret C2B6SvpISapmy5CcSZkonzJW6irp9F \
           --ossEndPoint oss-cn-shanghai.aliyuncs.com