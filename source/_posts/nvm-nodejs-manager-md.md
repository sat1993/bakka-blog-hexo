---
title: nvm管理多版本nodejs
comments: true
date: 2018-01-16 10:33:59
updated: 2018-01-16 10:33:59
tags:
  - linux
  - javascript
  - centos7
categories: javascript
---

## nvm简介

nvm是一个简单的命令行管理多版本nodejs工具。
可以在多个不同的nodejs版本中进行快捷切换，而且npm仓库不会冲突。

## nvm安装

[官方github-安装脚本](https://github.com/creationix/nvm#install-script)

```
~ curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.8/install.sh | bash
```

上述脚本会将`https://github.com/creationix/nvm.git`仓库克隆至`~/.nvm`目录，并会添加环境变量至`~/.bash_profile`:

```
~ vi .bash_profile
# 如果.bash_profile文件中没有以下内容，则添加.
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh" # This loads nvm
```
## nvm运用

* `nvm ls`查看当前操作系统安装的nodejs。
* `nvm ls-remote`查看可安装的nodejs版本。
* `nvm install v8.9.3`安装指定版本的nodejs。
* `nvm use v8.9.3`切换nodejs版本。
* `nvm version`或者`nvm current`查看当前使用的nodejs版本。
* `nvm alias default v8.9.3`指定系统默认版本的nodejs。
* `nvm uninstall v8.9.3`卸载指定版本的nodejs。

## 使用不同的nodejs

在项目目录下创建`.nvmrc`文件，文件指定nodejs的版本。

```
# 查看当前系统安装的nodejs
~ nvm ls
        v6.11.0
->       v8.9.3
default -> v8.9.3
node -> stable (-> v8.9.3) (default)
stable -> 8.9 (-> v8.9.3) (default)
iojs -> N/A (default)
lts/* -> lts/carbon (-> N/A)
lts/argon -> v4.8.7 (-> N/A)
lts/boron -> v6.12.3 (-> N/A)
lts/carbon -> v8.9.4 (-> N/A)
# 指定当前项目使用的nodejs版本
~ echo "v8.9.3" > .nvmrc
# 切换node版本
~ nvm use
Found '/path/to/project/.nvmrc' with version <v8.9.3>
Now using node v8.9.3 (npm v5.6.0)
```
