---
categories: ['Database']
title: 'Mysql设置my.cnf来记录下全部sql请求'
date: 2009-12-21 18:00:00
---

要想记录下提交到mysql数据库的全部sql语句，可以通过修改my.cnf来实现

修改/etc/my.cnf文件

找到[mysqld]段
加入

```
log=queryLog
log-slow-queries=showquerylog
```

然后就可以到/var/lib/mysql/目录下找到queryLog和showquerylog日志文件了

里面记录这每个提交到数据库执行的sql语句。

我的系统CentOS 4.4 rpm方式安装的mysql 5.0.57,如果其它系统或者其它安装方式，可能文件路径会有些不同。

