---
categories: ['Linux']
title: 'nginx通过轮询避免php-fpm引起的502错误'
date: 2012-08-01 13:00:00
---
##问题背景##

最近客户的一个网站总是偶尔出现502错误

网站架构采用的就是nginx 1.0.10+php 5.3.8+php-fpm模式

##问题分析##

首先要检查下php-fpm的进程数使用情况

```
netstat -napo |grep "php-fpm" | wc -l
```

如果这个查询出来的数量接近了你在php-fpm.conf里设置的数量，说明是进程数量不过用了。果断增加

而我这次碰到的不是这个。继续分析

会不会是php程序执行时间过长造成超时？

这个可以通过我的另外一篇文章来查看[php-fpm查找php慢速代码]

如果是这个问题，我们可以通过修改nginx.conf和php-fpm.conf里面相关的超时设置来解决

nginx.conf里面主要是如下

```
fastcgi_connect_timeout 300;
fastcgi_send_timeout 300;
fastcgi_read_timeout 300;
```
php-fpm.conf里如要是如下

```
request_terminate_timeout = 10s
```

很不幸，我碰到的也不是这个问题

接着我开始分析是不是fastcgi缓存不够？

主要是在nginx.conf配置里修改如下参数

```
fastcgi_buffer_size 64k;
fastcgi_buffers 4 64k;
fastcgi_busy_buffers_size 128k
```

这个我还真不清楚要设置多大合适，网站说这个配置小了，有可能引发502.

##终极解决办法##

既然上面的常规办法不能解决，那我就想其他办法了。可以肯定的是502引起肯定是因为php-fpm引发的，也就是nginx将正确的客户端请求发给了后端的php-fpm进程，但是因为php-fpm进程的问题导致不能正确解析php代码。最终返回给了客户端502错误。

我觉得nginx既然upstream可以支持多组后端服务器轮询实现简单的负责均衡，并且可以做简单的健康检查。那我就用这个办法开多组php-fpm服务来实现一个php-fpm池，让nginx在这个php-fpm资源里通过stream轮询。

因为有了健康检查机制，这样就可以在错误到达客户端前换另外一个php-fpm进程重新解析了。

###主要配置方法###

先修改php-fpm.conf来开启多组php-fpm进程

我这里开启了php-cgi_www1和php-cgi_www2两组

```
[www1]
listen = /tmp/php-cgi_www1.sock
user = www
group = www
pm = dynamic
pm.max_children = 128
pm.start_servers = 32
pm.min_spare_servers = 32
pm.max_spare_servers = 96
pm.max_requests= 10240
request_terminate_timeout = 10s
 
[www2]
listen = /tmp/php-cgi_www2.sock
user = www
group = www
pm = dynamic
pm.max_children = 128
pm.start_servers = 32
pm.min_spare_servers = 32
pm.max_spare_servers = 96
pm.max_requests= 10240
request_terminate_timeout = 10s
```

然后重启php-fpm进程，并查看/tmp目录是否已经出现了php-cgi_www1.sock和php-cgi_www2.sock的文件

如果存在这两个文件，说明php-fpm配置ok了。我们继续修改nginx.conf配置

主要在http {} 的配置块内，加入我们要使用的轮询配置

```
upstream php_servers{
  server unix:/tmp/php-cgi_www1.sock;
  server unix:/tmp/php-cgi_www2.sock;
}
fastcgi_next_upstream error timeout invalid_header http_500 http_503;
```

接着修改我们原来处理php代码的fastcgi配置

```
	location ~ .*\.(php|php5)?$
	   {
	     fastcgi_pass  unix:/tmp/php-cgi_www.sock;
	     fastcgi_index index.php;
	     include fastcgi.conf;
	   }
```

将其中的fastcgi_pass由原来的php-cgi_www.sock单独的php-fpm进程修改为我们创建的php_servers轮询池

```
location ~ .*\.(php|php5)?$
   {
     fastcgi_pass php_servers;
     fastcgi_index index.php;
     include fastcgi.conf;
   }
```

通过这样的配置，可以肯定的是php-fpm因为采用两组来轮询工作，并且有fastcgi_next_upstream进程简单的健康检查，可以最大限度的避免502错误发生了。

