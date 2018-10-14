---
categories: ['Linux']
title: "drupal在nginx 0.8.15下的简洁URL配置"
date: 2010-01-01 18:06:00
---
最近在学习drupal，发现这个程序比xoops、joomla都灵活，对应的学习曲线也高。反正安装后，根本不知道真没设置。呵呵

下面先在nginx上配置了rewrite规则，让drupal支持简洁的URL

```
server
{
listen 80;
server_name drupal.xinlogs.com;
index index.html index.htm index.php;
root /data0/htdocs/drupal;
 
#limit_conn crawler 20;
location = / {
index index.php;
}
 
location / {
index index.php index.html;
 
if (!-f $request_filename) {
rewrite ^(.*)$ /index.php?q=$1 last;
break;
}
 
if (!-d $request_filename) {
rewrite ^(.*)$ /index.php?q=$1 last;
break;
}
}
 
location ~ .*.(php|php5)?$
{
fastcgi_pass unix:/tmp/php-cgi.sock;
#fastcgi_pass 127.0.0.1:9000;
fastcgi_index index.php;
include fcgi.conf;
}
 
location ~ .*.(gif|jpg|jpeg|png|bmp|swf)$
{
expires 30d;
}
 
location ~ .*.(js|css)?$
{
expires 1h;
}
 
log_format drupal_access '$remote_addr - $remote_user [$time_local] "$request" '
'$status $body_bytes_sent "$http_referer" '
'"$http_user_agent" $http_x_forwarded_for';
access_log /data1/logs/drupal_access.log drupal_access;
}
```
