yum install http://script.tarsocial.com/OpenSource/jdk-8u311-linux-x64.rpm -y
 wget --no-check-certificate https://dlcdn.apache.org/hadoop/common/hadoop-3.3.4/hadoop-3.3.4.tar.gz


 tar -xf /tmp/hadoop-3.3.4.tar.gz -C /opt/

export JAVA_HOME=/opt/jdk1.8.0_341
export JRE_HOME=${JAVA_HOME}/jre
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
export HADOOP_HOME=/opt/hadoop-3.3.4
export PATH=$HADOOP_HOME/bin:${JAVA_HOME}/bin:$PATH

export HDFS_NAMENODE_USER=root
export HDFS_DATANODE_USER=root
export HDFS_SECONDARYNAMENODE_USER=root
export YARN_RESOURCEMANAGER_USER=root
export YARN_NODEMANAGER_USER=root



-- 8.102 启动NameNode节点
hdfs --daemon start namenode

-- 8.103 在所有节点上启动DataNode
hdfs --daemon start datanode

-- 8.104 启动Secondary NameNode
hdfs --daemon start secondarynamenode

- 8.105 选择node1.cn节点启动ResourceManager节点
yarn --daemon start resourcemanager

-- 4.4 在所有节点上启动NodeManager
yarn --daemon start nodemanager