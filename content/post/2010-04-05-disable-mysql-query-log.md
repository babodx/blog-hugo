---
categories: ['Database']
title: "开启或者关闭MYSQL的query log(查询日志)"
date: 2010-04-05 19:45:00
---
最近在整理服务器的时候，发现mysql的查询日志文件占用了很多空间。这个查询日志文件部分内容如下，随着查询增加，会越来越大

```
    Tcp port: 3306 Unix socket: /tmp/mysql.sock
    Time Id Command Argument
    100404 8:36:49 1 Connect zzz@localhost on
    1 Init DB zzz
    1 Query SET NAMES 'utf8'
    1 Query SELECT * FROM `boblog_counter` LIMIT 0,1
    1 Query SELECT `blogid`,`pubtime`,`edittime`,`blogalias` FROM `boblog_blogs` WHERE `property`<2 ORDER BY `pubtime`DESC LIMIT 0, 1500
    1 Quit
    100404 8:38:01 2 Connect admin@localhost on
    2 Init DB zzz
    2 Query SET NAMES 'utf8'
    2 Query SET sql_mode=''
    2 Query SELECT * FROM `cmseasy_settings` WHERE `tag`='table-fieldset' ORDER BY 1 DESC limit 1
    2 Init DB zzz
    2 Query SET NAMES 'utf8'
    2 Query SET sql_mode=''
    2 Query SELECT * FROM `cmseasy_user` WHERE userid>0 ORDER BY 1 DESC limit 1
    2 Init DB zzz
    2 Query SET NAMES 'utf8'
    2 Query SET sql_mode=''
    2 Query SELECT count(typeid) as rec_sum FROM `cmseasy_type`
    2 Query SELECT * FROM `cmseasy_type` ORDER BY `order`,1 limit 1000
    2 Init DB zzz
    2 Query SET NAMES 'utf8'
    2 Query SET sql_mode=''
    2 Query SELECT count(1) as rec_sum FROM `cmseasy_friendlink` WHERE state>0 and linktype=1
    2 Query SELECT * FROM `cmseasy_friendlink` WHERE state>0 and linktype=1 ORDER BY listorder asc,id limit 20
    2 Init DB zzz
    2 Query SET NAMES 'utf8'
    2 Query SET sql_mode=''
    2 Query SELECT count(1) as rec_sum FROM `cmseasy_friendlink` WHERE state>0 and linktype=1
    2 Query SELECT * FROM `cmseasy_friendlink` WHERE state>0 and linktype=1 ORDER BY listorder asc,id limit 20
    2 Init DB zzz
    2 Query SET NAMES 'utf8'
    2 Query SET sql_mode=''
    2 Query SELECT * FROM `cmseasy_templatetag` WHERE name='å~E¬å~O¸ç®~@ä»~K' ORDER BY 1 DESC limit 1
    2 Init DB zzz
    2 Query SET NAMES 'utf8'
    2 Query SET sql_mode=''
    2 Query Describe cmseasy_archive
    2 Query SELECT count(1) as rec_sum FROM `cmseasy_archive` WHERE typeid in (2) and checked=1 and (state IS NULL or state<>'-1')
    2 Query SELECT * FROM `cmseasy_archive` WHERE typeid in (2) and checked=1 and (state IS NULL or state<>'-1') ORDER BY aid desc limit 4

```

其实记录的都是mysql执行的一些select语句，对于正常运行的服务器，我觉得基本没有必要保留这些日志。

关键是怎么关闭，这个日志的开启与否，可以通过mysql里提交如下命令查看

```
    show variables like 'log';
```

如果现实 log 的值是 ON就代码开启，如果是OFF就代表关闭

我们可以通过my.cnf配置文件进行配置

在my.cnf文件里，加入general-log = 0就关闭了这个查询日志。如果是general-log = 1就开启了这个查询日志。

