---
categories: ['PHP']
title: "php eaccelerator vs fastcgi_cache性能比较"
date: 2012-01-19 10:15:00
---
最近放假一直在家里测试php如何优化性能。
今天比较了下默认php、eaccelerator和fastcgi_cache的性能。

先介绍下我的测试环境：
这次是在我VPS上做的测试：cpu 为1颗Intel(R) Core(TM) i7-2600 CPU @ 3.40GHz，内存256M
系统为centos 5.6 32bit
php版本 5.3.8
web服务采用Tengine/1.2.1，其实就是nginx

测试文件一个采用phpinfo.php，这个就是phpinfo()函数输出页面。另外一个就是采用我博客首页http://xinlogs.com
注：我的博客采用emlog 4.1
下面进入我的测试。。。。

php先用默认状态，不加载eaccelerator模块。
测试phpinfo.php,连续访问3000次，采用10个并发请求。

```
ab -n 3000 -c 10 http://xinlogs.com/phpinfo.php
Server Software:        Tengine/1.2.1
Server Hostname:        xinlogs.com
Server Port:            80

Document Path:          /phpinfo.php
Document Length:        54365 bytes

Concurrency Level:      10
Time taken for tests:   7.557223 seconds
Complete requests:      3000
Failed requests:        0
Write errors:           0
Total transferred:      163608000 bytes
HTML transferred:       163095000 bytes
Requests per second:    396.97 [#/sec] (mean)
Time per request:       25.191 [ms] (mean)
Time per request:       2.519 [ms] (mean, across all concurrent requests)
Transfer rate:          21141.76 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    0   0.0      0       2
Processing:     9   24  52.3     25    2432
Waiting:        9   24  52.2     25    2431
Total:          9   24  52.3     25    2432

Percentage of the requests served within a certain time (ms)
  50%     25
  66%     28
  75%     29
  80%     29
  90%     31
  95%     32
  98%     34
  99%     36
 100%   2432 (longest request)
```

