---
title: Hexo+NexT搭建私人博客
date: 2018-01-10 18:02:04
tags:
  - hexo
  - NexT
  - 私人博客
categories: 手记
---

通过Hexo+Next和Github Pages搭建私人博客。

* Hexo 是一个快速、简洁且高效的博客框架。Hexo 使用 Markdown（或其他渲染引擎）解析文章，在几秒内，即可利用靓丽的主题生成静态网页。
* NexT 是Hexo的一套漂亮精简的Theme，内集成第三方评论、RSS订阅、文章分类、文章标签、搜索等功能。
* Github Pages 是面向用户、组织和项目开放的公共静态页面搭建托管服务，站点可以被免费托管在Github上。

## 准备工作

* nodejs 环境，建议通过nvm安装。安装之后通过npm install yarn -g安装yarn包管理工具。
* git和github账号。

名词解释：
* 站点目录 - 博客根目录
* 主题目录 - 站点目录下theme目录下具体的主题目录，如landscape。
* 站点配置文件 - 站点目录下的_config.yml
* 主题配置文件 - 主题目录下的_config.yml，配置主题信息

## 初始化hexo

```
# 创建博客目录
~ mkdir bakka-blog && cd bakka-blog
# 安装hexo
~ yarn global add hexo
# 初始化博客
~ hexo init
# 启动hexo本地服务
~ hexo s
```

通过`hexo s`命令启动之后，博客服务可以通过`localhost:4000`访问。
初始化的博客会默认使用`landscape`主题。

## 关联博客与github

在github上创建{accout}.github.io的仓库，{accout}为github用户名。
此时可以访问{account}.github.io域名，得到404页面。我的仓库为sat1993.github.io，因此下面都以此为例。

修改博客目录下的`_config.yml(站点配置文件)`，将deploy节点下的type修改为git，并配置仓库信息。
```
deploy:
  type: git
  repository: https://github.com/sat1993/sat1993.github.io.git
  branch: master
```
如果`deploy.repository`想要配置ssh协议，需要配置git信息。

部署服务到github上。
```
# 安装hexo git部署插件。在站点目录下运行：
~ yarn add hexo-deployer-git
# 清空本地数据库
~ hexo clean
# 打包博客生成静态资源文件.
~ hexo generate
# 部署博客静态资源文件到github
~ hexo deploy
# 本地查看博客。服务为http://localhost:4000
~ hexo server
```
部署完成后，可以通过sat1993.github.io访问到`Hello World`博文。

### 域名绑定

通过sat1993.github.io访问实在是不雅。这里可以绑定自己的域名。
但绑定自己的域名是无法设置https安全证书的。

域名解析：
```
记录类型： CNAME
主机记录： 自定义域名
记录值： sat1993.github.io
```
hexo绑定CNAME文件。如果不添加该文件将一直跳转404页面。这也算是防止别人挂一个域名盗用博客。
```
# 进入站点目录。
~ echo "blog.bakka.site" > source/CNAME
```
通过`hexo g`和`hexo d`之后，便可以通过自己的域名访问服务了。
## 发布第一篇博文
通过`hexo new my-first-blog`可以初始化第一篇博文到`站点目录/source/_posts`下。
```
---
title: my-first-blog
date: 2018-01-10 20:01:34
tags:
categories:
---
```
在`---`后编写博文，通过`hexo s`启动服务，访问http://localhost:4000/2018/01/10/my-first-blog。
确认博客无误之后，通过`hexo g`和`hexo d`部署发布博文。通过http://blog.bakka.site/2018/01/10/my-first-blog访问正式的博文。

如果要删除博文，直接删除`站点目录/source/_posts/my-first-blog.md`再通过`hexo g && hexo d`部署到github上就ok了。

### 博文备份
hexo是通过本地解析将`站点目录/source/_posts/`下的md文件解析成html文件,保存在public目录下。
其他的静态资源也会保存在`站点目录/public`下。
如果不小心丢失本地的站点目录，将会导致无法更新博客。

在站点目录下通过`git init`构建git仓库。通过github创建仓库。
修改`.git/config`,在文件结尾添加。
```
[remote "origin"]
	url = 创建的git仓库地址。
	fetch = +refs/heads/*:refs/remotes/origin/*
```
因为本地已有.gitignore文件。因此我是没有修改.gitignore的。
若不存在该文件，则修改成如下内容。
```
.DS_Store
# 数据库文件
Thumbs.db
db.json
# 日志文件
*.log
# 依赖文件，可以通过yarn安装回来
node_modules/
# 打包后的静态资源，可通过hexo g打包
public/
# 部署的临时文件
.deploy*/
```
## hexo配置

主要配置站点基本信息，标签、菜单、搜索、博客名等。


