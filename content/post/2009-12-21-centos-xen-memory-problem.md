---
categories: ['Virtualization']
title: "CentOS 5.3 安装虚拟化后的内存识别问题."
date: 2009-12-21 18:00:00
---

昨天单位到了2台SUN x4450.

**硬件配置相当强力.**

CPU 4颗6核的 Intel(R) Xeon(R) CPU           X7460 @ 2.66GHz

系统可以识别出24颗处理器

```
[root@localhost ~]# cat /proc/cpuinfo |grep processor|wc -l
24
```

内存64G 

在内存问题上,我郁闷了一阵子.我用dmesg、free、top查看，都是32G

开始以为CentOS 5.3的64位内核就能支持到32G呢。又尝试在启动参数里加mem=64G

又上论坛询问、查看官方文档，都说CentOS 5.3 64bit应该没有内存限制的。

后来我发现了，原来问题出在安装的虚拟化上面

用xm top可以正确识别内存。而且安装的系统，只是当作xm 里面的一个dom0来运行的。

