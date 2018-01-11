---
title: centos7安装Docker
comments: true
date: 2018-01-11 16:35:44
updated: 2018-01-11 16:35:44
tags:
  - linux
  - centos7
  - docker
categories: centos7
---
## docker简介

docker是一个容器，将服务封装到docker image中，以container的形式启动。
主要可以做到服务间的隔离，保证不会出现相互侵蚀的情况。

使用Docker有以下好处：

1. 更高效的利用系统资源
2. 更快速的启动时间
3. 一致的运行环境
4. 持续交付和部署
5. 更轻松的迁移
6. 更轻松的维护和扩展

对个人开发者来说，docker最明显的好处就是能快速搭建开发所需要的环境(kafka、mysql、redis等)。
摆脱手动安装的困扰，需要的话docker pull一下，再不济build一个新镜像。

## docker安装

使用官方repo实在是太慢，这里借用阿里云的镜像
```
安装必须的系统工具
~ sudo yum install -y yum-utils device-mapper-persistent-data lvm2
添加yum源信息
~ sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
更新并安装docker-ce
~ sudo yum makecache fast
~ sudo yum -y install docker-ce
启动Docker服务
~ sudo service docker start
```

### 开启Docker加速

我使用的是daocloud的加速器:https://www.daocloud.io/mirror#accelerator-doc.

```
设置加速器，其实是修改的/etc/docker/deamon.json中的registry-mirrors属性
~ sudo curl -sSL https://get.daocloud.io/daotools/set_mirror.sh | sh -s http://加速器地址
```

### 安装docker-compose

`pip install docker-compose`

docker-compose参考官方文档https://docs.docker.com/compose/

