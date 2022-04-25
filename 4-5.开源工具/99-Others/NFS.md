## NFS

### 1.NFS
- NFS: `Network File System` 的缩写，即网络文件系统。功能是让客户端通过网络访问不同主机上磁盘里的数据

### 2.安装
- `sudo yum install nfs-utils`
- `rpcbind` 作为依赖会被自动安装上
- 启动和开机自启
  + `sudo systemctl enable rpcbind`
  + `sudo systemctl enable nfs`
  + `sudo systemctl start rpcbind`
  + `sudo systemctl start nfs`

### 3.防火墙
- 防火墙需要允许 `rpc-bind` 和 `nfs` 的服务
- `sudo firewall-cmd --zone=public --permanent --add-service={rpc-bind,mountd,nfs}`
- `sudo firewall-cmd --reload`


### 4.权限
- 权限规则：
  + `root` 用户被映射成 `nfsnobody` 用户
  + `client`端会和`server`端的`UID`相同的用户做映射，映射失败默认为 `nobody`用户
  + 权限跟随`server`端
  + 建议 `client` 端和 `server` 建立相同的`group`和`user`
  + 

- 常用的改变`用户权限`的命令：
  + 添加组：`groupadd -g gid name`
  + 添加带家目录可登录用户：`useradd -g gname -G gname -m -u uid  name `
  + 添加无家目录不可登录用户：`useradd -g gname -G gname -M -s /sbin/nologin -u uid  name `
  + 修改`GID`: `groupmod -g gid`
  + 修改`uID`: `usermod -u uid`
  + 

```bash
useradd  创建的新的系统用户

补充说明
useradd命令 用于Linux中创建的新的系统用户。useradd可用来建立用户帐号。帐号建好之后，再用passwd设定帐号的密码．而可用userdel删除帐号。使用useradd指令所建立的帐号，实际上是保存在/etc/passwd文本文件中。

语法
useradd(选项)(参数)
选项
-c<备注>：加上备注文字。备注文字会保存在passwd的备注栏位中；
-d<登入目录>：指定用户登入时的启始目录；
-D：变更预设值；
-e<有效期限>：指定帐号的有效期限；
-f<缓冲天数>：指定在密码过期后多少天即关闭该帐号；
-g<群组>：指定用户所属的群组；
-G<群组>：指定用户所属的附加群组；
-m：自动建立用户的登入目录；
-M：不要自动建立用户的登入目录；
-n：取消建立以用户名称为名的群组；
-r：建立系统帐号；
-s<shell>：指定用户登入后所使用的shell；
-u<uid>：指定用户id。
```

### 5.服务端配置
- 配置文件：`/etc/exports`
- 配置选项: 

|配置项| 说明|
| :-: | :-: |
|ro | 默认选项，以只读的方式共享| 
|rw | 以读写的方式共享| 
|no_root_squash | 将客户端使用的是root用户时，则映射到FNS服务器的用户依然为root用户 `该选项极不安全`| 
|root_squash | 如果访问NFS Server共享目录的用户时root，则它的权限将被压缩成匿名用户，同时它的UID和GID通常会变成nfsnobody账号身份| 
|all_squash | 默认选项，将所有访问NFS服务器的客户端的用户都映射为匿名用户，不管客户端使用的是什么用户| 
|anonuid | 设置映射到本地的匿名用户的UID| 
|anongid | 设置映射到本地的匿名用户的GID| 
|sync | 默认选项，保持数据同步，数据同步写入到内存和硬盘| 
|async | 异步，先将数据写入到内存，在将数据写入到硬盘| 
|secure | NFS客户端必须使用NFS保留端口（通常是1024以下的端口），默认选项| 
|insecure | 允许NFS客户端不使用NFS保留端口（通常是1024以上的端口）| 

- 客户端地址：
  
|客户端地址| IP范围|
| :-: | :-: |
| 单IP| 192.168.1.101|
| 子网掩码| 192.168.1.1/24|
| 网段| 192.168.1.*|
| 单域名| nfs.client.com|
| 泛域名| *.client.com|

- eg: `/mnt 10.0.1.0/16(rw,sync,root_squash,no_all_squash)`

### 6.常用命令
- 常用：
  + 查看共享目录：`exportfs` 或 `showmount -e localhost`
  + 挂载目录：`sudo mount -t nfs  nfs_ip:/mnt  /mnt`
  + 开机自动挂载: `sudo echo 'nfs_ip:/mnt    /mnt    nfs  defaults,_netdev  0 0' >> /etc/fstab `

