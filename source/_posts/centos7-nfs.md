---
title: centos7使用nfs做网络共享
comments: true
date: 2018-01-11 16:42:32
updated: 2018-01-11 16:42:32
tags:
  - centos7
  - linux
  - nfs
categories: centos7
---
## nfs

NFS(Network File System)即网络文件系统，它允许网络中的计算机之间通过TCP/IP网络共享资源。

本地可以通关挂载nfs共享，达到在本地直接打开小电影的效果。
通过aria2下载电影到挂载的移动硬盘。再通过nfs直接分享，可以直接在本地打开视频文件。
适用于centos7 1708版本(其他centos7版本也使用)

### nfs工作流程

1、由程序在NFS客户端发起存取文件的请求，客户端本地的RPC(rpcbind)服务会通过网络向NFS服务端的RPC的111端口发出文件存取功能的请求。`

2、NFS服务端的RPC找到对应已注册的NFS端口，通知客户端RPC服务。

3、客户端获取正确的端口，并与NFS daemon联机存取数据。

4、存取数据成功后，返回前端访问程序，完成一次存取操作。

## nfs服务安装

安装rpcbind、nfs-utils
`~ yum install rpcbind nfs-utils`

暂时不要启动nfs、rpcbind服务，需要先固定好nfs服务端口，避免重启电脑。

### 固定nfs服务端口

nfs服务端口主要有 nfs 2049、portmapper111、mountd 892、rpc.statd662、 nlockmgr 32803、32769

具体配置：

1. 修改/etc/sysconfig/nfs文件：

```
# Port rpc.mountd should listen on.
MOUNTD_PORT=892
#
# Optional arguments passed to rpc.statd. See rpc.statd(8)
STATDARG=""
# Port rpc.statd should listen on.
STATD_PORT=662
```

2. 修改 /etc/modprobe.d/lockd.conf

```
# Set the TCP port that the NFS lock manager should use.
# port must be a valid TCP port value (1-65535).
options lockd nlm_tcpport=32803
#
# Set the UDP port that the NFS lock manager should use.
# port must be a valid UDP port value (1-65535).
options lockd nlm_udpport=32769
```

### 启动nfs服务

通过systemctl启动rpcbind、nfs：
```
# 启动rpcbind
~ systemctl start rpcbind
# 启动nfs
~ systemctl start nfs
```

## 通过firewall配置防火墙

编辑firewalld中的nfs服务的防火墙配置文件：
`~ cp /usr/lib/firewalld/services/nfs.xml /etc/firewalld/services`

添加固定的端口到`/etc/firewalld/services/nfs.xml`配置文件中：

```
<?xml version="1.0" encoding="utf-8"?>
<service>
  <short>NFS4</short>
  <description>The NFS4 protocol is used to share files via TCP networking. You will need to have the NFS tools installed and properly configure your NFS server for this option to be useful.</description>
  <port protocol="tcp" port="111"/>
  <port protocol="tcp" port="662"/>
  <port protocol="tcp" port="892"/>
  <port protocol="tcp" port="2049"/>
  <port protocol="tcp" port="32803"/>
  <port protocol="udp" port="111"/>
  <port protocol="udp" port="662"/>
  <port protocol="udp" port="892"/>
  <port protocol="udp" port="2049"/>
  <port protocol="udp" port="32769"/>
</service>
```

开启防火墙配置：
```
# service名字需要与/etc/firewalld/services中的文件名匹配
~ firewall-cmd --permanent --zone=public --add-service=nfs
# 重新加载防火墙
~ firewall-cmd --reload
```

## 开放nfs共享

`{local_dir} {ip_address}({param1},{param2}) 客户端地址2(参数1,参数2,...)`

说明：

```
local_dir: 本地需要共享的目录绝对路径。
ip_address: 支持ip、x.x.x.0/24子网地址、域名、*.bakka.site子域名、*所有
param1,param2:
rw(该目录分享的权限是可擦写(read-write)，可通过客户端删除、编辑、添加文件)
ro(只读(read-only))
sync(sync 代表数据会同步写入到内存与硬盘中)
async(async 则代表数据会先暂存于内存当中，而非直接写入硬盘)
no_root_squash(客户机用root访问该共享文件夹时，不映射root用户)
root_squash(客户机用root用户访问该共享文件夹时，将root用户映射成匿名用户)
all_squash(客户机上的任何用户访问该共享目录时都映射成匿名用户)
anonuid(将客户机上的用户映射成指定的本地用户ID的用户)
anongid(将客户机上的用户映射成属于指定的本地用户组ID)
insecure(允许客户端从大于1024的tcp/ip端口连接服务器)
secure(限制客户端只能从小于1024的tcp/ip端口连接nfs服务器（默认设置）)
wdelay(检查是否有相关的写操作，如果有则将这些写操作 一起执行，这样可以提高效率（默认设置))
no_wdelay(若有写操作则立即执行，应与sync配合使用)
subtree(若输出目录是一个子目录，则nfs服务器将检查其父目录的权限(默认设置))
no_subtree(即使输出目录是一个子目录,nfs服务器也不检查其父目录的权限,这样可以提高效率)
```
nfs开放文件共享，编辑文件/etc/exports文件，添加：
`/home/lixiaozhu/mount/small-file/share_file 192.168.1.0/24(rw,sync,no_root_squash,insecure,anonuid=1001)`
重新加载nfs共享配置
```
~ exportfs -rv
exporting 192.168.1.107:/home/lixiaozhu/mount/small-file/share_file
```
客户端查看是否有可用的nfs共享：
`~ showmount -e 192.168.1.107`

## Client挂载

将nfs服务端的文件挂载到本地
`mount -t nfs 192.168.1.107:/home/lixiaozhu/mount/small-file/share_file ~/Documents/share_server`

## 挂载时常见错误

### Can't mount from 192.168.1.107 onto /:Operation not permitted

1. 防火墙端口有没有开放的，仅开放了客户端rpc api的部分，仅能查看分享，实际不能挂载。
2. rpc相关端口开放在1024以上，需要在`/etc/exports`配置`insecure`

