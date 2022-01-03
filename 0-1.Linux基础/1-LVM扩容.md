## LVM扩容

### 1.LVM(Logical Volume Manager)
- `LVM`是在磁盘分区和文件系统之间添加的一个`逻辑层`，来为文件系统屏蔽下层磁盘分区布局，提供一个抽象的盘卷

#### 1.1.LVM基础概念
- `物理存储介质（The physical media）`：这里指系统的存储设备：硬盘，如：/dev/hda1、/dev/sda等等，是存储系统最低层的存储单元
- `物理卷（physical volume）`：物理卷就是指硬盘分区或从逻辑上与磁盘分区具有同样功能的设备(如RAID)，是LVM的基本存储逻辑块，但和基本的物理存储介质（如分区、磁盘等）比较，却包含有与LVM相关的管理参数。
- `卷组（Volume Group）`：LVM卷组类似于非LVM系统中的物理硬盘，其由物理卷组成。可以在卷组上创建一个或多个“LVM分区”（逻辑卷），LVM卷组由一个或多个物理卷组成。
- `逻辑卷（logical volume）`：LVM的逻辑卷类似于非LVM系统中的硬盘分区，在逻辑卷之上可以建立文件系统(比如/home或者/usr等)。
- `PE（physical extent）`：每一个物理卷被划分为称为PE(Physical Extents)的基本单元，具有唯一编号的PE是可以被LVM寻址的最小单元。PE的大小是可配置的，默认为4MB。
- `LE（logical extent）`：逻辑卷也被划分为被称为LE(Logical Extents) 的可被寻址的基本单位。在同一个卷组中，LE的大小和PE是相同的，并且一一对应。

### 2.查看磁盘信息
- 查看主机的文件系统情况: `df -Th`
- 查看主 机的物理磁盘信息: `fdisk -l`

```
# fdisk -l

磁盘 /dev/vda：53.7 GB, 53687091200 字节，104857600 个扇区
Units = 扇区 of 1 * 512 = 512 bytes
扇区大小(逻辑/物理)：512 字节 / 512 字节
I/O 大小(最小/最佳)：512 字节 / 512 字节
磁盘标签类型：dos
磁盘标识符：0x0001b39f

   设备 Boot      Start         End      Blocks   Id  System
/dev/vda1   *        2048     1026047      512000   83  Linux
/dev/vda2         1026048   104857599    51915776   8e  Linux LVM

磁盘 /dev/vdb：107.4 GB, 107374182400 字节，209715200 个扇区
Units = 扇区 of 1 * 512 = 512 bytes
扇区大小(逻辑/物理)：512 字节 / 512 字节
I/O 大小(最小/最佳)：512 字节 / 512 字节
磁盘标签类型：dos
磁盘标识符：0x2953a37f

   设备 Boot      Start         End      Blocks   Id  System
/dev/vdb1            2048   209715199   104856576   8e  Linux LVM

磁盘 /dev/mapper/centos_template-root：48.9 GB, 48863641600 字节，95436800 个扇区
Units = 扇区 of 1 * 512 = 512 bytes
扇区大小(逻辑/物理)：512 字节 / 512 字节
I/O 大小(最小/最佳)：512 字节 / 512 字节


磁盘 /dev/mapper/centos_template-swap：4294 MB, 4294967296 字节，8388608 个扇区
Units = 扇区 of 1 * 512 = 512 bytes
扇区大小(逻辑/物理)：512 字节 / 512 字节
I/O 大小(最小/最佳)：512 字节 / 512 字节
```

### 3.新建分区并改变格式
- 该步骤需要`新建分区`并`格式化磁盘`, 操作需要谨慎

