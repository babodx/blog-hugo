---
categories: ['Hardware']
title: "H3C路由器 tracert超时，解决办法。"
date: 2011-09-21 19:00:00
---
单位的H3C路由器和交换机上，tracert总是超时。但是可以正常上网或者ping到目标。
后来查找资料，找到了解决办法。

解决方法：

    ip ttl-expires enable     ip unreachables enable

（官方说法这两条命令默认开启，可能跟版本有关，有的默认未开启。）

官方对这两条命令解释是：

ip ttl-expires enable命 令用来开启设备的ICMP超时报文的发送功能。undo ip ttl-expires命令用来关闭设备的ICMP超 时报文的发送功能。（缺省情况下，ICMP超时报文发送功能处于开启状 态。）

需要注意的是，关闭ICMP超时报文发送 功能后，设备不会再发送“TTL超时”ICMP差 错报文，但“重组超时”ICMP差错报文仍会正常发送。

ip unreachables enable命 令用来开启设备的ICMP目的不可达报文的发送功能。undo ip unreachables命令用来关闭设备的ICMP目 的不可达报文的发送功能。（缺省情况下，ICMP目的不可达报文发送功 能处于关闭状态。）
 
