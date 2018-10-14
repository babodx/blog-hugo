---
categories: ['Database']
title: 'mysqldump备份blob类型内容'
date: 2011-03-30 15:09:00
---
用mysqldump备份出数据库内容到SQL文件。当我们数据库表有blob类型字段的时候，这个导出的SQL再导入的时候就会因为blob字段内容乱码等原因，不能顺利导入了。

**解决办法：**

在用mysqldump备份的时候，采用--hex-blob参数，这样备份出来的sql文件，就可以顺利导入了。
