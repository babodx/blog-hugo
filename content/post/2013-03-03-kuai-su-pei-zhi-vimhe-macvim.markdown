---
title: "快速配置vim和macvim"
date: 2013-03-03 14:41:00
categories: ['Vim']
---
vim以为需要安装各种插件和配置好vimrc才能适合我们开发使用。如果默认只安装vim不用任何插件，并不好用。

以前写过一篇[配置gvim](http://babodx.github.com/blog/2011/10/31/gvim-vimrc-file/)，那个时候还是手动安装各种差距和写vimrc

现在已经开始采用vundle来管理vim插件了。而且平台也换mac了。

mac下面自带了vim 7.3 但是我们在mac的图形界面下面还是原因使用macvim。所以要先安装上macvim。

```
brew install macvim
```

**安装vim套件**

所谓vim套件其实就是一些高手整理好的vimrc和配色方案，然后通过vundle来安装上vimrc里面需要的各种插件。来达到快速让vim达到一个很好使用和适合开发的程度。

我使用的是vundle-vimfiles项目，先抓一份文件到自己本地。

如果你很浏览自己原来的.vim，那就先备份一下。我是直接删除了。呵呵

切换到自己的目录`cd ~`

git代码到自己本地

```
git clone git://github.com/ywjno/vundle-vimfiles.git
```

用vundle-vimfiles文件夹链接到~/.vim

```
ln -s vundle-vimfiles ~/.vim
```

然后将vundle-vimfiles项目里的vimrc文件链接到~/.vimrc，vundle-vimfiles项目给我们提供了几个版本的vimrc，例如`vimrc`、`easy-vimrc`、`gvimrc`

我这里在console下面用vim，一般看代码还是喜欢用macvim打开。所以我用了两个配置文件。

链接到console下的vim配置文件

```
ln -s ~/.vim/vimrc ~/.vimrc
```

链接到macvim的配置文件

```
ln -s ~/.vim/gvimrc ~/.gvimrc
```

最后我们安装好Vundle项目来帮助我们安装vimrc里面需要的各种vim plugin

```
git clone git://github.com/gmarik/vundle.git ~/.vim/bundle/vundle
```

安装后，我们进入vim通过vundle来继续安装需要的各种plugin。进入vim后，输入如下命令安装插件

```
:BundleInstall
```

如果以后想更新插件，主要输入`:BundleInstall!`

**调整配色**

我的配色喜欢solarized_light，编辑~/.gvimrc在前面加入`:colorscheme solarized_light`

这样打开也就3-5分钟我们就完全配置好我们的vim编辑器了




