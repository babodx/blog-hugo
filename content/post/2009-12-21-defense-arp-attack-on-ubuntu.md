---
categories: ['Linux']
title: 'ubuntu下arp攻击防御和反击！'
date: 2009-12-21 18:00:00
---

今天在单位，发现有人在用聚生网管和p2p终结者

在windows下面，可以安装arp防火墙，还可以通过安装同类软件，进行检测。可是我已经转到ubuntu上了，总不能一旦被攻击，就回windows吧。于是上网找了一些关于linux下防止arp攻击的方法。

**第一种方法**

用静态arp.

这种方法就是通过在 /etc/ethers 建立ip和mac地址对应记录。然后用arp -f来读取记录。这样将网关的mac地址和ip都建立在静态arp文件里。就不容易被arp欺骗了。

还可以配合关闭arp解析，用`ifconfig eth0 -arp`

**第二中方法**

采用arping

``` 
babo@babo-laptop:~$ arping 
Usage: arping [-fqbDUAV] [-c count] [-w timeout] [-I device] [-s source] destination
-f : quit on first reply
-q : be quiet
-b : keep broadcasting, don't go unicast
-D : duplicate address detection mode
-U : Unsolicited ARP mode, update your neighbours
-A : ARP answer mode, update your neighbours
-V : print version and exit
-c count : how many packets to send
-w timeout : how long to wait for a reply
-I device : which ethernet device to use (eth0)
-s source : source ip address
destination : ask for what ip address
用法举例：比如我的ip 192.168.1.101 网关:192.168.1.1
就用arping -U -I eth0 -s 192.168.1.101 192.168.1.1
显示结果如下
Unicast reply from 192.168.1.1 [00:21:29:94:62:47] 1.999ms
Unicast reply from 192.168.1.1 [00:21:29:94:62:47] 9.571ms
Unicast reply from 192.168.1.1 [00:21:29:94:62:47] 1.245ms
Unicast reply from 192.168.1.1 [00:21:29:94:62:47] 1.227ms
Unicast reply from 192.168.1.1 [00:21:29:94:62:47] 1.390ms
Unicast reply from 192.168.1.1 [00:21:29:94:62:47] 4.526ms
Unicast reply from 192.168.1.1 [00:21:29:94:62:47] 1.294ms
Unicast reply from 192.168.1.1 [00:21:29:94:62:47] 1.245ms
Unicast reply from 192.168.1.1 [00:21:29:94:62:47] 1.239ms
Unicast reply from 192.168.1.1 [00:21:29:94:62:47] 1.266ms
Unicast reply from 192.168.1.1 [00:21:29:94:62:47] 1.259ms
Unicast reply from 192.168.1.1 [00:21:29:94:62:47] 1.267ms
Sent 12 probes (1 broadcast(s))
Received 12 response(s)
```

**终极解决办法**

采用arpoison来解决是最好的办法了，这个办法还是参考了《linux下arp攻击的解决方案［原］》的办法。

arpoison需要libnet的库才能正确编译，所以要下载libnet和arpoison两个软件。

[arpoison主页](http://www.packetfactory.net/libnet)

[libnet主页](http://www.packetfactory.net/libnet)

**安装**

现安装libnet,因为是源代码编译，需要gcc等我就不说了。去查看具体ubuntu源代码安装需要的软件包吧

```
tar zxvf libnet.tar.gz
cd libnet/
sudo ./configure
sudo make
sudo make install
```

编译过程中，会提示也些警告，没有关系。反正安装后，有/usr/lib/libnet.a就可以了
然后安装arpoison

```
tar zxvf arpoison-0.6.tar.gz 
cd arpoison/
sudo gcc arpoison.c /usr/lib/libnet.a -o arpoison
sudo mv arpoison /usr/sbin/
```

用法举例：

```
babo@babo-laptop:~$ sudo arpoison
Usage: -i <device> -d <dest IP> -s <src IP> -t <target MAC> -r <src MAC> [-a] [-w time between packets] [-n number to send]
说明
-i 指定发送arp包的网卡接口eth0
-d 192.168.1.1 指定目的ip为192.168.1.1
-s 192.168.1.101 指定源ip为192.168.1.101
-t ff:ff:ff:ff:ff:ff 指定目的mac地址为ff:ff:ff:ff:ff:ff(arp广播地址)
-r 00:1c:bf:03:9f:c7 指定源mac地址为00:1c:bf:03:9f:c7
```

比如我想防止arp攻击

`sudo arpoison -i eth0 -d 192.168.1.1 -s 192.168.1.101 -t ff:ff:ff:ff:ff:ff -r 00:1c:bf:03:9f:c7`

比如我想攻击192.168.1.50的机器不让他上网

`sudo arpoison -i eth0 -d 192.168.1.50 -s 192.168.1.1 -t ff:ff:ff:ff:ff:ff -r 00:1c:bf:03:9f:c7`

**一些猜想**

如果在本机开始NAT服务

然后通过arp欺骗把对方的网关地址欺骗到自己这里，因为自己这里开了NAT，对方应该也可以上网吧。然后在本地开wireshark抓包，对方就不知不觉的被监视了。这些想法没有测试，不知道这样欺骗的办法，NAT是否可以。

