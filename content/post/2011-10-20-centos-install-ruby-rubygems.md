---
categories: ['Linux']
title: "centos 源码安装ruby和rubygems"
date: 2011-10-20 23:10:00
---
**获得ruby源代码**
```
wget http://ftp.ruby-lang.org/pub/ruby/1.9/ruby-1.9.2-p290.tar.gz
```

**解压并编译ruby**

```
tar zxvf ruby-1.9.2-p290.tar.gz  
cd ruby-1.9.2-p290
./configure --prefix=/usr/local/ruby192
make
make install
```

在/etc/profile最后添加

`export PATH=/usr/local/ruby192/bin:$PATH`

保存后，执行
`source /etc/profile`

查看下ruby版本
`ruby -v`

**下载rubygems1.8.10**
```
wget http://production.cf.rubygems.org/rubygems/rubygems-1.8.10.tgz
```

**解压并安装rubygems**

```
tar zxvf rubygems-1.8.10.tgz <br>
cd rubygems-1.8.10
ruby setup.rb
```

安装后用`gem -v` 可以查看下版本
