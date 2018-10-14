---
categories: ['Linux']
title: 'nginx通过301统一入口域名'
date: 2011-11-13 16:34:00
---
我们知道网站的入口域名一般都是www.domain.com或者domain.com。有的网站选择带www而有的网站选择不带www。
但是搜索引擎会将www.domian.com和domian.com 认为是两个地址从而分散我们的权重。所以通过nginx的301转向来统计入口域名，有利于我们的网站提高权重。
 
以我的博客为例，服务器采用的就是nginx+fastcgi配合php模式。我的nginx关于博客的配置如下

```
server
  {
    listen       80;
    server_name  xinlogs.com www.xinlogs.com;
```


这样通过xinlogs.com或者www.xinlogs.com都可以访问到我的博客，但是这样做相当于我的博客首页有了两个地址，不利于搜索引擎收录和分配权重。
所以我采用nginx提供的rewrite来实现301转向，通过301转向的域名地址搜索引擎会将权重或者PR也一起转向过去。这样就不损失网站的PR和权重了。也就是把通过www或者不带www访问的这些流量和查询或者外链的权重整合到了一起。
下面是我的配置，我希望通过xinlogs.com作为我博客的统一入口域名，而带www的访问全部转向到xinlogs.com域名。

```
server
  {
    listen       80;
    server_name  xinlogs.com www.xinlogs.com;
    if ($host != "xinlogs.com") {
        rewrite ^/(.*)$ http://xinlogs.com/$1 permanent;
    }
```


经过上面的的配置，也就是那句if条件语句，将不是xinlogs.com的域名，全部转移到了xinlogs.com下。
 
下面我们测试下效果，因为只有明确的看到了转向信息，才证明我的配置成功了。所以采用curl命令测试。
测试www.xinlogs.com是否被转向

```
-bash-3.2# curl -i www.xinlogs.com
HTTP/1.1 301 Moved Permanently
Server: nginx/1.0.8
Date: Sun, 13 Nov 2011 05:03:22 GMT
Content-Type: text/html
Content-Length: 184
Connection: keep-alive
Location: http://xinlogs.com/
```
 
<html>
<head><title>301 Moved Permanently</title></head>
<body bgcolor="white">
<center><h1>301 Moved Permanently</h1></center>
<hr><center>nginx/1.0.8</center>
</body>
</html>
</div>


可以看到被成功转向到了location:http://xinlogs.com/了
再看看xinlogs.com是否可以正常访问。<b><span style="color:#e53333;">注意：这个很关键，因为有时候配置错了会导致循环转向让主域名入口无法访问。</span></b>

```
-bash-3.2# curl -i xinlogs.com
HTTP/1.1 200 OK
Server: nginx/1.0.8
Date: Sun, 13 Nov 2011 05:05:43 GMT
Content-Type: text/html; charset=UTF-8
Transfer-Encoding: chunked
Connection: keep-alive
Vary: Accept-Encoding
X-Powered-By: PHP/5.3.8
```

后面内容省略了。。。


 可以看到xinlogs.com域名访问正常，正确返回200 ok。而且下面的页面内容也正确返回了。


