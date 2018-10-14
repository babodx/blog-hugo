---
categories: ['Linux']
title: '伪静态规则调试技巧'
date: 2011-12-01 21:46:00
---
最近在威客网上接了个任务，帮助调整伪静态规则。WEB服务是apache的，主要就是修改rewrite规则。

伪静态规则写起来比较麻烦，关键是正则表达是写完总要测试，而我们不知道这个rewrite该怎么测试。我们需要准确知道服务器是怎么匹配我们的规则的，匹配后又发生了什么。这也就是我今天要写的调试技巧。

目前我们采用的WEB Server主要是apache和nginx（Windows下的IIS我不用，只有基于Linux的）。

其实所谓技巧就是通过打开rewrite的日志功能，然后每次访问后，我们查看rewrite日志来分析我们的规则是否正确。

**APACHE开启rewrite办法**

在httpd.conf文件里加入

```
RewriteLog "/xampp/apache/logs/RewriteLog.txt" 
RewriteLogLevel 9
```

我这里没用Linux下的apache，直接在我windows下的xampp环境里测试的

访问后的rewrite日志文件如下

```
127.0.0.1 - - [01/Dec/2011:15:38:38 +0800] [localhost/sid#775148][rid#6ba00d8/initial] (3) [perdir C:/xampp/htdocs/] strip per-dir prefix: C:/xampp/htdocs/q-123.html -> q-123.html
127.0.0.1 - - [01/Dec/2011:15:38:38 +0800] [localhost/sid#775148][rid#6ba00d8/initial] (3) [perdir C:/xampp/htdocs/] applying pattern '^.*$' to uri 'q-123.html'
127.0.0.1 - - [01/Dec/2011:15:38:38 +0800] [localhost/sid#775148][rid#6ba00d8/initial] (4) [perdir C:/xampp/htdocs/] RewriteCond: input='C:/xampp/htdocs/q-123.html' pattern='!-f' => matched
127.0.0.1 - - [01/Dec/2011:15:38:38 +0800] [localhost/sid#775148][rid#6ba00d8/initial] (4) [perdir C:/xampp/htdocs/] RewriteCond: input='C:/xampp/htdocs/q-123.html' pattern='!-d' => matched
127.0.0.1 - - [01/Dec/2011:15:38:38 +0800] [localhost/sid#775148][rid#6ba00d8/initial] (2) [perdir C:/xampp/htdocs/] rewrite 'q-123.html' -> 'index.php?q-123.html'
127.0.0.1 - - [01/Dec/2011:15:38:38 +0800] [localhost/sid#775148][rid#6ba00d8/initial] (3) split uri=index.php?q-123.html -> uri=index.php, args=q-123.html
127.0.0.1 - - [01/Dec/2011:15:38:38 +0800] [localhost/sid#775148][rid#6ba00d8/initial] (3) [perdir C:/xampp/htdocs/] add per-dir prefix: index.php -> C:/xampp/htdocs/index.php
127.0.0.1 - - [01/Dec/2011:15:38:38 +0800] [localhost/sid#775148][rid#6ba00d8/initial] (2) [perdir C:/xampp/htdocs/] trying to replace prefix C:/xampp/htdocs/ with /
127.0.0.1 - - [01/Dec/2011:15:38:38 +0800] [localhost/sid#775148][rid#6ba00d8/initial] (5) strip matching prefix: C:/xampp/htdocs/index.php -> index.php
127.0.0.1 - - [01/Dec/2011:15:38:38 +0800] [localhost/sid#775148][rid#6ba00d8/initial] (4) add subst prefix: index.php -> /index.php
127.0.0.1 - - [01/Dec/2011:15:38:38 +0800] [localhost/sid#775148][rid#6ba00d8/initial] (1) [perdir C:/xampp/htdocs/] internal redirect with /index.php [INTERNAL REDIRECT]
127.0.0.1 - - [01/Dec/2011:15:38:38 +0800] [localhost/sid#775148][rid#6bdc840/initial/redir#1] (3) [perdir C:/xampp/htdocs/] strip per-dir prefix: C:/xampp/htdocs/index.php -> index.php
```

还是很详细的，用来分析我们的rewrite规则是否生效和是否有错误足够了！

**Nginx开启rewrite日志办法**

在nginx.conf的配置文件里，加入如下选项内容

```
rewrite_log on;
```
