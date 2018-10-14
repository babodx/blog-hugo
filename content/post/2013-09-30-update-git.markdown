---
title: "update git"
date: 2013-09-30 10:01:00
categories: ['git'] 
---

#问题#

朋友架设了一个gitlab，很适合内部使用。但是当我从服务器上想把salt的配置push上去的时候，一直失败。测试了下，clone也是失败的。

提示如下

```
error: The requested URL returned error: 401 Unauthorized while accessing http://x.x.x.x

```

##解决##

经过各种尝试后发现，本地git的版本在1.8的没有问题。而我CentOS 6.4的下面是1.7.1，clone的时候就是不提示用户名和密码。直接报验证错误了。

###更新git步骤###

```
cd ~/Downloads
su
yum -y install zlib-devel openssl-devel cpio expat-devel gettext-devel curl-devel perl-ExtUtils-CBuilder perl-ExtUtils-MakeMaker
exit
wget -O v1.8.1.2.tar.gz https://github.com/git/git/archive/v1.8.1.2.tar.gz
tar -xzvf ./v1.8.1.2.tar.gz
cd git-1.8.1.2/
su
make prefix=/usr/local all
make prefix=/usr/local install
exit
```

更新后,验证下版本

```
[root@salt-master test]# git --version
git version 1.8.1.2
```

