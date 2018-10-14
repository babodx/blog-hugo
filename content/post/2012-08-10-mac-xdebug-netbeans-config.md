---
categories: ['Mac']
title: mac下配置php调试环境
date: 2012-08-10 16:00:00
---
已经用了mac有2个月了，基本网络管理和开发工作都在mac下面了。

下面说下如何配置mac下面的php调试环境

**我的环境：**

* macbook air 2012款   
* Mac OS X 10.8 系统
* php环境：XAMPP 1.7.3
* 开发工具：一般用sublime text 2，调试或者项目开发用netbeans


下面说说如何安装调试环境，主要就是xdebug的安装和配置

这里需要提下mac下面的安装包管理工具homebrew

我就是用brew来安装xdebug的。homebrew是一个用ruby开发的，类似macport的工具。可以在线安装软件，就像linux下的apt-get或者yum一样。

**安装homebrew**

需要系统可以支持ruby，通过ruby -v命令可以查看ruby版本。mac os 自带ruby

直接在终端里输入

```
ruby <(curl -fsSk https://raw.github.com/mxcl/homebrew/go)
```

上面命令执行完，brew命令就可以执行了。

先运行brew doctor测试下环境是否完整，如果需要，还要添加Command Line Tools。

添加Command Line Tools

打开Xcode，在preferences->downloads里面下载并安装Command Line Tools

![xcode](http://farm9.staticflickr.com/8088/8515461958_4e96f751a7_z.jpg)

一切正确的话，执行brew doctor显示如下

```
babomatoMacBook-Air:~ babo$ brew doctor

Your system is raring to brew.
```

安装好brew后，先执行brew update更新下软件包的源，然后再加载两个formula的资源

```
brew update

brew tap josegonzalez/homebrew-php

brew tap homebrew/dupes
```

完成后，就可以通过brew search和brew install来查找并安装软件了

```
brew install php53-xdebug
```

安装好xdebug后，还需要配置php.ini才可以正常使用。

先用brew info查看xdebug相关信息

```
babomatoMacBook-Air:~ babo$ brew info php53-xdebug
php53-xdebug: stable 2.2.1, HEAD
http://xdebug.org
Depends on: autoconf
/usr/local/Cellar/php53-xdebug/2.2.1 (4 files, 360K) *
https://github.com/josegonzalez/homebrew-php/commits/master/Formula/php53-xdebug.rb
==> Options
--without-config-file
    Do not add ext-xdebug.ini to /usr/local/etc/php/5.3/conf.d
==> Caveats
To finish installing xdebug for PHP 5.3:
  * /usr/local/etc/php/5.3/conf.d/ext-xdebug.ini was created,
    do not forget to remove it upon extension removal.
  * Restart your webserver.
  * Write a PHP page that calls "phpinfo();"
  * Load it in a browser and look for the info on the xdebug module.
  * If you see it, you have been successful!
```

通过信息我们知道xdebug版本是 stable 2.2.1

配置文件在/usr/local/etc/php/5.3/conf.d/ext-xdebug.ini

我们xampp的配置文件在/Applications/XAMPP/xamppfiles/etc/php.ini

将xdebug的配置加入到/Applications/XAMPP/xamppfiles/etc/php.ini

在文件最下面加入如下信息

```
[xdebug]
zend_extension="/usr/local/Cellar/php53-xdebug/2.2.1/xdebug.so"
        xdebug.remote_enable=1
        xdebug.remote_host=localhost
        xdebug.remote_port=9000
        xdebug.remote_handler=dbgp
```

重启xampp的apache，然后访问phpinfo查看是否成功加载xdebug

![xdebug](http://farm9.staticflickr.com/8381/8515464954_5cd7f4b8be.jpg)

**配置netbeans可以调用xdebug**

首先将netbeans的偏好设置里的php5解释器设置为xampp的php。不要用系统自带的

![xampp_php](http://farm9.staticflickr.com/8518/8515466796_97a1191c23.jpg)

再设置调试的端口

![netbeans设置xdebug端口](http://farm9.staticflickr.com/8239/8514355437_689a0d90c0.jpg)

这些都设置好，就可以调试php代码了

最后放一张我调试的截图

![php调试截图](http://farm9.staticflickr.com/8246/8514357603_58e49f882b_z.jpg)

