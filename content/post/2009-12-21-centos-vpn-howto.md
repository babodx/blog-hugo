---
categories: ['Linux']
title: "CentOS 5.3架设VPN和619错误排除"
date: 2009-12-21 18:00:00
---
我买这个VPS的主机，一个目的是用来做Blog空间，另外一个就是用来跑vpn。

先按照[http://rashost.com/blog/centos5-pptpd-vpn](http://rashost.com/blog/centos5-pptpd-vpn)这里的文章安装vpn服务。

**内核支持**

pptpd VPN需要内核支持mppe，我们的VPS自带的内核已经把mppe编译进去了，没有把mppe另外当作内核的模块。

**软件安装**

要安装pptpd VPN，ppp和iptables这两个软件是必须安装的，安装命令：`yum install -y ppp iptables`

然后到[http://www.poptop.org/](http://www.poptop.org/)下载pptpd的rpm包并安装，下载的时候要注意下面几点：

* 好像只有1.3.3版本有rpm包，其他版本只有源代码 
* 没有el5或者centos5的rpm包，用rh4的rpm包可以安装在centos 5上 
* 64位的系统要下载64位的rpm包，32位的系统要下载32位的rpm包，别搞错了 

64位系统安装命令：`rpm -ivh pptpd-1.3.3-1.rhel4.x86_64.rpm`
32位系统安装命令：`<pre>rpm -ivh pptpd-1.3.3-1.rhel4.i386.rpm`

编辑配置文件 /etc/ppp/options.pptpd 

内容如下：

```
name pptpd refuse-pap refuse-chap refuse-mschap require-mschap-v2 require-mppe-128 proxyarp lock nobsdcomp novj novjccomp nologfd ms-dns 208.67.222.222 ms-dns 208.67.220.220 
```

编辑配置文件 /etc/pptpd.conf 内容如下：

```
option /etc/ppp/options.pptpd logwtmp localip 192.168.92.1 remoteip 192.168.92.11-15
```

编辑配置文件 /etc/ppp/chap-secrets,配置用户名为johndoe，密码为password，内容如下：

```
johndoe pptpd password *
```

修改配置文件/etc/sysctl.conf中的相应内容如下：

```
net.ipv4.ip_forward = 1
```

配置iptables:

```
iptables -t nat -A POSTROUTING -o eth0 -s 192.168.92.0/24 -j MASQUERADE /etc/init.d/iptables save /etc/init.d/iptables restart
```

设置iptables和pptpd开机自动启动：

```
chkconfig pptpd on chkconfig iptables on
```

然后运行reboot重新启动即可

##错误排除##

完成以上配置，通过Windows拨号到VPN，一定提示619错误。

引发619错误的原因有很多，比如密码不正确等，都报这个错误。

我们可以通过/var/log/messages查看下日志，找到问题原因

```
Dec 20 06:45:12 204-74-212-217 pptpd[18317]: CTRL: Client 221.223.48.186 control connection started
Dec 20 06:45:12 204-74-212-217 pptpd[18317]: CTRL: Starting call (launching pppd, opening GRE)
Dec 20 06:45:12 204-74-212-217 pppd[18318]: Plugin /usr/lib/pptpd/pptpd-logwtmp.so is for pppd version 2.4.3, this is 2.4.4
Dec 20 06:45:12 204-74-212-217 pptpd[18317]: GRE: read(fd=6,buffer=804e5a0,len=8196) from PTY failed: status = -1 error = Input/output error, usually caused by unexpected termination of pppd, check option syntax and pppd logs
Dec 20 06:45:12 204-74-212-217 pptpd[18317]: CTRL: PTY read or GRE write failed (pty,gre)=(6,7)
Dec 20 06:45:12 204-74-212-217 pptpd[18317]: CTRL: Client 221.223.48.186 control connection finished
```

后来有找了一些文档查看，和一条一条屏蔽配置语法
发现是logwtmp这个配置选项出现问题。最后编辑/etc/pptpd.conf文件
注释掉logwtmp选项，重启pptpd服务，就可以正常登陆了。
