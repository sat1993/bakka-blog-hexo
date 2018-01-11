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

这是我阅读的hexo相关的博客 [www.cduyzh.com](http://www.cduyzh.com/tags/hexo/)、[thief.one](https://thief.one/2017/03/03/Hexo搭建博客教程/)

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
在`---`后编写博文，通过`hexo s`启动服务，访问http://localhost:4000/2018/01/10/my-first-blog 。
确认博客无误之后，通过`hexo g`和`hexo d`部署发布博文。通过http://blog.bakka.site/2018/01/10/my-first-blog 访问正式的博文。

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
[branch "master"]
	remote = origin
	merge = refs/heads/master
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
通过git命令上传仓库。

#### 上传主题仓库
上传站点目录时，我们发现主题所在仓库是无法上传到git上的。因为我们的主题目录本身也是`git clone`下来的。

这里可以通过删除主题仓库的git相关文件(不建议)：
```
~ rm -rf .git*
```

或者`fork https://github.com/iissnan/hexo-theme-next`作为自己的一个主题仓库。
修改`.git/config`文件：
```
[core]
	repositoryformatversion = 0
	filemode = true
	bare = false
	logallrefupdates = true
	ignorecase = true
	precomposeunicode = true
[remote "origin"]
	url = fork的主题仓库地址
	fetch = +refs/heads/*:refs/remotes/origin/*
[branch "master"]
	remote = origin
	merge = refs/heads/master
```

上传我们的主题改动。将该仓库作为我们独有改动过的仓库。

虽然这样导致我们会有2个git仓库存我们的博客代码，但是分站点、主题两个部分去维护，也是可以的。

## 站点配置

配置站点基本信息，如语言、作者、标题等。

### 站点基础配置

修改站点配置文件`_config.yml`，修改`# Site`下的配置：
```
# Site
# 标题
title: Bakka's Blog
# 子标题
subtitle:
# 站点描述
description:
# 作者信息，主要展示于footer上
author: 进击的马鹿
# 修改首页语言，默认为英语
language: zh-Hans
# 修改时区
timezone:
```
这里主要修改`title、author、language`三个属性。

### 配置主题为NexT

到站点目录，克隆主题到主题目录：
```
# 克隆NexT项目到themes/next目录下
~ git clone https://github.com/iissnan/hexo-theme-next.git themes/next
```

修改站点配置文件_config.yml当前主题为next(themes目录下的主题目录名)

```
theme: next
```

运行`hexo g && hexo d`生成静态文件并发布。

[NexT 官方文档](http://theme-next.iissnan.com/getting-started.html)

### 站点404页面设置

腾讯公益404页面，寻找丢失儿童，让大家一起关注此项公益事业！效果如下 http://www.ixirong.com/404.html

使用方法，新建 404.html 页面，放到主题的 source 目录下，内容如下：

```
<!DOCTYPE HTML>
<html>
<head>
  <meta http-equiv="content-type" content="text/html;charset=utf-8;"/>
  <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1" />
  <meta name="robots" content="all" />
  <meta name="robots" content="index,follow"/>
  <link rel="stylesheet" type="text/css" href="https://qzone.qq.com/gy/404/style/404style.css">
</head>
<body>
  <script type="text/plain" src="http://www.qq.com/404/search_children.js"
          charset="utf-8" homePageUrl="/"
          homePageName="回到我的主页">
  </script>
  <script src="https://qzone.qq.com/gy/404/data.js" charset="utf-8"></script>
  <script src="https://qzone.qq.com/gy/404/page.js" charset="utf-8"></script>
</body>
</html>
```

### 博文压缩

在站点目录下执行：

```
# 安装gulp工具命令
~ yarn global add gulp
# 安装gulp与gulp压缩插件
~ yarn add gulp-minify-css gulp-uglify gulp-htmlmin gulp-htmlclean gulp
```
在站点根目录下新建`gulpfile.js`，并填入以下内容:

```
var gulp = require('gulp');
var minifycss = require('gulp-minify-css');
var uglify = require('gulp-uglify');
var htmlmin = require('gulp-htmlmin');
var htmlclean = require('gulp-htmlclean');
// 压缩 public 目录 css
gulp.task('minify-css', function() {
    return gulp.src('./public/**/*.css')
        .pipe(minifycss())
        .pipe(gulp.dest('./public'));
});
// 压缩 public 目录 html
gulp.task('minify-html', function() {
  return gulp.src('./public/**/*.html')
    .pipe(htmlclean())
    .pipe(htmlmin({
         removeComments: true,
         minifyJS: true,
         minifyCSS: true,
         minifyURLs: true,
    }))
    .pipe(gulp.dest('./public'))
});
// 压缩 public/js 目录 js
gulp.task('minify-js', function() {
    return gulp.src('./public/**/*.js')
        .pipe(uglify())
        .pipe(gulp.dest('./public'));
});
// 执行 gulp 命令时执行的任务
gulp.task('default', [
    'minify-html','minify-css','minify-js'
]);
```

之后可以通过`hexo g && gulp`来生成静态资源并压缩了。

### 添加搜索功能

安装hexo-generator-searchdb,在站点目录下执行：
```
yarn add hexo-generator-searchdb
```
编辑站点配置文件_config.yml，添加以下内容:
```
search:
  path: search.xml
  field: post
  format: html
  limit: 10000
```

## 主题配置

主要配置主题的基本信息，标签、菜单、CopyRight等。

### 修改网站页签图标

修改主题配置文件_config.yml，在favicon字段中修改small、medium:
```
favicon:
  small: /images/favicon-16x16.png
  medium: /images/favicon-32x32.png
```
将自己的图标(16x16、32x32)放置到主题目录的`source/images`目录下。
```
~ mv my-16x16-icon.png theme/next/source/images/favicon-16x16.png
~ mv my-32x32-icon.png theme/next/source/images/favicon-32x32.png
```
刷新服务即可。

### 菜单管理

修改主题配置文件`_config.yml`，在`menu`字段中添加字段：
```
menu:
  home: /
  about: /about
  ...
```
修改主题目录下的`languages/zh-Hans.yml`文件,更新中文展示：
```
menu:
  host: 首页
  about: 关于马鹿
  ...
```
最后在主题目录下的`source`目录下创建about目录，并放入index.html文件即可。

### 添加标签、分类菜单

#### 添加标签菜单

修改主题配置文件，添加`menu.tags: /tags/ || tags`。
```
menu:
  tags: /tags/ || tags
```

定位到站点目录下，使用hexo新建一个页面tags:
```
hexo new page tags
```
编辑刚刚生成的页面(source/tags/index.md)，将页面类型设置为tags:
```
---
title: 标签
date: 2018-01-10 23:11:01
type: "tags"
comments: false
---
```
可以通过comments参数开启评论.

#### 添加分类菜单

修改主题配置文件，添加`menu.categories: /categories/ || th`
```
menu:
  categories: /categories/ || th
```
定位到站点目录下，使用hexo新建一个页面categories:
```
hexo new page categories
```
编辑刚刚生成的页面(source/categories/index.md)，将页面类型设置为categories:
```
---
title: 分类
date: 2018-01-10 23:11:43
type: "categories"
comments: false
---
```
可以通过comments参数开启评论

设置完毕后通过`hexo g&& hexo s`快速查看效果。

### 修改js、css CDN配置

通过修改主题的固定js、css到`公开静态资源仓库`，降低github的流量访问，提升读客访问博文。

这里推荐使用[Staticfile CDN](https://www.staticfile.org/)

修改主题配置文件_config.yml:

```
vendors:
  # Internal path prefix. Please do not edit it.
  _internal: vendors

  # Internal version: 2.1.3
  jquery: https://cdn.staticfile.org/jquery/2.1.3/jquery.min.js

  # Internal version: 2.1.5
  # See: http://fancyapps.com/fancybox/
  fancybox: https://cdn.staticfile.org/fancybox/2.1.5/jquery.fancybox.pack.js
  fancybox_css: https://cdn.staticfile.org/fancybox/2.1.5/jquery.fancybox.min.css

  # Internal version: 1.0.6
  # See: https://github.com/ftlabs/fastclick
  fastclick: https://cdn.staticfile.org/fastclick/1.0.6/fastclick.min.js

  # Internal version: 1.9.7
  # See: https://github.com/tuupola/jquery_lazyload
  lazyload: https://cdn.staticfile.org/jquery_lazyload/1.9.7/jquery.lazyload.min.js

  # Internal version: 1.2.1
  # See: http://VelocityJS.org
  velocity: https://cdn.staticfile.org/velocity/1.2.1/velocity.min.js

  # Internal version: 1.2.1
  # See: http://VelocityJS.org
  velocity_ui: https://cdn.staticfile.org/velocity/1.2.1/velocity.ui.min.js

  # Internal version: 0.7.9
  # See: https://faisalman.github.io/ua-parser-js/
  ua_parser: https://cdn.staticfile.org/UAParser.js/0.7.9/ua-parser.min.js

  # Internal version: 4.6.2
  # See: http://fontawesome.io/
  fontawesome: https://cdn.staticfile.org/font-awesome/4.6.2/css/font-awesome.min.css

  # Internal version: 1
  # https://www.algolia.com
  algolia_instant_js:
  algolia_instant_css:

  # Internal version: 1.0.2
  # See: https://github.com/HubSpot/pace
  # Or use direct links below:
  # pace: //cdn.bootcss.com/pace/1.0.2/pace.min.js
  # pace_css: //cdn.bootcss.com/pace/1.0.2/themes/blue/pace-theme-flash.min.css
  pace: https://cdn.staticfile.org/pace/1.0.2/pace.min.js
  pace_css: https://cdn.staticfile.org/pace/1.0.2/themes/blue/pace-theme-flash.min.css

  # Internal version: 1.0.0
  # https://github.com/hustcc/canvas-nest.js
  canvas_nest: https://cdn.staticfile.org/canvas-nest.js/1.0.0/canvas-nest.min.js
```

注释的部分是当前版本的NexT所使用的版本。建议引用的CDN版本一定要一直。
修改完成后，通过`hexo s`预览下效果。

### 添加顶部加载条

修改主题目录下的`layout/_custom/header.swig`文件，添加如下内容：
```
<script src="https://cdn.staticfile.org/pace/1.0.2/pace.min.js"></script>
<link href="https://cdn.staticfile.org/pace/1.0.2/themes/blue/pace-theme-flash.min.css" rel="stylesheet">
<style>
    .pace .pace-progress {
        background: #1E92FB; /*进度条颜色*/
        height: 3px;
    }
    .pace .pace-progress-inner {
         box-shadow: 0 0 10px #1E92FB, 0 0 5px     #1E92FB; /*阴影颜色*/
    }
    .pace .pace-activity {
        border-top-color: #1E92FB;    /*上边框颜色*/
        border-left-color: #1E92FB;    /*左边框颜色*/
    }
</style>
```
通过`hexo s`运行本地服务访问首页就可以看到效果了。

### 隐藏网页地步Powered By信息

修改主题配置文件_config.yml。修改`footer.powered`、`footer.theme.enable`和`footer.theme.version`为false：
```
footer:
  # -------------------------------------------------------------
  # Hexo link (Powered by Hexo).
  powered: false

  theme:
    # Theme & scheme info link (Theme - NexT.scheme).
    enable: false
    # Version info of NexT after scheme info (vX.X.X).
    version: false
```