可以看到每秒大约处理396.97 [#/sec] (mean)。99%响应时间在36ms以内
我又测试了几次，基本每秒处理在390到450浮动，响应时间在45ms以内吧。

再来看看我博客首页的测试情况

```
ab -n 3000 -c 10 http://xinlogs.com/
Server Software:        Tengine/1.2.1
Server Hostname:        xinlogs.com
Server Port:            80

Document Path:          /
Document Length:        21494 bytes

Concurrency Level:      10
Time taken for tests:   52.787368 seconds
Complete requests:      3000
Failed requests:        2970
   (Connect: 0, Length: 2970, Exceptions: 0)
Write errors:           0
Total transferred:      64990449 bytes
HTML transferred:       64432449 bytes
Requests per second:    56.83 [#/sec] (mean)
Time per request:       175.958 [ms] (mean)
Time per request:       17.596 [ms] (mean, across all concurrent requests)
Transfer rate:          1202.31 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    0   0.0      0       0
Processing:     9  175 661.8    127   18091
Waiting:        9  174 661.9    126   18091
Total:          9  175 661.8    127   18091

Percentage of the requests served within a certain time (ms)
  50%    127
  66%    149
  75%    166
  80%    178
  90%    199
  95%    210
  98%    226
  99%    348
 100%  18091 (longest request)
```

可以看到每秒处理56.83 [#/sec] (mean)请求，99%的响应时间在348ms内
多次测试后，基本保持在每秒处理55-60个请求，响应时间基本在350ms内。

接着我开启php的eaccelerator模块,开启后的phpinfo信息如下：

```
This program makes use of the Zend Scripting Language Engine:
Zend Engine v2.3.0, Copyright (c) 1998-2011 Zend Technologies
    with eAccelerator v0.9.6.1, Copyright (c) 2004-2010 eAccelerator, by eAccelerator
```

我们再次测试phpinfo.php文件

```
ab -n 3000 -c 10 http://xinlogs.com/phpinfo.php

Server Software:        Tengine/1.2.1
Server Hostname:        xinlogs.com
Server Port:            80

Document Path:          /phpinfo.php
Document Length:        56845 bytes

Concurrency Level:      10
Time taken for tests:   7.128081 seconds
Complete requests:      3000
Failed requests:        0
Write errors:           0
Total transferred:      171333080 bytes
HTML transferred:       170819225 bytes
Requests per second:    420.87 [#/sec] (mean)
Time per request:       23.760 [ms] (mean)
Time per request:       2.376 [ms] (mean, across all concurrent requests)
Transfer rate:          23472.94 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    9   7.9      9      40
Processing:     2   13  13.6     12     620
Waiting:        0    9  13.2      8     603
Total:         12   23  13.6     23     623

Percentage of the requests served within a certain time (ms)
  50%     23
  66%     29
  75%     31
  80%     31
  90%     32
  95%     34
  98%     37
  99%     39
 100%    623 (longest request)
```

从结果来看，加了eAccelerator后，phpinfo.php的测试结果并不是很快。每秒处理420.87请求，偶尔还会在380左右。并没有明显变化。

再来看看博客首页的测试。

```
ab -n 3000 -c 10 http://xinlogs.com/
Server Software:        Tengine/1.2.1
Server Hostname:        xinlogs.com
Server Port:            80

Document Path:          /
Document Length:        21488 bytes

Concurrency Level:      10
Time taken for tests:   23.177888 seconds
Complete requests:      3000
Failed requests:        2970
   (Connect: 0, Length: 2970, Exceptions: 0)
Write errors:           0
Total transferred:      64929588 bytes
HTML transferred:       64371588 bytes
Requests per second:    129.43 [#/sec] (mean)
Time per request:       77.260 [ms] (mean)
Time per request:       7.726 [ms] (mean, across all concurrent requests)
Transfer rate:          2735.67 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    0   1.0      0      23
Processing:     4   76 582.2     31   13655
Waiting:        4   76 582.2     31   13655
Total:          4   76 582.2     31   13655

Percentage of the requests served within a certain time (ms)
  50%     31
  66%     46
  75%     55
  80%     58
  90%     69
  95%     80
  98%    100
  99%    129
 100%  13655 (longest request)
```

从博客首页的测试可以发现，用了eAccelerator的效果还是很明显的。每秒处理请求129.43 [#/sec] (mean)，99%的响应时间在129ms内完成。
这个结果比不用eaccelerator的情况下，高了1倍多。

个人认为eAccelerator因为减少了php每次编译时间，所以当php程序需要调用很多类，或者调用其他php文件的时候。这个优化就体现出来了。就想我上面的测试，博客首页的加载确实快了，每秒处理的请求也多了。但是想phpinfo.php这样只有一个文件，并且只是简单的输出phpinfo信息的页面，eaccelerator起到的优化就很少了。

下面我们再来测试下fastcgi_cache有多快，这个就是缓存，完全不是优化php执行速度了，而是直接缓存上结果，下次直接调用缓存。

我们先用curl请求2次phpinfo.php 确定已经缓存上，再来测试。

```
-bash-3.2# curl -I http://xinlogs.com/phpinfo.php
HTTP/1.1 200 OK
Server: Tengine/1.2.1
Date: Thu, 19 Jan 2012 07:09:23 GMT
Content-Type: text/html
Connection: keep-alive
Vary: Accept-Encoding
X-Powered-By: PHP/5.3.8
X-Cache: HIT

ab -n 3000 -c 10 http://xinlogs.com/phpinfo.php
Server Software:        Tengine/1.2.1
Server Hostname:        xinlogs.com
Server Port:            80

Document Path:          /phpinfo.php
Document Length:        58255 bytes

Concurrency Level:      10
Time taken for tests:   0.656969 seconds
Complete requests:      3000
Failed requests:        0
Write errors:           0
Total transferred:      175320000 bytes
HTML transferred:       174765000 bytes
Requests per second:    4566.43 [#/sec] (mean)
Time per request:       2.190 [ms] (mean)
Time per request:       0.219 [ms] (mean, across all concurrent requests)
Transfer rate:          260605.90 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    0   0.0      0       0
Processing:     0    1   1.1      1       4
Waiting:        0    1   0.9      1       4
Total:          0    1   1.1      1       4

Percentage of the requests served within a certain time (ms)
  50%      1
  66%      2
  75%      2
  80%      3
  90%      3
  95%      3
  98%      4
  99%      4
 100%      4 (longest request)
```

看到什么是速度了吧。。。每秒处理请求 4566.43 [#/sec] (mean)，响应时间4ms以内完成。
这个就是fastcgi_cache的速度了.

也许一个phpinfo.php页面不能说明问题，我们再来看看博客首页的情况

```
ab -n 3000 -c 10 http://xinlogs.com/

Server Software:        Tengine/1.2.1
Server Hostname:        xinlogs.com
Server Port:            80

Document Path:          /
Document Length:        21485 bytes

Concurrency Level:      10
Time taken for tests:   0.578070 seconds
Complete requests:      3000
Failed requests:        0
Write errors:           0
Total transferred:      65055000 bytes
HTML transferred:       64455000 bytes
Requests per second:    5189.68 [#/sec] (mean)
Time per request:       1.927 [ms] (mean)
Time per request:       0.193 [ms] (mean, across all concurrent requests)
Transfer rate:          109900.19 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    0   0.0      0       0
Processing:     0    1   0.8      2       3
Waiting:        0    1   0.8      2       3
Total:          0    1   0.8      2       3
WARNING: The median and mean for the processing time are not within a normal deviation
        These results are probably not that reliable.
WARNING: The median and mean for the waiting time are not within a normal deviation
        These results are probably not that reliable.
WARNING: The median and mean for the total time are not within a normal deviation
        These results are probably not that reliable.

Percentage of the requests served within a certain time (ms)
  50%      2
  66%      2
  75%      2
  80%      2
  90%      2
  95%      2
  98%      2
  99%      3
 100%      3 (longest request)
```

再看看首页，因为采用了fastcgi_cache，每秒处理请求5189.68 [#/sec] (mean)，响应时间3ms内完成。

性能比较表格(主要提供每秒处理数做比较)

                    请求文件长度    php默认     eAccelerator加速模式      fastcgi_cache模式
phpinfo.php     58255 bytes     396.97          420.87                          4566.43 
博客首页        21485 bytes      56.83           129.43                           5189.68 

**总结：**

上面的测试虽然不是很严谨，但是通过这些已经可以发现fastcgi_cache的优势了，通过缓存php结果，大幅提升请求处理能力。
一般程序再通过eaccelerator模式加速后，可以提升2-3倍性能，而fastcgi_cache缓存的性能提升大约在10倍。

不过fastcgi_cache的应用也有它的弊端，就是要通过程序控制好刷新和不缓存内容。不然登陆后的用户都显示一个界面，或者发布的消息迟迟不能显示在首页都是它要解决的问题。

一般用户还是建议采用eaccelerator模块加速，因为这样基本无需修改代码，最容易实现。fastcgi_cache很难在不调整代码的情况下使用，因为他会缓存全部php文件，至少我的博客在启用了fastcgi_cache后，后台不正常。
