---
categories: ['Linux']
title: 'squid缓存php页面'
date: 2012-01-17 19:25:00
---
这两天在看squid反向代理加速web应用方面的内容。

总体来看squid的cache都是缓存一些静态内容，例如图片、css、js等文件。而动态内容是不能缓存的。那有没有办法让squid缓存php文件呢？答案是肯定的。

首先用squid反向代理后，我们访问一个test.html的静态页面。

```
[root@localhost ~]# curl -I http://xinlogs.com:3128/test.html
HTTP/1.0 200 OK
Content-Type: text/html
Content-Length: 77
Accept-Ranges: bytes
Server: Tengine/1.2.1
Date: Tue, 17 Jan 2012 12:38:36 GMT
Last-Modified: Tue, 17 Jan 2012 01:29:47 GMT
X-Cache: HIT from squid.xinlogs.com
Via: 1.1 squid.xinlogs.com:3128 (squid/2.7.STABLE9)
Connection: close
```

可以看到X-Cache: HIT from squid.xinlogs.com，成功命中缓存。说明是被squid缓存的内容直接给我们了。
而访问一个动态页面呢？

```
[root@localhost ~]# curl -I http://xinlogs.com:3128/test.php

HTTP/1.0 200 OK

Server: Tengine/1.2.1

Date: Tue, 17 Jan 2012 12:40:52 GMT

Content-Type: text/html

Vary: Accept-Encoding

X-Powered-By: PHP/5.3.8

X-Cache: MISS from squid.xinlogs.com

Via: 1.1 squid.xinlogs.com:3128 (squid/2.7.STABLE9)

Connection: close
```

通过X-Cache: MISS from squid.xinlogs.com，可以看到是未命中，也就是从squid后端的nginx请求到的内容。

查了写资料，发现之所以不缓存动态脚本，主要是php文件没有输出Last-Modified: Tue, 17 Jan 2012 01:29:47 GMT这行头信息。

下面我们构造一个可以输出头信息的php文件test2.php来测试下。

```
<?php
$now = time();
$generatedAt = gmdate("D, d M Y H:i:s T",$now);
//print_r($generatedAt);
$lastModified = gmdate("D, d M Y 00:00:00 T",$now);
$expiresAt = gmdate("D, D M Y H:i:s T",strtotime($lastModified)+86400);
$maxAge=strtotime($expiresAt)-strtotime($generatedAt);
header('Last-modified: '.$lastModified);
header('Cache-control: max-age=' . $maxAge);
echo "Test".time();
```

这里我们通过header函数来构造了Last-Modified和Cache-control头信息。

再让我们通过curl -I来查看下是否可以缓存这个test2.php文件

```

[root@localhost ~]# curl -I http://xinlogs.com:3128/test2.php

HTTP/1.0 200 OK

Server: Tengine/1.2.1

Date: Tue, 17 Jan 2012 12:46:08 GMT

Content-Type: text/html

Vary: Accept-Encoding

X-Powered-By: PHP/5.3.8

Last-Modified: Tue, 17 Jan 2012 00:00:00 GMT

Cache-Control: max-age=-1169168

Age: 60

Content-Length: 14

X-Cache: HIT from squid.xinlogs.com

Via: 1.1 squid.xinlogs.com:3128 (squid/2.7.STABLE9)

Connection: close
```

通过上面信息，可以看到test2.php已经被缓存了。


**总结：通过在php脚本里输出header头信息，还是可以让squid缓存上php文件的。**

**但是目前还有一些问题没有解决：**

通过IE浏览器访问可以正常命中，通过chrome或者firefox浏览器每次访问都是如下未命中

```
1326794201.750      1 210.56.223.167 TCP_REFRESH_MISS/200 355 GET http://xinlogs.com:3128/test2.php - FIRST_UP_PARENT/xinlogs text/html
```

而且不同访问方式的第一次访问也都是未命中，比如wget -S第一次访问未命中，如果换了curl -I再访问，还是未命中。

尝试在squid.conf文件里加入了reload-into-ims on选项，但是无效。

以上问题估计还需要看看是不是需要配合nginx的设置来解决，因为静态文件是不存在这些问题的。
