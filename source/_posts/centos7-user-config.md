---
title: 用户配置与ssh秘钥登陆
comments: true
date: 2018-01-11 16:46:42
updated: 2018-01-11 16:46:42
tags:
  - linux
  - centos7
categories: centos7
---
## 添加用户

```
添加用户
~ useradd lixiaozhu
设置密码，用户没有密码无法登陆。
~ passwd lixiaozhu
```

参考文章：http://blog.csdn.net/boybruce/article/details/17198601

## 设置秘钥登陆
配置好公私钥之后，还需要修改ssh配置，从密码登陆修改为秘钥登陆。
```
切换为root用户
~ su -
~ vi /etc/config/sshd_config

在文件中添加3行内容
RSAAuthentication yes
PubkeyAuthentication yes
AuthorizedKeysFile      .ssh/authorized_keys
重启sshd服务
~ systemctl restart sshd
开放22端口
~ firewall-cmd --zone=public --permanent --add-port=22/tcp && firewall-cmd --reload
```
设置好sshd服务之后，配置用户秘钥信息.
```
切换用户
~ su lixiaozhu && cd ~
创建.ssh目录，并修改目录权限
~ mkdir .ssh && chmod 700 .ssh
通过连接端生成秘钥，并scp公钥到服务器
~ ssh-keygen -t rsa
通过密码传输文件
~ scp lxz.pub lixiaozhu@192.168.1.107:~/.ssh/
到服务端修改文件权限
~ cat ~/.ssh/lxz.pub >> ~/.ssh/authorized_keys && chmod 600 ~/.ssh/authorized_keys
~ rm ~/.ssh/lxz.pub
连接端连接服务器
~ ssh -i lxz lixiaozhu@192.168.1.107
```

ssh只需要保证服务端`authorized_keys`文件记录秘钥的公钥，客户端记录私钥即可秘钥登陆。


