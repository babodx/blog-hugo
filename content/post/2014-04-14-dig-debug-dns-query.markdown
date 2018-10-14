---
title: "dig排查dns问题"
date: 2014-04-14 22:05:00
categories: ['Linux']
---
最近单位的某些域名在用联通3g访问的时候，经常有无法解析域名的情况。后来是采用dig逐步排查，解决了问题的。

##解决问题的思路##
1、通过ping、dig命令，先判断域名是否可以被解析。

2、如果只是某些机器不正常，就通过@参数指定dns服务器查询。

3、如果查询不到解析，就+tcp 采用tcp协议尝试下。

4、采用+trace通过递归查询，看看那个节点出现的问题

5、大招，这招还不行就放弃吧
```
dig +trace +recurse +all +qr -t NS xinlogs
```
<!--more-->

##正常现象##
以xinlogs.com为例，通过dig ns xinlogs.com查询到如下信息
```
; <<>> DiG 9.8.3-P1 <<>> ns xinlogs.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 64006
;; flags: qr rd ra; QUERY: 1, ANSWER: 2, AUTHORITY: 0, ADDITIONAL: 0

;; QUESTION SECTION:
;xinlogs.com.			IN	NS

;; ANSWER SECTION:
xinlogs.com.		600	IN	NS	f1g1ns2.dnspod.net.
xinlogs.com.		600	IN	NS	f1g1ns1.dnspod.net.

;; Query time: 431 msec
;; SERVER: 192.168.1.1#53(192.168.1.1)
;; WHEN: Mon Apr 14 22:34:06 2014
;; MSG SIZE  rcvd: 83
```
其中可以发现采用打NS为f1g1ns1.dnspod.net. 和f1g1ns2.dnspod.net.
然后我们再判断下NS服务器是否正常dig f1g1ns1.dnspod.net 发现也可以正常获取到f1g1ns1.dnspod.net的IP地址。

通过dig @8.8.8.8 +trace +short xinlogs.com，可以看到解析的全部过程如下
```
NS e.root-servers.net. from server 8.8.8.8 in 271 ms.
NS g.root-servers.net. from server 8.8.8.8 in 271 ms.
NS d.root-servers.net. from server 8.8.8.8 in 271 ms.
NS a.root-servers.net. from server 8.8.8.8 in 271 ms.
NS c.root-servers.net. from server 8.8.8.8 in 271 ms.
NS f.root-servers.net. from server 8.8.8.8 in 271 ms.
NS i.root-servers.net. from server 8.8.8.8 in 271 ms.
NS m.root-servers.net. from server 8.8.8.8 in 271 ms.
NS b.root-servers.net. from server 8.8.8.8 in 271 ms.
NS j.root-servers.net. from server 8.8.8.8 in 271 ms.
NS k.root-servers.net. from server 8.8.8.8 in 271 ms.
NS l.root-servers.net. from server 8.8.8.8 in 271 ms.
NS h.root-servers.net. from server 8.8.8.8 in 271 ms.
A 204.232.175.78 from server 183.60.52.217 in 38 ms.
```

##最常用的方法##
当我们查看一个域名是否可以解析的时候，直接采用如下命令
```
dig xinlogs.com
```

##通过指定dns服务器解析##
我们有时候需要通过特定dns服务器来查看解析结果，通过@8.8.8.8的参数可以指定采用8.8.8.8解析。
```
dig @8.8.8.8 xinlogs.com
```

##强制采用tcp查询##
dns服务器默认是udp协议的，如果有些服务器只开通了tcp协议。可以通过参数强制用tcp查询。
```
dig +tcp xinlogs.com
```

##trace##
通过+trace参数可以从上到下，逐级递归查询。看看到底是那个环节出现的问题。



