---
title: "备份到阿里云的oss"
date: 2014-02-09 16:37:00
categories: ['Linux']
---

#概述#

备份一直是个麻烦的问题。备份到本地担心系统坏了导致数据丢失，备份到远程机器成本又有点高。
我的方法是通过ruby下的一个backup gem，来实现定期将mysql数据库和web程序备份到本地的同时，上传到阿里云的oss上。

##安装##

###ruby安装###

通过rvm来安装ruby，首先安装rvm。参考http://rvm.io 网站

```
\curl -sSL https://get.rvm.io | bash -s stable
```

rvm安装后，用如下命令安装ruby

```
rvm install ruby

```

###安装backup gem###

```
gem install backup
```

###安装backup-aliyun插件###

这个插件主要是给backup gem提供可以备份到阿里云oss的功能。
默认的插件依赖carrierwave-aliyun gem，可以上传到杭州站点下的bucket。不支持上传到青岛站点。

我更新了部分代码，加入了aliyun_area选项。可以支持青岛站点。
安装默认版本

```
gem install backup-aliyun
```

安装我修改后的版本

```
git clone https://github.com/babodx/backup-aliyun.git

cd backup-aliyun

gem build backup-aliyun.gemspec

gem install backup-aliyun-0.1.1.gem

```

##配置##

通过backup对数据库和程序文件备份，需要生成配置文件和配置model

```
backup generate:config
```

```
backup generate:model --trigger my_backup \
  --archives --storages='local' --compressor='gzip'
```

编辑Backup/models/my_backup.rb文件，来定义需要备份的内容。具体可以参考http://meskyanichi.github.io/backup/v4/getting-started/ 文档

我的文件内容如下

```
# encoding: utf-8

#引入aliyun备份插件
require 'backup-aliyun'

Backup::Model.new(:my_backup, 'Description for my_backup') do

  #定义需要备份的目录
  archive :my_archive do |archive|
    # Run the `tar` command using `sudo`
    # archive.use_sudo
    archive.add "/data/htdocs/my_backup"
  end

  #定义需要备份的数据库
  database MySQL do |db|
    db.name               = "db_name"
    db.username           = "db_user"
    db.password           = "db_passwd"
    db.host               = "127.0.0.1"
    db.port               = 3306
  end

  #定义备份到本地路径
  store_with Local do |local|
    local.path = '~/backups/'
    local.keep = 5
  end

  #定义备份到aliyun oss
  store_with "Aliyun" do |aliyun|
    aliyun.access_key_id = 'access_key_id'
    aliyun.access_key_secret = 'access_key_secret'
    aliyun.bucket = 'bucket_name'
    aliyun.path = 'bucket_path'
    aliyun.keep = 10
    aliyun.aliyun_internal = 'true'
    aliyun.aliyun_area = 'cn-qingdao'
  end

  #定义开启压缩
  compress_with Gzip

end
```

##执行备份##

```
backup perform -t my_backup

```


