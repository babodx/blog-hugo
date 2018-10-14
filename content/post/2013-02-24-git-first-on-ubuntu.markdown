---
title: "git-first-on-ubuntu"
date: 2013-02-24 08:29:00
categories: ['git']
---
#ubuntu 12.10下初次使用git
前些时候在mac下面使用了git，感觉很棒。今天在单位安装了一台ubuntu 12.10的桌面系统，也开始尝试用git的方式来写blog。

##安装
ubuntu下面安装git非常容易。
    sudo apt-get install git-core

然后对git进行全局设置
    git config user.name "yourname"
    git config user.email "youremail"
设置后用下面命令可以查看
    git config -l
