---
title: "CentOS 5.10安装报磁盘空间错误"
date: 2014-06-14 05:01:00
categories: ["Linux"]
---
今天单位的一台HP DL380 G7安装CentOS 5.10的时候出现错误。
因为服务器配了7块1T的磁盘，做了个Raid5以后，大约3.8T。所有采用了GPT分区，安装的时候直接报错了，提示GTP分区问题。
参考了如下网站
http://blog.endpoint.com/2013/11/installing-centos-5-on-3tb-drive.html
http://oliverpelz.blogspot.it/2010/09/how-to-install-centos-55-on-any-gpt.html
http://richardjh.org/blog/install-centos-large-partitions-using-gpt-disk-layout/

操作步骤都比较麻烦。因为必须安装CentOS5 不然CentOS 6可以很顺利的。。。

既然GTP分区不行，就用smart盘引导后重新创建Raid。将一个300G空间创建为逻辑卷，剩下的3T空间另外创建一个。
然后将操作系统安装在300G空间内，系统安装成功后再通过parted对3T磁盘分区。

结果这次安装过成功又报了如下错误：An error occurred transferring the install image to your hard drive. You are probably out of disk space.

果断采用ctrl+alt+F3切到命令界面，发现有如下提示：CRITICAL: error transferring stage2.img: [Errno 5] Input/output err

发现这个错误后，已经可以知道不是因为GTP分区引起的了。而是在stage2.img这个文件传输过程中出现错误。
更换安装介质后，问题得到了解决。
