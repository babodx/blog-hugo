---
categories: ['Windows']
title: win7下启动telnet--转载
date: 2010-01-12 14:00:00
---
因为我日常需要用到telnet来管理交换机，所以经常用到telnet。

升级到win7后，发现telnet不能用了，提示如下

![telnet报错截图](http://farm9.staticflickr.com/8382/8512218888_459238840f.jpg)
后来查找资料发现

Windows 7出于安全性考虑屏蔽了Telnet。

如何开启呢？

在开始菜单-》控制面板-》程序界面中，点“打开或关闭Windows功能”如下图

![添加功能](http://farm9.staticflickr.com/8378/8512221634_b60d3f0c62.jpg)

然后在弹出的"Windows功能"界面中，选择上Telnet客户端，点击确定按钮。如下图

![打开telnet客户端](http://farm9.staticflickr.com/8526/8512222420_fd27aa15fe.jpg)

通过上面这些设置，就打开了Windows默认屏蔽的Telnet功能了。
