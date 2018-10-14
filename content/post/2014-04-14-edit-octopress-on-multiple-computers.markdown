---
title: "多电脑同步octopress"
date: 2014-04-14 21:30:00
categories: ['Octopress']
---
采用markdown编写博客文章，然后通过octopress发布到github上。感觉写博客就像编程一样有意思。
但是当我们有多台电脑的时候，如何同步呢？或者家里有了octopress环境，单位如何写博客呢？
下面是我常用的方法
<!--more-->

##安装步骤##

**需要安装git环境，可以使用git命令即可。**

**需要安装ruby环境，建议采用rvm安装**

有了以上条件后，只要将github上的blog源码clone到本地就可以来。我的博客是将正式发布的内容通过octopress的rake deploy发布到master分支。所有的文章markdown源码push到source分支。

当我需要在别的机器同步一份的时候，首先就是同步一份源码下来。
```
git clone -b source https://github.com/babodx/babodx.github.com.git blog
```

同步源码到blog目录后，进入到blog目录。删除_deploy目录，再将master分支同步到_deploy目录
```
cd blog
git clone https://github.com/babodx/babodx.github.com.git _deploy
```

这样就完成了同步，接着需要安装需要打gem
```
bundle install
```

以后如果两台机器需要同步，只要pull一份代码就可以了


