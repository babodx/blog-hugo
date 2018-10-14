---
title: "digitalocean-vps"
date: 2013-04-29 10:55:00
categories: ['Host']
---

##Digitalocean debian6 vps测试##

最近又折腾了一次VPS。 原来一直使用的是vpsee.com提供的最低配置VPS。

**配置**

* 1 core CPU
* 256 MB 内存
* 256 MB 交换
* 10 GB 硬盘
* 160 GB 流量
* 1 IP
* 5 机房可选

费用 $10/月

再来看看我最近选择的digitalocean的vps

**配置**

* 512MB / 1 CPU
* 20GB SSD Disk
* 地点：New York  Amsterdam  San Francisco

费用  $5/月

<!--more-->

通过上面比较可以看到内存多了256M、硬盘多10G、流量多了840G。
而且硬盘采用SSD，性能很给力！

我申请了一台基于debian6 32bit的测试了，下面是效果

默认系统很精简，启动后占用内存12M，具体见下图：
![debian6 内存占用](http://farm8.staticflickr.com/7048/8691651426_83dbc54a9c_b.jpg)

硬盘占用700M
![debian6 硬盘占用](http://farm9.staticflickr.com/8536/8690532229_4a3395dc6e_z.jpg)

##网速测试##
先看看和美国其他vps的文件传递速度
![到美国vps的网速](http://farm8.staticflickr.com/7045/8691651836_9f52659248_c.jpg)

再看看到国内的速度
![到国内网速](http://farm8.staticflickr.com/7045/8690532089_2b0987a779_c.jpg)

##总结##
总的来说性能很给力，网速也不错。而且价格便宜

我接下来再这台服务器上配置了varnish+nginx+php+mysql环境，性能也是不错了。跑了一个朋友基于wordpress的网站。优化了内核、配置了防火墙策略和防入侵、DDOS等，给varnish分配了32M。最终服务器占用90M，用ab和siege压力测试了下，并发300 压1万次，完全没问题。

关于debian6的服务器具体配置，我以后会写出来。
