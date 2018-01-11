---
title: Centos7硬盘挂载
comments: true
date: 2018-01-11 16:37:44
updated: 2018-01-11 16:37:44
tags:
  - linux
  - centos7
categories: centos7
---
## Centos7硬盘挂载

按最初的设计，我有两块移动硬盘，2T的做媒体资源的nfs贡献，4T的做数据备份与日志备份。

2T因为已经有电影资源，因此不格式化硬盘，直接通过ntfs挂载，并设置nfs共享。

4T盘基本是空的，通过格式化成ext4格式。不设置文件共享。

格式化并挂载ext4硬盘：
```
~ fdisk -l
通过fdisk -l查询到要格式化的盘为/dev/sdb
~ mkfs.ext4 /dev/sdb
挂载sdb1分区到目录上
~ mount /dev/sdb1 /home/lixiaozhu/mount/big-file/
```

ntfs硬盘挂载：

```
安装依赖：
~ yum install ntfs-3g fuse -y
~ fdisk -l
通过fdisk -l查询到要挂载的盘为/dev/sdc1
~ mount -t ntfs-3g /dev/sdc1 /home/lixiaozhu/mount/small-file
```
