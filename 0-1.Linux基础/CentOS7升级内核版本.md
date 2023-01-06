## CentOS7 升级内核版本

### 1.默认版本
- CentOS7.X默认内核版本为：3.10
- 查看当前内核版本：uname -sr

### 2.升级内核
- 添加ELRepo源：

```
rpm --import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org
rpm -Uvh http://www.elrepo.org/elrepo-release-7.0-2.el7.elrepo.noarch.rpm
```

- 查看相关内核包：`yum --disablerepo="*" --enablerepo="elrepo-kernel" list available`
- 内核版本介绍：
    + lt:longterm的缩写：长期维护版；
    + ml:mainline的缩写：最新稳定版；
- 安装最新的主线稳定内核: `yum --enablerepo=elrepo-kernel install kernel-ml`
- 查看内核版本：`uname -sr`

### 3.设置默认内核
- 查看当前内核引导顺序：awk -F\' '$1=="menuentry " {print i++ " : " $2}' /etc/grub2.cfg

```
vim /etc/default/grub
设置 GRUB_DEFAULT=0, 初始化页面的第一个内核将作为默认内核
```

- 重新创建内核配置：`grub2-mkconfig -o /boot/grub2/grub.cfg`

### 4.删除旧内核

```
uname -sr
yum remove kernel-3.10.0-514.el7.x86_64 \
    kernel-tools-libs-3.10.0-862.11.6.el7.x86_64 \
    kernel-tools-3.10.0-862.11.6.el7.x86_64 \
    kernel-3.10.0-862.11.6.el7.x86_64
```