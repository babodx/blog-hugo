---
categories: ['Linux']
title: '解决Linux下Tomcat不能重启和停止问题'
date: 2009-12-28 15:33:00
---
我的Tomcat 5.5.28安装在CentOS 5.3 64bit系统上

每次重启都停住，只要要等5-10分钟才可以。

系统reboot或者shutdown也都卡在关闭tomcat的过程中

后来发现直接用/usr/local/tomcat/bin/shutdown.sh一样存在问题，但是如果Tomcat什么项目都不加载，却正常。

我怀疑是tomcat加载的lib或者jar文件一多，重启和关闭就会慢的巨慢。。。甚至10分钟以上。

为了解决这个问题，我修改了/etc/init.d/目录下的tomcat文件，让关闭或者重启的时候，直接通过kill命令杀掉tomcat进程。

下面是我/etc/init.d/tomcat文件

``` 
#!/bin/bash
#
# Startup script for the tomcat
#
# chkconfig: 345 80 15
# description: Tomcat is a Servlet+JSP Engine.
 
# Source function library.
. /etc/rc.d/init.d/functions
 
 
export JAVA_HOME=/usr/local/jdk1.6.0_16
export CLASSPATH=.:$JAVA_HOME/jre/lib/rt.jar:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
export PATH=$PATH:$JAVA_HOME/bin
 
export CATALINA_BASE=/usr/local/tomcat
export CATALINA_HOME=/usr/local/tomcat
export CATALINA_TMPDIR=/usr/local/tomcat/temp
export JRE_HOME=/usr/local/jdk1.6.0_16
 
start(){
if [ -z $(/sbin/pidof java) ]; then
echo "Starting tomcat"
/usr/local/tomcat/bin/startup.sh
touch /var/lock/subsys/tomcat
else
echo "tomcat allready running"
fi
}
 
 
stop(){
if [ ! -z $(/sbin/pidof java) ]; then
echo "Shutting down tomcat"
#下面就是关闭的核心代码了，用ps获取tomcat进程id，直接kill掉

ps aux|grep tomcat|grep startup|awk '{print $2}'|xargs kill -9
#/usr/local/tomcat/bin/shutdown.sh
until [ -z $(/sbin/pidof java) ]; do :; done
rm -f /var/lock/subsys/tomcat
else
echo "tomcat not running"
fi
}
 
 
case "$1" in
start)
start
;;
stop)
stop
;;
restart)
stop
start
;;
status)
/usr/local/tomcat/bin/catalina.sh version
;;
*)
echo "Usage: $0 {start|stop|restart|status}"
esac
 
exit 0
 
```
