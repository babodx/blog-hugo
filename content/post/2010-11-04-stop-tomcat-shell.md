---
categories: ['Linux']
title: 'CentOS下的Tomcat停止脚本'
date: 2010-11-04 19:23:00
---
我在CentOS 5.3 64bit上安装的Tomcat有个小问题，就是不能通过service tomcat stop停止或者server tomcat restart重启。

我看了下/etc/init.d/tomcat的文件，和以前没有什么不一样，但就是不能正常停止。

后来我自己修改了/etc/init.d/tomcat文件，改为直接kill进程。目前已经可以正常通过service关闭或者重启tomcat了。

下面是我/etc/init.d/tomcat文件内容，贴出来留着以后查看。呵呵

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
