---
categories: ['Linux']
title: "Centos Linux 远程终端ssh乱码问题"
date: 2011-11-10 15:23:00
---

我们经常碰到linux乱码问题。尤其是碰到网页上传个中文文件名的文件，ssh登陆到linux一看全乱码想删除都不行。很郁闷的。如下图所示中文文件名全都是显示问号了（这个乱码由于你的编码设置不同，显示的也不太一样）

![乱码截图](http://farm9.staticflickr.com/8094/8513901709_166ee71c2c.jpg)

还有一个就是vim的乱码

![vim乱码](http://farm9.staticflickr.com/8235/8513903997_2edf6ef68a_m.jpg)

**解决办法：**

首先需要给linux安装中文支持。这里以centos为例，所以采用yum安装

`yum groupinstall chinese-support`

然后再设置linux系统的i18n文件，位置在/etc/sysconfig/i18n

```
LANG="zh_CN.GB18030"

SUPPORTED="zh_CN.UTF-8:zh_CN.GBK:zh_CN.GB18030:zh_CN:zh:en_US.UTF-8:en_US:en"

SYSFONT="latarcyrheb-sun16"
```

接着设置LC_ALL环境变量，在/etc/profile文件里加入

`export LC_ALL=zh_CN.GB18030`

全部设置好后，重启系统。再次登陆后，用如下命令查看

```
-bash-3.2# locale

LANG=zh_CN.GB18030

LC_CTYPE="zh_CN.GB18030"

LC_NUMERIC="zh_CN.GB18030"

LC_TIME="zh_CN.GB18030"

LC_COLLATE="zh_CN.GB18030"

LC_MONETARY="zh_CN.GB18030"

LC_MESSAGES="zh_CN.GB18030"

LC_PAPER="zh_CN.GB18030"

LC_NAME="zh_CN.GB18030"

LC_ADDRESS="zh_CN.GB18030"

LC_TELEPHONE="zh_CN.GB18030"

LC_MEASUREMENT="zh_CN.GB18030"

LC_IDENTIFICATION="zh_CN.GB18030"

LC_ALL=zh_CN.GB18030
```

完成以上操作，应该就可以正常显示中文文件名字了。不过这个只是linux没有问题了，我们的ssh客户端还需要支持才可以。我一般使用Putty来当做ssh客户端，下面就以putty为例子进行设置。
先设置使用的字体，选择windows->Appearance，弹出的字体界面里选择“新宋体”，字符集选择“CHINESE_GB2312”

![putty设置](http://farm9.staticflickr.com/8369/8513908051_06d0c35864_z.jpg)

再设置编码

![设置编码](http://farm9.staticflickr.com/8225/8513910593_16685f713a.jpg)

以上设置完成后，就可以正常显示了。效果如下图所示：

![正常显示的效果](http://farm9.staticflickr.com/8379/8515025280_c0816e5461.jpg)

在命令行输入中文文件名也可以正常使用了。

![输入中文效果](http://farm9.staticflickr.com/8097/8515027934_4514657da8_m.jpg)

到目前为止，ssh下操作中文文件名的问题就彻底解决了。可以正常输入中文文件名来操作了。

下面是vim显示乱码问题，vim一般都是因为文件编码和显示编码的问题引起的乱码。

我们打开文件发现乱码后，采用如下命令

`set encoding=utf-8 termencoding=gbk`

这样以后，vim也可以正常操作中文了。效果图

![vim中文操作效果](http://farm9.staticflickr.com/8248/8515031904_d608be830d_z.jpg)


**注意:**

网上看到一些资料说是将i18n的LANG设置为zh_CN.UTF-8，然后LC_ALL也是设置为zh_CN.UTF-8

但是我试验了下，效果并不好（也将putty调整为了utf-8）.但是文件名显示还是乱码。需要用ls --show-control-chars来正常显示，输入中文文件名也不正常。

以上只是在putty下使用，也许配合其他ssh客户端效果不太一样。稍后我再添加一些其他客户端效果。

