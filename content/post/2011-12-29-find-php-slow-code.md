---
categories: ['PHP']
title: 'php-fpm查找php慢速代码'
date: 2011-12-29 21:45:00
---
前两天帮助一个客户优化ecshop。
他的ecshop代码是找人修改过的，修改后速度已很很慢，大概打开一页需要9s吧。
而默认的ecshop在同样服务器上速度就很快。
开始走了很多弯路，因为只是速度慢，并无错误。就没有想到是代码的原因。
对比两个ecshop，首先发现首页加载的request数量差距很大，慢的站点105次，快的46次。
而且慢的站点1.66M大小，而快的只有455k。通过开启gzip和合并css、js等手段优化后，效果并不明显。
 
后来想到可能是因为找人修改过代码，开始怀疑到代码上（也确实没啥办法了）。
想到php-fpm可以记录下执行过慢的php脚本，于是修改php-fpm配置，加入了记录慢速php请求的代码。

下面是在php 5.3.8下的php-fpm.conf文件添加的内容

```
request_slowlog_timeout=5s
slowlog=/var/log/fpm_slow.log
```

这个配置会将php脚本执行超过5秒的，记录在/var/log/fpm_slow.log文件中。
 
如果你的php还是5.2.x的版本，那么php-fpm还是以补丁的形式执行，请参考下面的配置文件

```
The timeout (in seconds) for serving of single request after which a php backtrace will be dumped to slow.log file
      '0s' means 'off'
      <value name="request_slowlog_timeout">0s</value>
 
      The log file for slow requests
      <value name="slowlog">logs/slow.log</value>
```

最终在/var/log/fpm_slow.log文件里，发现很多如下记录

```
[27-Dec-2011 10:07:27]  [pool www] pid 8524

script_filename = /data/huaximall/flow.php

[0x0a0f41c0] fsockopen() !:649

[0x0a0f4104] _connect_sock() !:937

[0x0a0f3fdc] sock_to_host() !:693

[0x0a0f3f08] get_sock() !:372

[0x0a0f3e5c] get() !:952

[0x0a0f3d4c] getAll() !:291

[0x0a0f3c3c] get_child_tree() !:313

[0x0a0f3b18] get_child_tree() !:251

[0x0a0f2fa8] get_categories_tree() !:44
```

这里比较奇怪，人家的这个日志都可以显示出具体是哪个文件的哪行，我这里只有函数和行，而文件名的地方却是“！”号。。。。
这个问题我还没有解决，不过通过这些日志，我已经可以判断出来是flow.php的执行时间超过了5s。。。导致首页加载过慢。

后面分析flow.php，发现又调用了init.php等相关库和初始化文件。
最后发现fsockopen() _connect_sock() sock_to_host()这些函数是在尝试连接到memcached的服务器。而这个服务器的IP不对，导致这里延时很大。。。

还有个技巧就是在php页面里加入计算执行时间的输出，看看到底哪些行的执行时间过长。
具体时间的计算，可以参考下面代码

```
$start = microtime(true);
$end = microtime(true);
$time=$end-$start;
```

