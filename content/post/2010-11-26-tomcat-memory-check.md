---
categories: ['Linux']
title: '监控tomcat内存占用'
date: 2010-11-26 19:47:00
---
最近单位给客户部署的一台服务器，主要跑的JAVA应用，一个物业OA系统。经常因为Tomcat内存占用过高而停止响应。

观察了一下，每次停止响应时候，Tomcat进程占用内存超过1.2G

初步怀疑是程序不够完善有内存泄露的地方，但是一时半会也找不到具体代码哪里有问题。

我就写了一个shell不断监控Tomcat进程内存占用，当发现内存占用大于800M，我就自动重启一次Tomcat。

```
    #!/bin/sh   
    MEM=`ps aux|grep tomcat|grep startup|awk '{print $6}'`   
    #echo $MEM  
    if [ $MEM -gt 800000 ]; then   
       echo "tomcat mem too large,restart tomcat!"  
       service tomcat restart   
    fi   
```

注意：如果service tomcat restart不能正常重启Tomcat，可以参考我前面写的[CentOS下的Tomcat停止脚本]

