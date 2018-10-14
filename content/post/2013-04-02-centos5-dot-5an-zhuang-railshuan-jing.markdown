---
title: "centos5.5安装rails环境"
date: 2013-04-02 13:10:00
categories: ['Rails']
---

最近招生需要写一个考试结果查询程序，就用rails来写了。以前一直java、php的，发现rails的高效开发以后，就打算尝试下。
程序写好后，就开始考虑服务器的问题了。我手边就只有centos的系统，也直到ubuntu和debian对于rails来说有好点。但是现实总是残酷的。。。

##安装EPEL
因为系统为centos 5.5 所以很多现在流行的软件包都没有。所以采用epel来安装。

```
wget http://dl.fedoraproject.org/pub/epel/5/i386/epel-release-5-4.noarch.rpm
rpm -ivh epel-release-5-4.noarch.rpm
```

有了epel以后，就可以安装一些需要的软件包了。

##安装git
git直接用`yum install git` 就可以顺利安装了

##安装rvm

```
echo insecure >> ~/.curlrc
curl -L get.rvm.io | bash -s stable
source ~/.bashrc
source ~/.bash_profile
rvm list
```

##安装ruby

```
rvm install 1.9.3
```
安装的时候可能会因为系统缺少一些软件包而提示错误。根据提示安装对应软件包就可以了。
我这里是提示缺少libyaml-devel和libffi-devel俩个

```
yum install libyaml-devel
yum install libffi-devel
```
安装完成后，设置下默认的ruby版本

```
rvm use 1.9.3 --default
```

有了这些，就可以直接用gem来安装rails了

##安装Rails

```
gem install rails
```

##小结
上面安装基本很顺利，就是用rvm安装ruby的时候可能会提示确认支持的软件包。根据提示安装上就可以了。
