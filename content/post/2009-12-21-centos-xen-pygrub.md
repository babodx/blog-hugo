---
categories: ['Virtualization']
title: 'CentOS里的Xen配置pygrub'
date: 2009-12-21 18:00:00
---

如果用virt-install生成一台虚拟服务器
在查看配置的时候，会发现已经没有了kernel=这个配置来制定启动内核。而是用了

`bootloader = '/usr/bin/pygrub'`

关于这个，可以参考
[http://wiki.xensource.com/xenwiki/PyGrub](http://wiki.xensource.com/xenwiki/PyGrub)

CentOS 5.3的虚拟化，好像没有了XenU的内核，而是改用了这种新的方式。

反正我用kernel启动失败了。后来用这个方式已经可以正常使用虚拟服务器了。