```
[root@proxy01 ~]# fdisk /dev/vdb
欢迎使用 fdisk (util-linux 2.23.2)。

更改将停留在内存中，直到您决定将更改写入磁盘。
使用写入命令前请三思。

Device does not contain a recognized partition table
使用磁盘标识符 0x2953a37f 创建新的 DOS 磁盘标签。

命令(输入 m 获取帮助)：n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p):
Using default response p
分区号 (1-4，默认 1)：
起始 扇区 (2048-209715199，默认为 2048)：
将使用默认值 2048
Last 扇区, +扇区 or +size{K,M,G} (2048-209715199，默认为 209715199)：
将使用默认值 209715199
分区 1 已设置为 Linux 类型，大小设为 100 GiB

命令(输入 m 获取帮助)：
命令(输入 m 获取帮助)：
命令(输入 m 获取帮助)：m
命令操作
   a   toggle a bootable flag
   b   edit bsd disklabel
   c   toggle the dos compatibility flag
   d   delete a partition
   g   create a new empty GPT partition table
   G   create an IRIX (SGI) partition table
   l   list known partition types
   m   print this menu
   n   add a new partition
   o   create a new empty DOS partition table
   p   print the partition table
   q   quit without saving changes
   s   create a new empty Sun disklabel
   t   change a partition's system id
   u   change display/entry units
   v   verify the partition table
   w   write table to disk and exit
   x   extra functionality (experts only)

命令(输入 m 获取帮助)：p

磁盘 /dev/vdb：107.4 GB, 107374182400 字节，209715200 个扇区
Units = 扇区 of 1 * 512 = 512 bytes
扇区大小(逻辑/物理)：512 字节 / 512 字节
I/O 大小(最小/最佳)：512 字节 / 512 字节
磁盘标签类型：dos
磁盘标识符：0x2953a37f

   设备 Boot      Start         End      Blocks   Id  System
/dev/vdb1            2048   209715199   104856576   83  Linux

命令(输入 m 获取帮助)：t
已选择分区 1
Hex 代码(输入 L 列出所有代码)：L

 0  空              24  NEC DOS         81  Minix / 旧 Linu bf  Solaris
 1  FAT12           27  隐藏的 NTFS Win 82  Linux 交换 / So c1  DRDOS/sec (FAT-
 2  XENIX root      39  Plan 9          83  Linux           c4  DRDOS/sec (FAT-
 3  XENIX usr       3c  PartitionMagic  84  OS/2 隐藏的 C:  c6  DRDOS/sec (FAT-
 4  FAT16 <32M      40  Venix 80286     85  Linux 扩展      c7  Syrinx
 5  扩展            41  PPC PReP Boot   86  NTFS 卷集       da  非文件系统数据
 6  FAT16           42  SFS             87  NTFS 卷集       db  CP/M / CTOS / .
 7  HPFS/NTFS/exFAT 4d  QNX4.x          88  Linux 纯文本    de  Dell 工具
 8  AIX             4e  QNX4.x 第2部分  8e  Linux LVM       df  BootIt
 9  AIX 可启动      4f  QNX4.x 第3部分  93  Amoeba          e1  DOS 访问
 a  OS/2 启动管理器 50  OnTrack DM      94  Amoeba BBT      e3  DOS R/O
 b  W95 FAT32       51  OnTrack DM6 Aux 9f  BSD/OS          e4  SpeedStor
 c  W95 FAT32 (LBA) 52  CP/M            a0  IBM Thinkpad 休 eb  BeOS fs
 e  W95 FAT16 (LBA) 53  OnTrack DM6 Aux a5  FreeBSD         ee  GPT
 f  W95 扩展 (LBA)  54  OnTrackDM6      a6  OpenBSD         ef  EFI (FAT-12/16/
10  OPUS            55  EZ-Drive        a7  NeXTSTEP        f0  Linux/PA-RISC
11  隐藏的 FAT12    56  Golden Bow      a8  Darwin UFS      f1  SpeedStor
12  Compaq 诊断     5c  Priam Edisk     a9  NetBSD          f4  SpeedStor
14  隐藏的 FAT16 <3 61  SpeedStor       ab  Darwin 启动     f2  DOS 次要
16  隐藏的 FAT16    63  GNU HURD or Sys af  HFS / HFS+      fb  VMware VMFS
17  隐藏的 HPFS/NTF 64  Novell Netware  b7  BSDI fs         fc  VMware VMKCORE
18  AST 智能睡眠    65  Novell Netware  b8  BSDI swap       fd  Linux raid 自动
1b  隐藏的 W95 FAT3 70  DiskSecure 多启 bb  Boot Wizard 隐  fe  LANstep
1c  隐藏的 W95 FAT3 75  PC/IX           be  Solaris 启动    ff  BBT
1e  隐藏的 W95 FAT1 80  旧 Minix
Hex 代码(输入 L 列出所有代码)：8e
已将分区“Linux”的类型更改为“Linux LVM”

命令(输入 m 获取帮助)：p

磁盘 /dev/vdb：107.4 GB, 107374182400 字节，209715200 个扇区
Units = 扇区 of 1 * 512 = 512 bytes
扇区大小(逻辑/物理)：512 字节 / 512 字节
I/O 大小(最小/最佳)：512 字节 / 512 字节
磁盘标签类型：dos
磁盘标识符：0x2953a37f

   设备 Boot      Start         End      Blocks   Id  System
/dev/vdb1            2048   209715199   104856576   8e  Linux LVM

命令(输入 m 获取帮助)：w
The partition table has been altered!

Calling ioctl() to re-read partition table.
正在同步磁盘。
[root@proxy01 ~]#
```

### 4.创建物理卷
```
[root@proxy01 ~]# partprobe # 更新分区表
[root@proxy01 ~]# pvcreate /dev/vdb1
```

### 5.扩展卷组
```
[root@proxy01 ~]# vgs
  VG              #PV #LV #SN Attr   VSize   VFree
  centos_template   1   2   0 wz--n- <49.51g    0
[root@proxy01 ~]#
[root@proxy01 ~]# vgextend centos_template /dev/vdb1
  Volume group "centos_template" successfully extended
[root@proxy01 ~]#
```

### 6. 扩展逻辑卷
```
[root@proxy01 ~]# lvextend -l +100%FREE /dev/mapper/centos_template-root
  Size of logical volume centos_template/root changed from <45.51 GiB (11650 extents) to 145.50 GiB (37249 extents).
  Logical volume centos_template/root successfully resized.
[root@proxy01 ~]#
[root@proxy01 ~]#
[root@proxy01 ~]# lsblk
NAME                     MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sr0                       11:0    1   498K  0 rom
vda                      252:0    0    50G  0 disk
├─vda1                   252:1    0   500M  0 part /boot
└─vda2                   252:2    0  49.5G  0 part
  ├─centos_template-root 253:0    0 145.5G  0 lvm  /
  └─centos_template-swap 253:1    0     4G  0 lvm  [SWAP]
vdb                      252:16   0   100G  0 disk
└─vdb1                   252:17   0   100G  0 part
  └─centos_template-root 253:0    0 145.5G  0 lvm  /
```

### 7.调整文件系统的大小
- 首先使用 `df -hT` 确认分区的`格式`
- `ext4` 等使用 `resize2fs`, `xfs` 使用 `xfs_growfs`

```
ext4: resize2fs  /dev/mapper/centos_template-root
xfs: xfs_growfs /dev/mapper/centos_template-root
```

- 确认扩容是否生效: `df -h`