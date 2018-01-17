---
title: neovim配置与插件记录
comments: true
date: 2018-01-16 10:53:53
updated: 2018-01-16 10:53:53
tags:
  - vim
categories: vim
---

## neovim
 neovim 是 vim 的重构版，完整继承了 vim 的操作键。并在此基础上开发了许多新特性。

## neovim安装

* macos / os x

```
~ brew update
~ brew install neovim
```

* centos7

```
yum -y install epel-release
curl -o /etc/yum.repos.d/dperson-neovim-epel-7.repo https://copr.fedorainfracloud.org/coprs/dperson/neovim/repo/epel-7/dperson-neovim-epel-7.repo
yum -y install neovim
```

## neovim配置

### 支持python插件

参考博客安装python3,[pyenv管理多版本python](http://blog.bakka.site/2018/01/11/pyenv-python-manager/)
neovim支持python插件，但需要有python3以及`neovim`依赖包支持。
使用的配置文件中有使用到python插件的。
运行`pip intall neovim`安装python neovim依赖包。

### 配置neovim

克隆配置文件到`.config`目录：
```
git clone https://github.com/sat1993/nvim-config.git ~/.config/nvim
```

克隆完成之后，安装本地插件：
```
# 进入nvim
~ nvim
# 会报很多的错误，这里一路回车进入
# 安装插件，插件会安装在~/.config/nvim/viml/plugged/目录
:pluginstall
# 更新远端插件，此命令可能需要运行多次。安装较慢。
:updateremoteplugins
```

安装完成后`:q!`退出，再`nvim`进入时没有报错，且可以看到如下页面说明安装完成：
![nvim-config-1](/assets/images/nvim-config-1.png)

## 插件记录

### roxma/nvim-completion-manager

[nvim-completion-manager](https://github.com/roxma/nvim-completion-manager)

代码自动补全框架。

![plugin-1](/assets/images/neovim-config-plugin-1.gif)

### fgrsnau/ncm-otherbuf

[ncm-otherbuf](https://github.com/fgrsnau/ncm-otherbuf)
nvim-completion-manager提供nvim buf相关提示补全的插件。

### roxma/ncm-github

[ncm-github](https://github.com/roxma/ncm-github)

![ncm-github](/assets/images/neovim-config-plugin-3.gif)

### roxma/nvim-cm-tern

[nvim-cm-tern](https://github.com/roxma/nvim-cm-tern)

用户目录需要配置`.tern-config`或者项目目录需要配置`.tern-project`

具体配置参考[tern reference manual](http://ternjs.net/doc/manual.html#configuration)

### shougo/neco-syntax

[neco-syntax](https://github.com/shougo/neco-syntax)

ncm句法补充

### shougo/neco-vim

for vim

[neco-vim](https://github.com/shougo/neco-vim)

ncm viml语法补充

### iamcco/mathjax-support-for-mkdp

for markdown

[mathjax-support-for-mkdp](https://github.com/iamcco/mathjax-support-for-mkdp)

![mathjax-support-for-mkdp](/assets/images/neovim-config-plugin-2.png)

### iamcco/markdown-preview.vim

for markdown

[iamcco/markdown-preview.vim](https://github.com/iamcco/markdown-preview.vim)

![markdown-preview.vim](/assets/images/neovim-config-plugin-2.png)

### joker1007/vim-markdown-quote-syntax

for markdown

[joker1007/vim-markdown-quote-syntax](https://github.com/joker1007/vim-markdown-quote-syntax)

markdown语法高亮展示

![vim-markdown-quote-syntax](/assets/images/neovim-config-plugin-5.png)

### w0rp/ale

[w0rp/ale](https://github.com/w0rp/ale)

代码规范检查框架。

### sgur/vim-editorconfig

[sgur/vim-editorconfig](https://github.com/sgur/vim-editorconfig)

vim editorconfig配置

### mattn/emmet-vim


[mattn/emmet-vim](https://github.com/mattn/emmet-vim)

![emmet-vim](/assets/images/neovim-config-plugin-6.gif)

emmet前身为zen coding，能快速生成html代码。

#### 使用案例
```
~ nvim index.html

输入html:5_,_为光标所在位置。
组合建<c-y><leader>，生成html5代码。
```

### calebeby/ncm-css

[calebeby/ncm-css](https://github.com/calebeby/ncm-css)

css自动补全。

### maksimr/vim-jsbeautify

[maksimr/vim-jsbeautify](https://github.com/maksimr/vim-jsbeautify)

按`.editconfig`的配置格式化js、jsx文件。

```
~ nvim .editconfig

;.editorconfig

root = true

[**.js]
indent_style = space
indent_size = 2

[**.json]
indent_style = space
indent_size = 2

[**.jsx]
e4x = true
indent_style = space
indent_size = 2

[**.css]
indent_style = space
indent_size = 2

[**.html]
indent_style = space
indent_size = 2
max_char = 78
brace_style = expand
```

组合键`<leader>sf`即可一键格式化当前的css、js、html、json、jsx文件。

### heavenshell/vim-jsdoc

[heavenshell/vim-jsdoc](https://github.com/heavenshell/vim-jsdoc)

![vim-jsdoc](https://github.com/heavenshell/vim-jsdoc/wiki/images/vim-jsdoc-screencast-preview.gif)

#### 使用

1. 移动光标到fuction行
2. 输入`:jsdoc`插入jsdoc

### galooshi/vim-import-js

[galooshi/vim-import-js](https://github.com/galooshi/vim-import-js)

![vim-import-js](https://camo.githubusercontent.com/fc3f7b2f8b5f5c43f4c7bc555f0302a3af6603e3/68747470733a2f2f7261772e6769746875622e636f6d2f67616c6f6f7368692f76696d2d696d706f72742d6a732f6d61737465722f64656d6f2e676966)

#### 使用

需要先通过npm安装`import-js`

`npm install -g import-js`

* `<leader>j`对应`:importjsword`。导入光标所在处变量的模块
* `<leader>i`对应`:importjsfix`。导入所有未导入的模块，并移除未使用的模块。
* `<leader>g`对应`:importjsgoto`。前往光标所在模块。

### tpope/vim-fugitive

[tpope/vim-fugitive](https://github.com/tpope/vim-fugitive)

#### 使用

* `:gstatus`查看文件状态，`-`添加或者移除提交列表中的文件。
* `:gcommit`提交git版本。
* `:gblame`提交blame备注信息。
* `:ggrep`以关键字查询提交信息。但一运行就报错。。
* `:glog`相当于`git log`
* `:gmove`相当于`git mv`
* `:gread`相当于`git checkout --filename`
* `:gbrowse`在浏览器中打开当前文件。

### junegunn/gv.vim

[junegunn/gv.vim](https://github.com/junegunn/gv.vim)

#### 使用

* `:gv`查看提交历史。和`git log`相等
* `:gv!`仅展示带有当前文件修改的提交记录
* `:gv?`展示当前文件的本地提交列表

### cohama/agit.vim

[cohama/agit.vim](https://github.com/cohama/agit.vim)

![agit.vim](https://camo.githubusercontent.com/4f9902124a9b63455414a837104a3e038c71defe/687474703a2f2f692e6779617a6f2e636f6d2f64303839356638386263643135653235323031373332356130316661383962632e676966)

#### 使用

`:agit`查看当前分支的提交状况。

### lokaltog/vim-easymotion

[lokaltog/vim-easymotion](https://github.com/lokaltog/vim-easymotion)

![vim-easymotion](https://camo.githubusercontent.com/d5f800b9602faaeccc2738c302776a8a11797a0e/68747470733a2f2f662e636c6f75642e6769746875622e636f6d2f6173736574732f333739373036322f323033393335392f61386539333864362d383939662d313165332d383738392d3630303235656138333635362e676966)

#### 使用

移动与`wbewbe`移动一样决定移动方式。如：
`<Leader><Leader>w`,按下相应的字母即可跳转过去。
同理可用`<leader><leader>e`和`<leader><leader>b`选择首字母还是单词末尾。

### terryma/vim-multiple-cursors

[terryma/vim-multiple-cursors](https://github.com/terryma/vim-multiple-cursors)

![vim-multiple-cursors](https://github.com/terryma/vim-multiple-cursors/blob/master/assets/example1.gif?raw=true)

上述案例中，vim命令如下：`2Gfp<C-n><C-n><C-n>cname`

移动到目标单词后，使用<C-n>选中一样的单词。

### itchyny/vim-cursorword
[itchyny/vim-cursorword](https://github.com/itchyny/vim-cursorword)

![vim-cursorword](https://raw.githubusercontent.com/wiki/itchyny/vim-cursorword/image/image.gif)

光标相同单词展示下划线。

### tpope/vim-surround

[tpope/vim-surround](https://github.com/tpope/vim-surround)

### kana/vim-textobj-user

[kana/vim-textobj-user](https://github.com/kana/vim-textobj-user)
扩展文本对象。

### terryma/vim-expand-region

[terryma/vim-expand-region](https://github.com/terryma/vim-expand-region)

![vim-expand-region](https://camo.githubusercontent.com/64655fb5626161f9245df9b562ff8584fc61067f/68747470733a2f2f7261772e6769746875622e636f6d2f74657272796d612f76696d2d657870616e642d726567696f6e2f6d61737465722f657870616e642d726567696f6e2e676966)
选中区块。`<S-+>`扩展选中区域。

### scrooloose/nerdcommenter

[scrooloose/nerdcommenter](https://github.com/scrooloose/nerdcommenter)

#### 使用

快速注释工具：

* `[count]<leader>cc`注释当前行或者Visual模式选中的块
* `[count]<leader>cn`与cc相同，但会强制注释
* `[count]<leader>cm`使用注释块注释，而不是注释行。
* `[count]<leader>ci`使用注释行注释，注释整行。
* `[count]<leader>cs`注释块，并格式化
* `[count]<leader>cy`与cc相同，优先拉取注释行。
* `<leader>c$`从当前光标注释到文件结尾
* `<leader>cA`在当前行的结束位置添加注释
* `<leader>ca`切换注释方式，如java从\\切换到\**\
* `[count]<leader>cl`行注释首列对齐。
* `[count]<leader>cb`使用对齐的行注释。
* `[count]<leader>cu`取消选中行的注释。

### kshenoy/vim-signature

[kshenoy/vim-signature](https://github.com/kshenoy/vim-signature)

![vim-signature](https://github.com/kshenoy/vim-signature/blob/images/screens/vim-signature_mark_jumps.gif?raw=true)

#### 使用

添加mark，并支持在mark中跳转。

* `mx` 添加x mark。x可以为其他字母
* `dmx` 删除x mark。
* `m,` 自动添加mark，如果之前最后的mark为b,则添加c mark
* `m.` 自动添加mark或取消，如果之前最后的mark为b，则添加c，若已使用m.添加过mark，则取消。
* `m-` 删除当前光标所在mark
* `m<Space>` 删除当前缓冲区所有的mark
* ]` 跳转到下一个mark
* [` 跳转到上一个mark
* ]' 跳转到下一个mark的行首
* [' 跳转到上一个mark的行首
* `] 按字母顺序向下跳转
* `[ 按字母顺序向上跳转
* '] 按字母顺序向下跳转到目标mark的行首
* '[ 按字母顺序向上跳转到目标mark的行首
* `m/` 打开展示所有mark的列表。
* `m[0-9]` 对应mark !@#$%^&*()。类型mark
* `m<S-[0-9]>` 移除对应的mark
* `]-` 跳转到下一个相同类型的mark
* `[-` 跳转到上一个相同类型的mark
* `]=` 跳转到下一个任意类型的mark
* `[=` 跳转到上一个任意类型的mark
* `m?` 打开展示所有类型mark的列表
* `m<BS>` BS为退格键，删除所有类型mark

### Shougo/vinarise.vim

[Shougo/vinarise.vim](https://github.com/Shougo/vinarise.vim)

编辑二进制文件。

### dyng/ctrlsf.vim

[dyng/ctrlsf.vim](https://github.com/dyng/ctrlsf.vim)

#### 使用

* `<leader>fs`对应`<Plug>CtrlSFPrompt`相当于`:CtrlSF`。查询当前光标下的单词
* `<leader>fw`对应`<Plug>CtrlSFCWordPath`相当于`:CtrlSF {word}`。查询特定单词。
* `<leader>fo`对应`:CtrlSFOpen`打开上次搜索的结果。

### simnalamburt/vim-mundo

[simnalamburt/vim-mundo](https://github.com/simnalamburt/vim-mundo)

![vim-mundo](https://camo.githubusercontent.com/88e7b28111c7c53ccb1b44556d4d6783533f911b/68747470733a2f2f73696d6e616c616d627572742e6769746875622e696f2f76696d2d6d756e646f2f73637265656e73686f742e706e67)

查看文件历史修改记录并回滚。

#### 使用

* `<leader>h`相当于`:MundoToggle`打开历史修改记录窗口。

### junegunn/vim-easy-align

[junegunn/vim-easy-align](https://github.com/junegunn/vim-easy-align)

![vim-easy-align](https://raw.githubusercontent.com/junegunn/i/master/easy-align/equals.gif)

vim快速对齐插件

#### 使用

* Visual模式下选中要对齐的框，输入`ga[count]=`，表示以等号对齐。count表示第几列=号对齐。可以为*，表示所有。
* 普通模式下输入`ga(范围选中如ip, 2j等)[count]=`，对齐范围内的=号。

### neoclide/vim-jsx-improve

[neoclide/vim-jsx-improve](https://github.com/neoclide/vim-jsx-improve)

jsx句法缩排工具。

#### 使用

* 选定jsx代码块，输入=号缩排。

### tpope/vim-repeat

[tpope/vim-repeat](https://github.com/tpope/vim-repeat)

`.`号重复功能支持。支持多个插件：
```
surround.vim
speeddating.vim
abolish.vim
unimpaired.vim
commentary.vim
vim-easyclip
```
### thinca/vim-quickrun

[thinca/vim-quickrun](https://github.com/thinca/vim-quickrun)

更快的运行命令shell命令

#### 使用

* `QuickRun [command]`运行shell命令

### kassio/neoterm

[kassio/neoterm](https://github.com/kassio/neoterm)

vim中打开终端窗口

#### 使用

* `<leader>tn`打开一个新的终端窗口。
* `<leader>to`打开终端窗口。
* `<leader>tt`显示或隐藏最后一个终端窗口。
* `<leader>th`关闭终端窗口。
* `<leader>tl`相当于终端的clear命令。
* `<leader>tc`相当于终端的<C-c>命令。
* `:T [command]`在终端中执行command命令。

### iamcco/smartIM.nvim

[iamcco/smartIM.nvim](https://github.com/iamcco/smartIM.nvim)

在出发插入模式和普通模式执行指定的方法。

### Konfekt/FastFold

[Konfekt/FastFold](https://github.com/Konfekt/FastFold)

折叠管理(zo、za、zr、zc、zj、zk、zuz)

### andymass/vim-matchup

[andymass/vim-matchup](https://github.com/andymass/vim-matchup)

![vim-matchup](https://raw.githubusercontent.com/wiki/andymass/vim-matchup/images/match-up-hl1.gif)

匹配代码块起始结尾。

### Shougo/denite.nvim

[Shougo/denite.nvim](https://github.com/Shougo/denite.nvim)

快速切换文件、缓冲区文件。

需要激活Python3接口`pip install neovim`

#### 使用

* `<C-p>`打开当前工作目录下的文件。`<C-j>和<C-k>`上下切换文件。
* `<leader>b`打开缓冲区文件列表。

#### 问题

##### <C-p>报错，No sucn file or directory: 'ag'。

![denite.vim](/assets/images/neovim-config-plugin-7.png)

需要安装`ag`命令。
```
~ brew update
~ brew install the_silver_searcher
```

### pocari/vim-denite-emoji

[pocari/vim-denite-emoji](https://github.com/pocari/vim-denite-emoji)

输入emoji表情。依赖以下两个插件：
* denite.vim
* vim-emoji

#### 使用

* `:Denite emoji`选择emoji即可。

### tweekmonster/helpful.vim
[tweekmonster/helpful.vim](https://github.com/tweekmonster/helpful.vim)

![helpful](https://cloud.githubusercontent.com/assets/111942/16898497/2bf0a402-4baa-11e6-9f9b-3793384d5894.png)

#### 使用

```
" Search for a function
:HelpfulVersion matchaddpos()

" Search for keys
:HelpfulVersion <.*>

" Case-insensitive search
:HelpfulVersion f11\c
```

### bfredl/nvim-miniyank

[bfredl/nvim-miniyank](https://github.com/bfredl/nvim-miniyank)

### tweekmonster/startuptime.vim

[tweekmonster/startuptime.vim](https://github.com/tweekmonster/startuptime.vim)

viml效率测试工具

### junegunn/vader.vim

[junegunn/vader.vim](https://github.com/junegunn/vader.vim)

viml调试工具

### jiangmiao/auto-pairs

[jiangmiao/auto-pairs](https://github.com/jiangmiao/auto-pairs)

自动闭合括号等。如输入`(`获得`()`。删除同理。

### itchyny/lightline.vim

[itchyny/lightline.vim](https://github.com/itchyny/lightline.vim)

美化vim下方状态栏。

### icymind/NeoSolarized

[icymind/NeoSolarized](https://github.com/icymind/NeoSolarized)

vim主题

### junegunn/vim-emoji

[junegunn/vim-emoji](https://github.com/junegunn/vim-emoji)

使vim支持编辑emoji。


