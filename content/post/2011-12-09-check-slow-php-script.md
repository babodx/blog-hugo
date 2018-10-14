---
categories: ['PHP']
title: '如何找到执行慢的php程序'
date: 2011-12-09 19:56:00
---

最近在帮助一个网站做性能优化。
从原来的windows平台切换到了linux平台。php环境也已经变为主流的Nginx+php-fpm+eAccelerator+Zend Optimization组合
打开了MySQL的慢连接，发现基本集中在论坛的表查询上，但是我们如何定位有哪些php程序在执行的时候过慢呢？

通过查看php-fpm.conf配置，可以找到如下配置信息

``` 
The timeout (in seconds) for serving of single request after which a php backtrace will be dumped to slow.log file
      '0s' means 'off'
      <value name="request_slowlog_timeout">0s</value>

      The log file for slow requests
      <value name="slowlog">logs/slow.log</value>
```

request_slowlog_timeout 项是一个设定超过多长时间就会被记录到slow.log日志中。0s为关闭这个选项。

下面的slowlog为设置slow.log文件的位置。

打开这个以后，我们就可以看到哪些php文件用了过多的执行时间，来重点优化。
而且php-fpm.log也回提示出执行时间超过这个设定的信息。

下面是php-fpm.log的提示

```
Dec 09 20:19:25.170259 [WARNING] fpm_request_check_timed_out(), line 146: child 28844, script '/data/htdocs/bbs/forumdisplay.php' (pool default) executing too slow (3.110822 sec), logging
```

提示forumdisplay.php文件执行过慢，用了3.110822秒

再来看看slow.log的内容

```
Dec 09 20:23:49.534250 pid 28865 (pool default)

script_filename = /data/htdocs/t/bbs/forumdisplay.php

[0xbff462a0] mysql_query() p:66

[0xbff47020] query() p:622
```

可以看到和上面的php-fpm.log是对应的的。

有了这个办法，我们就可以随时查看网站中到底是什么程序执行速度慢，来重点优化了！
