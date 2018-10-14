---
categories: ['Linux']
title: '如何快速统计服务器访问量'
date: 2009-12-21 18:00:00
---

最近看了javaeye上面**robbin**的一篇文章，学习到了很多知识。

[http://robbin.javaeye.com/blog/97287](http://robbin.javaeye.com/blog/97287) 如何快速统计RoR网站的访问量

我的服务器是Apache+tomcat的配置，上面跑了几个网站。都是基于apache 虚拟主机配置的。

apache配置如下

```
Include conf/vhost_*.conf
```

然后每个虚拟主机对应一个配置文件

例如下面的(一些信息用???代替了，都是域名和姓名等个人信息)

```
<VirtualHost *:80>
    ServerAdmin ???<a href="mailto:???@???.cn">@???.cn</a>
    DocumentRoot /home/???/www
    ServerName <a href="http://www./???.org">www.???.org</a>
    DirectoryIndex index.jsp index.htm
    ErrorLog logs/abc-error_log
    CustomLog "| /usr/sbin/rotatelogs /var/log/httpd/abc-access_%Y%m%d.log 86400" common
    <Location "/*.jsp">
         JkUriSet worker ajp13:localhost:8009
    </Location>
</VirtualHost>
```

这样配置后，就会在/var/log/httpd下面按照虚拟主机名字和日期，每天生成一个访问日志文件

**统计每日动态请求处理总数**

```
cat /var/log/httpd/abc-access_20081116.log |grep "200 "|wc -l
```

**统计URL的访问频度**

```
cat /var/log/httpd/abc-access_20081116.log |grep "200 "|awk '{print $7}'|sort|uniq -c
```

**统计时间访问频度**

```
cat /var/log/httpd/abc-access_20081116.log |grep "200 "|awk '{print $4}'|awk -F : '{print $2}'|sort|uniq -c
```

**以上这些是基于CentOS+apache+tomcat实现的**

```
[root@ict ~]# cat /var/log/httpd/esdinchina-access_20081117.log |grep "200 "|awk '{print $4}'|awk -F : '{print $2}'|sort|uniq -c
```

