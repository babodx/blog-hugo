---
categories: ['Linux']
title: 'monit监控linux服务'
date: 2012-02-02 18:23:00
---

最近负责的两台服务器需要监控nginx和memcached服务，防止网站访问异常。

我用monit来解决这个需求。

monit是一款linux下的开源软件，可以负责监控系统的服务、进程、文件等内容，并设置一定的条件下执行特定的action。

我们可以通过配置，让monit来检查网站的状态和memcached的状态，发现异常的时候，自动重启服务并邮件通知我们。

我配置了30s检查一次，出现问题发送邮件到我的gmail邮箱。我手机马上就能收到邮件提醒，非常效率。

 

安装配置步骤：

下载monit

```
wget http://mmonit.com/monit/dist/monit-5.3.2.tar.gz
```

解压并安装

```
tar zxvf monit-5.3.2.tar.gz

cd monit-5.3.2

./configure --prefix=/usr/local/monit

make

make install
```

配置

先将默认的配置文件拷贝到/etc目录下，然后编辑monitrc文件

```
cp monitrc /etc/
```

下面是我的一些配置：

```
set daemon  30   #设置30s检查一次

set logfile /var/log/monit.log   #设定日志文件

#设定邮件

set mailserver smtp.gmail.com PORT 587 USERNAME "yourname@gmail.com" PASSWORD "yourpassword" USING tlsv1 

#设定邮件格式

set mail-format {

      from: monit@huaximall.com

      subject: monit alert --  $EVENT $SERVICE

      message: $EVENT Service $SERVICE

                 Date:        $DATE

                 Action:      $ACTION

                 Host:        xinlogs.com

                 Description: $DESCRIPTION


            Your faithful employee,

            Monit

}

#设定提醒超时

set alert babodx@gmail.com with reminder on 3 cycles

#设定检查nginx服务

check process nginx with pidfile /usr/local/webserver/nginx-1.0.11/nginx.pid

  start program = "/usr/local/webserver/nginx-1.0.11/sbin/nginx"

  stop program = "/usr/bin/killall nginx"

  if failed host xinlogs.com port 80 protocol http

     and request "/t.html"

     then restart

  if 2 restarts within 3 cycles then timeout


#设定检查memcached服务

check process memcached with pidfile /var/run/memcache.pid

      start program = "/usr/local/memcached/bin/memcached -d -m 1024 -u root -p 11211 -c 1024 -P /var/run/memcache.pid"

      stop program = "/bin/kill -9 `cat /var/run/memcache.pid`; rm /var/run/memcached.pid"

      if failed host 127.0.0.1 port 11211 protocol MEMCACHE then restart

      if cpu > 60% for 2 cycles then alert

      if cpu > 98% for 5 cycles then restart

      if 2 restarts within 3 cycles then timeout

      group cache
```

下面再贴出来提醒的邮件样子给大家看看

这个是检查到nginx被终止后的提醒邮件

```
Does not exist Service nginx
                Date:        Thu, 02 Feb 2012 11:31:50
                Action:      restart
                Host:        xinlogs.com
                Description: process is not running

           Your faithful employee,
           Monit
```

下面是自动恢复nginx服务后的提醒邮件

```
Exists Service nginx
                Date:        Thu, 02 Feb 2012 11:32:28
                Action:      alert
                Host:        xinlogs.com
                Description: process is running with pid 6894

           Your faithful employee,
           Monit
```

有了这些就可以安心了，万一网站访问异常了。monit会尝试自动恢复的，而且第一时间通知我们。因为我的vps在美国，所以发送到gmail很及时。手机再设置个邮件提醒功能，基本就ok了。
