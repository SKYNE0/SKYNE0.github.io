## GitLab安装
> [官方安装文档](https://about.gitlab.com/install/)

### 1.安装依赖

```bash
sudo yum install -y curl policycoreutils-python openssh-server perl
sudo systemctl enable sshd
sudo systemctl start sshd

# 不开启防火墙的话跳过该步骤
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --permanent --add-service=https
sudo systemctl reload firewalld
```

### 2.安装Postfix
- 安装`Postfix`来发送邮件，如果不需要可以跳过
- `sudo yum install postfix`
  + `sudo systemctl enable postfix`
  + `sudo systemctl start postfix`

### 3.添加官方镜像源
- 官方：`curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ee/script.rpm.sh | sudo bash`
- 因为众所周知的原因，`推荐`使用国内的`镜像源`

```bash
vim /etc/yum.repos.d/gitlab-ce.repo

[gitlab-ce]
name=Gitlab CE Repository
baseurl=https://mirrors.cloud.tencent.com/gitlab-ce/yum/el$releasever/
gpgcheck=0
enabled=1
```

- `sudo yum makecache`
- 查看可安装版本：`yum list gitlab-ce --showduplicates`

### 4.安装

