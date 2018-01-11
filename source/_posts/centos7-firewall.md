---
title: centos7使用firewalld防火墙
comments: true
date: 2018-01-11 16:40:47
updated: 2018-01-11 16:40:47
tags:
  - centos7
  - linux
categories: centos7
---
## centos7 firewalld

firewalld是centos7的一大特性，最大的好处有两个：支持动态更新，不用重启服务；第二个就是加入了防火墙的“zone”概念。

firewalld默认配置文件有两个：/usr/lib/firewalld/ （系统配置，尽量不要修改）和 /etc/firewalld/ （用户配置地址）

## Firewall Zone

硬件防火墙默认一般有三个区，firewalld引入这一概念系统默认存在以下区域:
drop：默认丢弃所有包
block：拒绝所有外部连接，允许内部发起的连接
public：指定外部连接可以进入
external：
dmz：和硬件防火墙一样，受限制的公共连接可以进入
work：工作区，概念和workgoup一样，也是指定的外部连接允许
home：类似家庭组
internal：信任所有连接

## 配置firewalld

```
添加端口,--permanent表示永久生效。不加该参数，下次启动就需要重新配置。
~ firewall-cmd --zone=public --permanent --add-port=8080/tcp
刷新防火墙配置
~ firewall-cmd --reload
查看开放端口
~ firewall-cmd --list-ports
```

## 以Service的配置文件来配置Firewall

```
在/etc/firewalld/services目录下添加nfs.xml文件后
~ firewall-cmd --zone=public --permanent --add-service=nfs
刷新配置
~ firewall-cmd --reload
```

具体nfs.xml文件如下：
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

如果是复杂服务需要开放多个端口的话，建议通过`/etc/firewalld/services`管理。
建立一个common.xml的service用于管理所有简单端口服务。

