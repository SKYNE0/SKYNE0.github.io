### 
yum install http://script.tarsocial.com/OpenSource/jdk-8u311-linux-x64.rpm -y
 wget --no-check-certificate https://dlcdn.apache.org/hadoop/common/hadoop-3.3.4/hadoop-3.3.4.tar.gz


 tar -xf /tmp/hadoop-3.3.4.tar.gz -C /opt/

export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.242.b08-1.el7.x86_64/jre
export HADOOP_HOME=/opt/hadoop-3.3.4
export PATH=$HADOOP_HOME/bin:$PATH