- `mount`

```
mount  用于挂载Linux系统外的文件

补充说明
mount命令 Linux mount命令是经常会使用到的命令，它用于挂载Linux系统外的文件。

语法
mount [-hV]
mount -a [-fFnrsvw] [-t vfstype]
mount [-fnrsvw] [-o options [,...]] device | dir
mount [-fnrsvw] [-t vfstype] [-o options] device dir
选项
-V：显示程序版本
-h：显示辅助讯息
-v：显示较讯息，通常和 -f 用来除错。
-a：将 /etc/fstab 中定义的所有档案系统挂上。
-F：这个命令通常和 -a 一起使用，它会为每一个 mount 的动作产生一个行程负责执行。在系统需要挂上大量 NFS 档案系统时可以加快挂上的动作。
-f：通常用在除错的用途。它会使 mount 并不执行实际挂上的动作，而是模拟整个挂上的过程。通常会和 -v 一起使用。
-n：一般而言，mount 在挂上后会在 /etc/mtab 中写入一笔资料。但在系统中没有可写入档案系统存在的情况下可以用这个选项取消这个动作。
-s-r：等于 -o ro
-w：等于 -o rw
-L：将含有特定标签的硬盘分割挂上。
-U：将档案分割序号为 的档案系统挂下。-L 和 -U 必须在/proc/partition 这种档案存在时才有意义。
-t：指定档案系统的型态，通常不必指定。mount 会自动选择正确的型态。
-o async：打开非同步模式，所有的档案读写动作都会用非同步模式执行。
-o sync：在同步模式下执行。
-o atime、-o noatime：当 atime 打开时，系统会在每次读取档案时更新档案的『上一次调用时间』。当我们使用 flash 档案系统时可能会选项把这个选项关闭以减少写入的次数。
-o auto、-o noauto：打开/关闭自动挂上模式。
-o defaults:使用预设的选项 rw, suid, dev, exec, auto, nouser, and async.
-o dev、-o nodev-o exec、-o noexec允许执行档被执行。
-o suid、-o nosuid：
允许执行档在 root 权限下执行。
-o user、-o nouser：使用者可以执行 mount/umount 的动作。
-o remount：将一个已经挂下的档案系统重新用不同的方式挂上。例如原先是唯读的系统，现在用可读写的模式重新挂上。
-o ro：用唯读模式挂上。
-o rw：用可读写模式挂上。
-o loop=：使用 loop 模式用来将一个档案当成硬盘分割挂上系统。
实例
将 /dev/hda1 挂在 /mnt 之下。

#mount /dev/hda1 /mnt
将 /dev/hda1 用唯读模式挂在 /mnt 之下。

#mount -o ro /dev/hda1 /mnt
将 /tmp/image.iso 这个光碟的 image 档使用 loop 模式挂在 /mnt/cdrom 之下。用这种方法可以将一般网络上可以找到的 Linux 光 碟 ISO 档在不烧录成光碟的情况下检视其内容。

#mount -o loop /tmp/image.iso /mnt/cdrom
```

- `exportfs`

```bash
exportfs 命令用来管理当前NFS共享的文件系统列表。

参数：

-a 打开或取消所有目录共享。
-o options,...指定一列共享选项，与 exports(5) 中讲到的类似。
-i 忽略 /etc/exports 文件，从而只使用默认的和命令行指定的选项。
-r 重新共享所有目录。它使 /var/lib/nfs/xtab 和 /etc/exports 同步。 它将 /etc/exports 中已删除的条目从 /var/lib/nfs/xtab 中删除，将内核共享表中任何不再有效的条目移除。
-u 取消一个或多个目录的共享。
-f 在“新”模式下，刷新内核共享表之外的任何东西。 任何活动的客户程序将在它们的下次请求中得到 mountd添加的新的共享条目。
```

- `showmount`
  
```
showmount  显示NFS服务器加载的信息

补充说明
showmount命令 查询“mountd”守护进程，以显示NFS服务器加载的信息。

语法
showmount(选项)(参数)
选项
-d：仅显示已被NFS客户端加载的目录；
-e：显示NFS服务器上所有的共享目录。
参数
NFS服务器：指定NFS服务器的ip地址或者主机名。
```