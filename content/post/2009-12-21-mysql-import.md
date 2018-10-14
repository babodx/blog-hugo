---
categories: ['Database']
title: '如何向万网的Mysql数据库导入数据'
date: 2009-12-21 18:00:00
---
昨天单位由一个网站，要放到万网的虚拟主机去。
网站是jsp写的，程序好导。直接ftp过去就可以了。

可是mysql的数据库怎么导呢？

我先用

```
mysqldump database_name>database_name.sql
```

然后把这个database_name.sql下载下来。

再给虚拟主机空间上传一个phpmyadmin来做数据库管理用。这样就可以使用phpmyadmin导入数据了。

如果网站数据小，这样就ok了。可是我的database_name.sql有60M

万网空间上的phpmyadmin只能最大支持2M

于是只能另外想办法。我先导入表格结构吧。

```
mysqldump –d database_name>database_name.sql
```

在phpmyadmin里，选择好数据库后，用导入功能，将database_name.sql导入

![phpmyadmin图片](http://farm9.staticflickr.com/8096/8510015688_bc8709cc96.jpg)

现在有了表结构了，但是每个表都没有数据。我们再继续导入数据。

我的表里面，除了一个news表格特别大，大约51M，其他都在2M一下。所以我每个表格生成一个txt文件，然后导入到万网的数据库。

本地进入mysql>提示符后，用以下命令将数据的表格数据导出。

```
select * from admin into outfile “/tmp/admin.txt” fields terminated by “|” lines terminated by “@”
```

上面命令将本地数据库的admin表，导出到/tmp/目录下的admin.txt文件。

我们有几个表格，就用这个命令导出几个txt文件。

然后将txt文件用gzip压缩，因为phpmyadmin支持gzip压缩格式的。

```
cd /tmp/
gzip admin.txt
```

最后将压缩的admin.txt.gz下载到本地，用浏览器上传给phpmyadmin导入

![](

**解决news表格过大问题**


我的news表格有51Ｍ，就算gzip压缩后，还有7M左右，还是不能用phpmyadmin导入。我们可以将news表格的数据切分为多个txt文件，然后保证每个文件gzip后，在2M以内就可以。这个切分就要根据具体表格来判断了。

我先查询了news表格一共有多少条记录

```
select count(*) from news;
```

返回1477条，表格是51M。压缩后的表格是7M。

7M/2M=3.5 

1477/3.5=422条。

根据上面的计算，只要按照422条一个文件来分割就可以了。为了保险，因为每条记录的大小并不是一样的平均分配，我按照每个文件200条记录来切分。

```
select * from news where newId>0 limit 200 into outfile “/tmp/news0.txt” fields terminated by “|” lines terminated by “@”;

然后用

```
select max(newId) from news where newId>0 limit 200;
```

查看下200条记录后，是到了那条记录，下次分割就从那条开始
这样分割出来的8个文件，一次压缩后，上传到phpmyadmin导入。就完成了全部表格的导入工作。

**总结**

其实如果数据本身不复杂，直接用navicat也可以远程连接导入的。但是navicat不支持定义 lines terminated。我的news表格里面存在的都是html代码，经常出现换行符，所以只能选择用phpmyadmin了。
