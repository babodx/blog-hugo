---
categories: ['Linux']
title: "centos 单独安装apachebench"
date: 2012-01-19 08:50:00
---
这两天在测试php性能优化方法。

为了做压力测试可观察效果，就选择了ApacheBench来作为压力测试工具。其实就是大家常说的ab。

但是这个工具是安装apache web server的时候自带的，现在我服务器上都是跑nginx。也不想为了用这个工具就再装个apache。所以在用下面方法单独安装ab工具，这里记录下步骤。
 
首先安装ab运行需要的软件包apr-util

```
yum install apr-util
```

然后安装一个yum的工具包，为了可以单独弄出来ab。

```
yum install yum-utils
```

上面两个安装好以后，我们开始单独安装ab，其实就是下载到apache的rpm包，然后解压后，cp出来ab工具。

下载apache的rpm包就交给yumdownloader完成了，下载前我们建立一个临时目录用来存放。

```
mkdir httpd

cd httpd

yumdownloader httpd
```

下载后，我们对rpm进行解包

```
rpm2cpio httpd-2.2.3-43.el5.centos.3.i386.rpm | cpio -idmv
```

最后将我们用到的ab拷贝到系统的/usr/bin目录下就可以了。最后再删除临时目录就全部ok了。

```
mv usr/bin/ab /usr/bin/ab
cd ..
rm -rf httpd
```

