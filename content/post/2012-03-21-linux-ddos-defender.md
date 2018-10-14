---
categories: ['Linux']
title: 'Linux防御DDOS攻击'
date: 2012-03-21 19:33:00
---
这两天一个以前客户在万网购买的云主机一直被SYN Flood 攻击。

其实SYN Flood到是只能拖慢系统，没到死机的地步。问题是万网发现了DDOS攻击后，直接给云主机断网24小时。

这个处理太恶心了。不说想办法切断攻击的IP，而是直接断掉客户的网络。。。有点说不过去呀

电话咨询了下，他们是系统自动处理的，发现了直接断网。
 
一般应付DDOS攻击主要是调整SYN相关的内核参数，这个可以从/etc/sysctl.conf入手调整。

再有就是添加iptables规则，来限制syn和连接数量

最后就是通过脚本检查netstat状态，自动将连接数多的ip封掉。

一般linux系统能做到的就这些了，那些可以防ddos的硬防火墙老百姓搞不起。
 
调整syn相关参数和iptables网上资料很多，我就不说了。

整段封IP的话，可以用

```
iptables -I INPUT -s 192.0.0.0/8 -j DROP #来封掉192开头的全部ip
```
 
我是通过查看messages日志文件，发现了一些大量连接的IP。然后先手动封掉这些IP。下面是部分日志信息，当然这个是靠配置iptables来实现的日志

```
Mar 20 21:51:21 uhz001737 kernel: HTTP_ATTACK:IN=eth1 OUT= MAC=00:16:3e:00:5d:d3:c0:62:6b:ac:ea:c1:08:00 SRC=157.55.16.56 DST=223.4.118.120 LEN=48 TOS=0x1C PREC=0x20 TTL=112 ID=16250 DF PROTO=TCP SPT=61923 DPT=80 WINDOW=8192 RES=0x00 SYN URGP=0 
Mar 20 21:51:27 uhz001737 kernel: HTTP_ATTACK:IN=eth1 OUT= MAC=00:16:3e:00:5d:d3:c0:62:6b:ac:ea:c1:08:00 SRC=157.55.16.56 DST=223.4.118.120 LEN=48 TOS=0x1C PREC=0x20 TTL=112 ID=21741 DF PROTO=TCP SPT=61923 DPT=80 WINDOW=8192 RES=0x00 SYN URGP=0 
Mar 20 21:51:29 uhz001737 kernel: HTTP_ATTACK:IN=eth1 OUT= MAC=00:16:3e:00:5d:d3:c0:62:6b:ac:ed:c1:08:00 SRC=157.55.16.178 DST=223.4.118.120 LEN=48 TOS=0x1C PREC=0x20 TTL=112 ID=28201 DF PROTO=TCP SPT=14521 DPT=80 WINDOW=8192 RES=0x00 SYN URGP=0 
Mar 20 21:51:29 uhz001737 kernel: HTTP_ATTACK:IN=eth1 OUT= MAC=00:16:3e:00:5d:d3:c0:62:6b:ac:ed:c1:08:00 SRC=157.55.16.178 DST=223.4.118.120 LEN=48 TOS=0x1C PREC=0x20 TTL=112 ID=29875 DF PROTO=TCP SPT=14588 DPT=80 WINDOW=8192 RES=0x00 SYN URGP=0 
```
最后在介绍一款linux下防止DDOS的软件：DDoS-Defender-v2.1.0.tar.gz

其实DDOS也是通过分析netstat状态来判断大量连接的IP，然后采取封掉的策略。并可以设置封掉多久后自动解除。

基本经过上面的配置，现在万网没有再因为DDOS问题断网24小时了。
