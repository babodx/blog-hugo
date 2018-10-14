---
title: "nginx反向代理websockets"
date: 2013-10-03 11:42:00
categories: ['Linux']
---

有些情况下，部署的服务器是只开80端口的，这个时候如果我们想要ssh怎么弄？

**就是本地开其他端口的web ssh client，然后通过nginx的80端口反向代理。这个一般都需要走webscokets的，下面我就把我折腾的东西记录下。**

##web client的选择##

我是用了一个基于python技术，html5，websockets实现的web界面ssh客户端。[!GateOne](https://github.com/liftoff/GateOne)

其实还有一款也不错 ［!Guacamole］(http://guac-dev.org/),这个是基于java技术实现的，可以实现VNC,RDP,SSH协议的客户端。

##GateOne安装##

安装很简单，我是在debian下的环境安装的。

我主要参考了http://liftoff.github.io/GateOne/About/index.html#installation

###大概步骤###

<!--more-->

安装需求环境Python和Tornado Framework.

```

babo@xinlogs:~$ python -V
Python 2.6.6

babo@xinlogs:~$ python -c "import tornado; print(tornado.version)"
3.1.1

```

python一般系统会自带的，tornado需要采用pip安装下。也可以用easy_install安装

```
sudo pip install tornado kerberos
```

下载gateone的源码，然后用setup.py安装。默认安装在/opt/gateone下面

```
tar zxvf gateone*.tar.gz; cd gateone*; sudo python setup.py install
```

####配置####
我主要是为了方便，禁用了https方式，直接采用http协议了。这样nginx反向代理容易，要不还要配置nginx的443端口和证书。

修改settings/10server.conf,我主要修改了几项

```
            "disable_ssl": true,
            "origins": ["yunwei.biz","localhost", "127.0.0.1:7000", "xinlogs"],
            "port": 7000,
            "url_prefix": "/",

```


##Nginx配置##

其实nginx的配置不难，关键大坑在于对webscokets的支持上面。我原来安装的是1.4.0d的版本，配置后只要到了websockets的时候，就返回400错误。我看文档说说从1.3.x后就支持websockets了，于是就以为自己配置问题。修改header大小限制，超时限制，各种折腾下来，依然无效。而且nginx日志就算是debug也啥都不报。。。。。

我开始怀疑nginx了，换到tengine 1.5.1上面。问题依旧。。。。

最后我更新到了最新的nginx 1.5.6后，居然一切正常了。因为单位采用tengine后，觉得upstream_check_module很好用。于是又找了这个模块单独给nginx 1.5.6添加上。

```
./configure --prefix=/usr/local/nginx/ --add-module=/root/nginx_upstream_check_module/ --add-module=/root/headers-more-nginx-module-0.22/
```

我就把这个7000端口反向代理到／目录了。

```
location / {
            root   html;
            index  index.html index.htm;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header Host $http_host;
            proxy_set_header X-NginX-Proxy true;
            proxy_pass $scheme://127.0.0.1:7000;
            proxy_redirect off;
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "upgrade";

}
```

最后放张图展示下,我在浏览器上访问我的ssh样子。

![gateone图片](http://farm8.staticflickr.com/7289/10064296086_2f1a0e37eb.jpg)
