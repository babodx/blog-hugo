---
categories: ['Linux']
title: 'apache下多虚拟主机的配置与管理'
date: 2009-12-30 16:08:00
---
当一台服务器想给多个域名提供web服务的时候，我们可以使用apache虚拟主机配置。

apache的配置文件在centos系统下，默认放在/etc/httpd/conf目录下的httpd.conf文件里。

虚拟主机的配置如下

```
#############TEST VirtualHost
<VirtualHost *:80>
ServerAdmin babodx@gmail.com
DocumentRoot "/home/babo"
ServerName www.xinlogs.com
DirectoryIndex index.html
ErrorLog logs/www.xinlogs.com_error_log
CustomLog logs/www.xinlogs.com-access_log common
 
</VirtualHost>

```

如果我们apache给10个或更多的域名提供web服务，这样的话，我们的httpd.conf就会有很多<VirtualHost *:80>这样的配置段落，看起来很长，管理起来也很麻烦。而且一个apache同时给几十个web域名提供虚拟主机，完全没有问题。那该如何写配置文件便于我们管理呢？

##解决办法##

为了管理方便，配置文件的结构清晰。

我们完全可以将每个虚拟主机的配置文件放在独立文件中，这样apache的主要配置文件httpd.conf看上去很简洁。

只要在最后加入

```
Include conf/vhost_*.conf
```
 
然后再conf目录里，每个虚拟主机的文件只要以vhost_开头，后面可以用自己的域名加.conf后缀。比如有个test.com的域名要做web。

我们就可以直接在conf目录创建一个vhost_test.com.conf文件，文件里写入虚拟主机配置，比如

```
#############TEST VirtualHost
<VirtualHost *:80>
ServerAdmin babodx@gmail.com
DocumentRoot "/home/test.com"
ServerName test.com
DirectoryIndex index.html
ErrorLog logs/test.com_error_log
CustomLog logs/test.com-access_log common
 
</VirtualHost>
```
通过这种配置结构，我们管理再多的虚拟主机也不用怕。只要修改conf目录下对应虚拟主机的配置文件即可。
