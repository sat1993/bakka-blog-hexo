---
title: 安装centos7操作系统
date: 2018-01-10 11:26:06
tags:
  - linux
  - 操作系统
comments: true
categories: 个人服务器
---
## 服务器简介

最初在3月份的时候折腾过服务器。当时配置是

1. 机械键盘500G 7年使用龄
2. 海盗船笔记本内存8G
3. G3460 cpu
4. 华硕H81T主板

在12月24日进行了硬件升级，主要原因还是因为我准备构建服务，担心我家的硬盘给我整崩了。
还有一个原因是双十一的时候800购买了3年的虚拟机，有了一个公网ip，可以对外服务了。

基于以上原因，升级了一下配置

1. 镁光MX200固态硬盘256G 2年使用龄.不过因为笔记本一直吃灰，就从笔记本上扣了下来。
2. 海盗船笔记本内存16G   笔记本的内存、硬盘都被我扣了。彻底报废，cpu针脚不对，不然i5双核的cpu也能用。
3. G3460 cpu             cpu成大短板，不过没事，再短没有我家方正100M带宽短。
4. 华硕H81T主板          主板棒棒哒。服务器退休以后还可以升级下部件做一体机，倒不如说现在就可以逛贴吧。

## debian9.3.0系统

在3月份的时候机器已经有centos7 1611系统，不过把硬盘干了也得重来。
debian相比centos7依赖库新、多，系统本身轻量稳定。内核为4.x.x。python 3.x版本
但是离线安装版没有gcc、make等。无法安装网卡。离线安装gcc，但相关的deb包有40个以上。
直接放弃。

## centos7 1708系统

原机械硬盘安装的是1611版本，也算是升级了一下。
centos7相对而言比较稳定，但存在yum库老旧，大多数依赖需要手动配置yum源，如docker。
甚至也有需要单独安装的git。系统自带的python也是2.7.9版本。
但相对debian，初始安装时勾选development Envirment，会带有gcc、make等工具。
几乎一键安装网卡驱动。前提是先升级内核。。内核版本3.10.0，r8168驱动需要4.7以上内核。

### 安装的时候遇到的问题：

#### 选取install CentOS7或者 Test this media & install CentOS 7 直接黑屏?

上网查询之后发现是机器没有图形卡或者图形卡驱动不匹配导致的。
这里选择第三个选项Troubleshooting。再选择Install CentOS7 in basic graphics mode
此选项允许您在图形模式下安装CentOS的，即使安装程序无法加载正确的驱动程序为您的视频卡。如果你的屏幕会出现扭曲或使用安装CentOS选项时一片空白。

参考链接：http://www.cnblogs.com/yllinux/p/7238630.html

#### 安装完成之后没有gcc，无法安装网卡驱动?

依据之前1611安装经验，在miniInstall中需要勾选Development Envirment安装基础的开发工具，这样会带有gcc、make等工具。

### 内核升级

因为电脑没有网络，需要离线安装内核。

[下载制定版本的kernel、kernel-devel](http://elrepo.org/linux/kernel/el7/x86_64/RPMS/)

下载其中的kernel-ml-devel-4.14.8-1.el7.elrepo.x86_64.rpm、kernel-ml-4.14.8-1.el7.elrepo.x86_64.rpm。

```
~ yum -y install kernel-ml-devel-4.14.8-1.el7.elrepo.x86_64.rpm
~ yum -y install kernel-ml-4.14.8-1.el7.elrepo.x86_64.rpm
```

在内核安装完成之后还需要修改内核的启动顺序。

#### 修改grub中默认的内核版本

运行`~ grub2-editenv list`,查看当前默认启动内核。

查看所有内核：
`~ cat /boot/grub2/grub.cfg | grep menuentry`

复制第一个单词menuentry 后面单引号包裹的内核型号。与`grub2-editenv list`运行的结果`saved_entry=`是一样的。

修改最新的内核为默认启动：
`~ grub2-set-default 'Red Hat Enterprise Linux Server (4.4.0) 7.2 (Maipo)'`

最后调用`~ grub2-editenv list`查看是否更新默认启动的内核。

参考链接：http://www.voidcn.com/article/p-oojnbemz-bqr.html

### 安装网卡驱动

```
解压0010-r8168-8.045.08.tar.bz2:
~ bzip 0010-r8168-8.045.08.tar.bz2
~ tar -xvf 0010-r8168-8.045.08.tar
安装网卡驱动
~ cd r8168-8.045.08
~ sh autorun.sh
```

安装完成之后插上网线就可以看到有黄灯闪烁。

至此机器可以直接插网线到路由器上，通过ssh进行操作。

### 切换yum源为阿里云镜像源

有了网卡第一件事就是切换yum源，家里的破网实在蛋疼。
`curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo`
之后运行`yum makecache`生成缓存，切换完成~

