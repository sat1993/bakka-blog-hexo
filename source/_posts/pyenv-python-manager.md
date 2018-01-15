---
title: pyenv管理多版本python
comments: true
date: 2018-01-11 16:26:15
updated: 2018-01-11 16:26:15
tags:
  - python
  - linux
categories: python
---
## pyenv是什么？

centos7默认的python为2.7.9版本。而neovim需要3以上版本的python。
而python的卸载、覆盖安装都是很麻烦，并且容易出错。可以通过pyenv简单搞定多版本管理。

pyenv是python版本管理工具，可以快速在命令行切换不同版本的python。

### 安装

```
将pyenv项目clone到~/.pyenv目录
~ git clone https://github.com/pyenv/pyenv.git ~/.pyenv
修改~/.bash_profile文件，添加以下内容
~ vi ~/.bash_profile

export PYENV_ROOT="$HOME/.pyenv"
export PATH="$PYENV_ROOT/bin:$PATH"
if command -v pyenv 1>/dev/null 2>&1; then
  eval "$(pyenv init -)"
fi
刷新配置立即生效
~ source ~/.bash_profile
```
### 安装python所需依赖

安装python前需要先安装好yum依赖。
```
~ yum groupinstall 'Development Tools'
~ yum install zlib-devel bzip2-devel openssl-devel ncurese-devel
```

### 使用

pyenv在使用上非常简单，可以通过简单的命令快速切换版本，如下：
```
查看当前可以安装的Python版本
~ pyenv install --list
安装python
~ pyenv install 3.6.4
切换全局python版本为3.6.4
~ pyenv global 3.6.4
切换当前终端(shell)为3.6.4的python
~ pyenv local 3.6.4
查看当前使用的python版本
~ pyenv version
查看所有版本的python版本，带有*的为当前使用的版本
~ pyenv versions
```

### pip使用国内镜像

在`~/.pip/pip.conf`文件中添加或修改

```
[global]
index-url = http://mirrors.aliyun.com/pypi/simple/

[install]
trusted-host=mirrors.aliyun.com
```
这里使用阿里云的镜像源

## 常见的问题

### 通过pyenv install 3.6.4太慢了？

可以通过预先下载好python的包到~/.pyenv/cache目录下，再调用pyenv命令进行安装。

```
pyenv默认是没有cache目录的，如果有cache目录直接cd就好
~ make -p ~/.pyenv/cache && cd ~/.pyenv/cache
通过搜狐镜像下载python
~ wget http://mirrors.sohu.com/python/3.6.4/Python-3.6.4.tar.xz
可以看到直接跳过了下载的步骤，直接开始安装了
~ pyenv install 3.6.4
```

