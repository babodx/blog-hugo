---
title: "开始使用octopress"
date: 2013-02-13 14:54:00
categories: ['Octopress']
---
#概述#
最近在看[ruby-china.org](http://ruby-china.org)和[happycasts.net](http://happycasts.net)的视频。开始尝试使用git来做版本控制。原来都是使用svn，所以最近一直在折腾和学习。看到很多编程人员都开始采用octopress来代替wordpress写blog了。我也开始尝试使用这个octopress的blog系统。

octopress是一套基于ruby构建的blog管理系统，通过git来为我们的文章做版本控制，并且通过rake命令可以简单的完成博客的发布与远程站点的同步。配合github提供的pages功能，我们连自己的主机都可以省了。非常方便。
<!--more-->
##新建文章##
写文章只要进入到我们的octopress目录下，输入下面命令就可以创建一个新的blog文章。
	rake new_post\[title\]
其中title就是你文章的标题，可以根据自己实际情况填写。

##写文章##
我们创建的文章会保存在下面的目录内
	~/project/octopress/source/_posts
文件名为我们创建时候的日期-title.markdown的形式，中文的title会自动转为拼音。例如我这篇文章的名字就如下所示
	2013-02-13-kai-shi-shi-yong-octopress.markdown
我们只需要用我们熟悉的编辑器打开这个.markdown文件来写我们的文章就可以了。而文章的写作采用markdown语言。这个语言也是我最近才接触的，感觉非常容易上手，可以说完全是为了写作而诞生的。可以让我们愉快的写作，而不是考虑用什么字体，用什么格式，怎么对齐等问题。

有了这个很好的开始，后面就是慢慢学习octopress的技巧和markdown的各种语法了。
