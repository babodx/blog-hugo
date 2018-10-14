---
title: "nginx通过http/1.1访问upstream"
date: 2014-08-07 16:03:39
categories: ["Linux"]
---

Nginx在做upstream的负载均衡的时候，默认请求后端应用服务器使用的是http/1.0协议。

单位有个应用在采用nginx做负载均衡后，经常出现一个10s卡住现象。而用浏览器直接访问后端则没有这个问题。通过在后端应用上tcpdump抓包分析，发现nginx提交过来的请求都是http/1.0协议，而浏览器直接过来的是http/1.1协议。

Nginx初期不支持通过http/1.1协议访问后端应用，直到1.1.4版本后才支持http/1.1协议访问后端。采用http/1.1协议后，因为支持keepalive，可以减少Nginx到后端的连接数。

##启用http/1.1协议访问upstream
只要在想要启用http/1.1访问的location段加入proxy_http_version和proxy_set_header两项即可。

```
location / {
  proxy_pass http://upstream_domain;
  proxy_http_version 1.1;
  proxy_set_header Connection "";

}

```
