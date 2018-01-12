---
title: frp内网穿透
comments: true
date: 2018-01-12 10:21:35
updated: 2018-01-12 10:21:35
tags:
  - linux
  - docker
categories: linux
---
## frp是什么？

frp 是一个可用于内网穿透的高性能的反向代理应用，支持 tcp, udp, http, https 协议。[github](https://github.com/fatedier/frp)

由frpc与frps组成，frps是服务端，frpc是客户端。

使用frp我们可以将家中的机器通过阿里云、腾讯云代理出来，从而可以在公网访问服务。

## 下载frp命令工具


通过`dpkg --print-architecture`查看你操作系统的位数。我的是amd64。
如果没有dpkg直接`yum install`即可。

[github-release](https://github.com/fatedier/frp/releases)
下载对应的frp包。
```
# 实际上文件是在amazon.s3存储上，可能需要翻墙下载
~ curl -o frp.tar.gz https://github.com/fatedier/frp/releases/download/v0.14.1/frp_0.14.1_linux_amd64.tar.gz
# 解压缩
~ tar -zxvf frp.tar.gz
# 移动到/usr/local/bin目录下，终端命令工具
~ sudo mv frp_0.13.0_linux_amd64/frps /usr/local/bin/
~ sudo mv frp_0.13.0_linux_amd64/frpc /usr/local/bin/
~ frpc --help
~ frps --help
```

## 使用Docker来启动frp服务
通过命令启动需要管理好进程，当需要多个转发服务的时候不好管理。也无法得知客户端究竟开放了哪个端口。
通过docker可以很好的管理服务。
[centos7安装Docker](http://blog.bakka.site/2018/01/11/docker-install/)

获取docker镜像:

```
# 克隆项目
~ git clone https://github.com/sat1993/docker-frp.git
# docker build
~ cd docker-frp && docker build -t sat1993/frp ./
```

## 开放ssh服务

### 配置frps服务端
我是通过docker启动的服务端，主要好管理端口与服务。
### 通过命令启动frps服务
如果没有docker或者不希望通过docker启动服务端的话，可以使用命令启动。
准备ssh.ini配置文件：
```
[common]
bind_port = 40021
```
启动frps服务：
```
setsid frps -c ssh.ini
```

### 通过docker-compose启动frps服务
通过`docker-compose.yml`配置启动服务：
```
version: '2'
services:
  ssh-frp:
    images: sat1993/frp:latest
    ports:
      - "40021:7000"
      - "34090:34090"
    environment:
      FRP: "server"
```
通过docker-compose配置文件，可以一目了然启动了哪些frp服务，占用了哪些端口。
这里使用docker默认的frps配置文件，开放7000服务端口。
上面40021是反向代理服务端口，34090则是对外开放服务端口。34090是由frpc客户端配置的端口。
我们的frpc客户端需要连接40021，我们连接ssh服务时通过34090端口。
### 开放外网端口
通过防火墙开放40021端口和34090端口(centos7使用firewalld服务)：
```
~ firewall-cmd --zone=public --permanent --add-port=40021/tcp
~ firewall-cmd --zone=public --permanent --add-port=34090/tcp
~ firewall-cmd --reload
```
如果是阿里云服务，则到管理台配置安全组即可。

### 配置frpc客户端

通过命令行配置frpc客户端，这里不能使用docker配置。
因为docker内配置frpc只能转发docker环境内的服务。ssh是宿主机上的服务。
准备ssh.ini配置文件：
```
[common]
server_addr = {frps服务所在机器的ip或者域名}
# frps开放的反向代理服务端口
server_port = 40021

[ssh]
type = tcp
# 配置这个可以转发相同网络环境的其他服务。
local_ip = 127.0.0.1
# 本地ssh服务端口
local_port = 22
# 通过frps开放出来的服务端口，占用frps所在机器的端口
remote_port = 34090
# frpc与fprs通信时加密
use_encryption = true
# 压缩通信消息
use_compression = true
```
运行frpc服务：
```
setsid frpc -c ssh.ini
```

连接ssh测试：
```
ssh -i ../sdk_key/lxz -oPort=34090 xxx@xxx.xxx
```
我是配置了秘钥登陆的。所以指定秘钥登陆。
[用户配置与ssh秘钥登陆](http://blog.bakka.site/2018/01/11/centos7-user-config/)

## frp配置web服务转发

这里就只放配置文件了。具体可以参考 [github文档](https://github.com/fatedier/frp/blob/master/README_zh.md)

### frps配置
```
[common]
# 反向代理服务端口
bind_port = 7000
# 实际对外服务的http端口
vhost_http_port = 49100
```

### frpc配置

```
[common]
# frps服务的域名或者ip
server_addr = xxx.xxx
# frps开发的反向代理服务端口
server_port = 7000

[web]
type = http
# 本地需要穿透的实际服务的端口。
local_port = 8080
# 远端对外服务端口。
remote_port = 49100
# 对外访问的域名，仅该域名可访问转发的服务。
custom_domains = test.bakka.site
```

如果没有购置域名，可以通过绑定host的形式访问服务。
