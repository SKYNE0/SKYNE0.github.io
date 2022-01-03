## maven安装部署

### 1.安装

#### 1.1.主机环境
- OS：CentOS7
- Jdk：1.8
- Maven：3.6

#### 1.2.下载
- 下载二进制包，，可以去官方下载或者阿里云镜像(`推荐`)
- 官网：https://maven.apache.org/download.cgi
- 阿里云镜像：https://mirrors.aliyun.com/apache/maven/maven-3/

#### 1.3.解压
- 下载后解压，推荐解压到`/opt`目录
- `tar -xf apache-maven-3.6.3-bin.tar.gz -C /opt`

#### 1.4.添加环境变量
- 在`/etc/profile`底部追加下述内容
  
```bash
export MAVEN_HOME=/opt/apache-maven-3.6.3
export PATH=$PATH:$MAVEN_HOME/bin
```
#### 1.5.验证
- `mvn -v`

```bash
# mvn --version
Apache Maven 3.6.3 (cecedd343002696d0abb50b32b541b8a6ba2883f)
Maven home: /opt/apache-maven-3.6.3
Java version: 1.8.0_311, vendor: Oracle Corporation, runtime: /usr/java/jdk1.8.0_311-amd64/jre
Default locale: zh_CN, platform encoding: UTF-8
OS name: "linux", version: "3.10.0-1160.el7.x86_64", arch: "amd64", family: "unix"
```

### 2.配置Maven
#### 2.1.配置本地仓库目录
- 默认在：`${user.home}/.m2/repository`, 建议修改一下

```bash
vim conf/settings.xml

<localRepository>/opt/apache-maven-3.6.3/repository</localRepository>
```
#### 2.2.配置国内镜像地址
- 国外源较慢，因此配置为国内镜像源
  
```bash
  <mirrors>
      <mirror>
            <id>alimaven</id>
            <name>aliyun maven</name>
             <url>https://maven.aliyun.com/nexus/content/groups/public/</url>
            <mirrorOf>central</mirrorOf>
      </mirror>
  </mirrors>
```

#### 2.3.验证
- `mvn help:system`: 该命令会首次下载很对依赖，并输出一些环境变量之类的信息
- 如无报错，则`Maven`安装配置完成

```bash
PWD=/opt/apache-maven-3.6.3
SSH_TTY=/dev/pts/0
MAVEN_CMD_LINE_ARGS= help:system
HOSTNAME=dev-sl-web
LESSOPEN=||/usr/bin/lesspipe.sh %s
SHLVL=1
MAIL=/var/spool/mail/root
MAVEN_PROJECTBASEDIR=/opt/apache-maven-3.6.3
SSH_CONNECTION=192.168.14.92 64169 10.0.4.222 22
SSH_CLIENT=192.168.14.92 64169 22
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin:/opt/apache-maven-3.6.3/bin
TERM=xterm-256color
OLDPWD=/opt/apache-maven-3.6.3
MAVEN_HOME=/opt/apache-maven-3.6.3
XDG_RUNTIME_DIR=/run/user/0

[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  0.797 s
[INFO] Finished at: 2021-12-29T15:54:32+08:00
[INFO] ------------------------------------------------------------------------
```