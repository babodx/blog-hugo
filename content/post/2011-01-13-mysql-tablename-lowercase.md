---
categories: ['Database']
title: 'Mysql设置不区分表名大小写'
date: 2011-01-13 16:02:00
---
今天遇到个问题，有个程序从Windows导入到Linux的时候，表格全部小写了。但程序里很多DAO全是大写调用。

Linux默认安装的Mysql是表名区分大小写的。

这个设置主要是在/etc/my.cnf里面的[mysqld]段，加入如下设置项

```
lower_case_table_names = 1
```
 
如果设置为0，就是区分大小写。设置为1就是不区分了。

