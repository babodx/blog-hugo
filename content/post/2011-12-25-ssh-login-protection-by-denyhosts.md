---
categories: ['Linux']
title: '通过denyhosts保护ssh登陆'
date: 2011-12-25 18:31:00
---
今天查看服务器的/var/log/secure日志，发现很多类似下面的信息

`PAM 6 more authentication failures; logname= uid=0 euid=0 tty=ssh ruser= rhost=121.176.35.192  user=root`

说明经常有人通过ssh尝试登陆，并不断尝试猜测root的密码。

这样总让人尝试我的密码也不是个事儿呀....于是通过denyhosts软件，开启了保护。

通过denyhosts我们可以设置登陆失败几次，就禁止这个IP再次登陆。并可以设置多少分钟后，放开保护。
 
**denyhosts介绍**

DenyHosts是Python语言写的一个程序，用DenyHosts可以阻止试图猜测SSH登录口令，它会分析/var/log/secure等日志文件，当发现同一IP在进行多次SSH密码尝试时就会记录IP到/etc/hosts.deny文件，从而达到自动屏蔽该IP的目的。

安装需求

首要要求有python环境，并版本在2.3以上

CentOS 默认安装了2.4.3版本，我们可以通过下面命令查看

```
[root@localhost ~]# python -V

Python 2.4.3
```

**注意大小写。**

其次是sshd需要支持tcp_wrappers。这个一般都支持。可以用下面命令判断

`ldd /usr/sbin/sshd |grep wrap`

如果显示：libwrap.so.0 => /lib/libwrap.so.0 (0x00db7000)表示支持

**安装**

当前最新的版本是2.6

[下载](wget http://downloads.sourceforge.net/project/denyhosts/denyhosts/2.6/DenyHosts-2.6.tar.gz?r=http%3A%2F%2Fsourceforge.net%2Fprojects%2Fdenyhosts%2Ffiles%2F&ts=1324820171&use_mirror=cdnetworks-kr-1)

**解压安装**

```
tar zxvf DenyHosts-2.6.tar.gz 

cd DenyHosts-2.6

python setup.py install
```

默认将程序安装在/usr/share/denyhosts

库文件在/usr/lib/python2.4/site-packages/DenyHosts/

denyhosts.py安装到/usr/bin

**设置启动脚本**

```
cd /usr/share/denyhosts/

cp daemon-control-dist daemon-control

chown root daemon-control

chmod 700 daemon-control

grep -v "^#" denyhosts.cfg-dist > denyhosts.cfg
```

这样就生成了一个标准的配置文件denyhosts.cfg

我们需要按照自己的想法，配置下这个denyhosts.cfg文件

```
SECURE_LOG = /var/log/secure

#表示系统中secure日志文件位置

HOSTS_DENY = /etc/hosts.deny

#表示hosts.deny配置文件在系统中位置

PURGE_DENY = 30m

#表示多久后解封IP

BLOCK_SERVICE  = sshd

DENY_THRESHOLD_INVALID = 1

#表示无效用户名，登陆失败几次就封IP

DENY_THRESHOLD_VALID = 5
#表示有效果的普通用户登陆几次就封IP

DENY_THRESHOLD_ROOT = 3

#表示root用户登陆失败几次封IP

DENY_THRESHOLD_RESTRICTED = 1

WORK_DIR = /usr/share/denyhosts/data

SUSPICIOUS_LOGIN_REPORT_ALLOWED_HOSTS=YES

HOSTNAME_LOOKUP=NO

表示是否做域名反解析

LOCK_FILE = /var/lock/subsys/denyhosts
```

启动denyhosts

`/usr/share/denyhosts/daemon-control start`

配置denyhosts随系统自启动

```
cd /etc/init.d

ln -s /usr/share/denyhosts/daemon-control denyhosts

chkconfig --add denyhosts

chkconfig --level 345 denyhosts on
```

查看效果

如果尝试登陆失败次数达到后，IP就会被记录到/etc/hosts.deny文件内，来达到阻止登陆的目的

我们也可以通过查看/var/log/denyhosts日志文件来判断是否有被封的IP

下面是我的机器部署denyhosts一段时间后封的IP

```
# DenyHosts: Sun Dec 25 21:40:13 2011 | sshd: 112.164.231.91

sshd: 112.164.231.91
```
