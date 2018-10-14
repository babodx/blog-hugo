---
categories: ['Database']
title: 'mysqldump解决中文乱码问题'
date: 2011-03-14 14:32:00
---

我的数据库采用编码如下：

```
mysql> show variables like 'coll%';
+----------------------+-------------------+
| Variable_name        | Value             |
+----------------------+-------------------+
| collation_connection | latin1_swedish_ci |
| collation_database   | latin1_swedish_ci |
| collation_server     | latin1_swedish_ci |
+----------------------+-------------------+
3 rows in set (0.00 sec)
 
mysql> show variables like 'character_set%';
+--------------------------+----------------------------+
| Variable_name            | Value                      |
+--------------------------+----------------------------+
| character_set_client     | latin1                     |
| character_set_connection | latin1                     |
| character_set_database   | latin1                     |
| character_set_filesystem | binary                     |
| character_set_results    | latin1                     |
| character_set_server     | latin1                     |
| character_set_system     | utf8                       |
| character_sets_dir       | /usr/share/mysql/charsets/ |
+--------------------------+----------------------------+
8 rows in set (0.00 sec)
```
 
数据库的编码采用utf8

开始用mysqldump出来的都是乱码。后来采用 --default-character-set=gbk参数，然后再用iconv转码为utf8就解决了问题。

下面是我的数据库备份脚本

```
    #!/bin/sh   
    #####configurate#######   
    rq=`date +%Y%m%d%N`   
    user="user"  
    password="password"  
    rm -rf /var/lib/mysql/mysql-bin.*   
    mysqldump -u$user -p$password --default-character-set=gbk --single-transaction --flush-logs --master-data=2 mydatabase>/home/ict/mysql_backup/database$rq.sql   

```
导出后转码

`iconv -t utf-8 -f gbk -c database.sql>databaseutf8.sql`

这样已经可以看到正确的中文了。

**导入**

在windows下，如果采用HeidiSQL打开SQL文件，可以正确看到中文，但是导入后还是乱码。

解决办法，采用记事本打开，选择另存为，编码选择ANSI。然后再用HeidiSQL工具打开导入即可。

