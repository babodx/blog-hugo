---
categories: ['Virtualization']
title: 'CentOS虚拟化服务的配置'
date: 2009-12-19 14:50:00
---
单位到了一台sun的x4450服务器，打算开学前部署好虚拟化服务，给每位带学生实训的老师一台虚拟的windows 2003 Server. 大约需要20台虚拟服务器

##服务器介绍##

![Sun Fire X4450 Server](http://farm9.staticflickr.com/8390/8509308054_d3488a134d_z.jpg)

* 硬件配置：
* CPU: 4颗 Intel(R) Xeon(R) CPU           X7460  @ 2.66GHz   (每颗6核心，供24核心)
* 内存: 64G
* 硬盘: 8块 Vendor: HITACHI   Model: H101414SCSUN146G  Rev: SA25

##虚拟方案##
用CentOS 5.3 64bit 作为DOM0，来管理整个虚拟化服务。然后采用hvm方式虚拟20台Windows server 2003。

##虚拟服务器配置：##

* CPU：1颗
* 内存：1G
* 硬盘：30G

整体结构如下

![虚拟化整体结构](http://farm9.staticflickr.com/8243/8508488101_df918b53e0.jpg)

在SunFire X4450的硬件上，安装Xen虚拟化服务。所有硬件由Xen内核管理，我们的CentOS 5.3 64bit操作系统跑在Xen内核上面，负责管理以后创建的DOM1到DOMN的多个虚拟服务器。

##虚拟化服务的安装##

这个就不具体写了，可以参考我前面的blog或者http://www.howtoforge.com/paravirtualization-with-xen-on-centos-5.3-x86_64-p2

如果不安装图形界面，在用virt-install安装windows操作系统的时候，需要VNC服务。这样可以远程通过VNC客户端连接来安装，因为ssh的方式没有图形界面。
如果安装了图形界面的CentOS，就很容易了。直接用图形界面的虚拟机管理程序安装即可。

##GUESTOS的配置##
由于每台虚拟服务器安装后，并没有开启远程服务，所以我们还是需要通过VNC连接登入虚拟服务器。在配置好IP和远程服务后，就可以使用远程桌面登陆了。
在每台虚拟服务器的配置文件里，加入如下语句
```
vnc = 2
vncunused = 2
vnclisten = "10.10.15.151"
vncpasswd = "password" 
```
**注意：** password为登陆vnc的密码，根据实际情况设置。vnclisten设置的是主机CentOS的IP 

**突破8台虚拟服务器的限制**
 
默认安装后，CentOS的max_loop最大值是8，这样我们默认只能启动8个虚拟服务器（不过半虚拟的linux好像不受这个影响）
我们需要修改这个参数
``` 
rmmod loop 
echo "options loop max_loop=64">/etc/modprobe.d/loop 
modprobe loop 
```
这样就可以了。

##配置网络##

我的服务器是有4块网卡 eth0、eth1、eth2、eth3

我打算eth0 采用10.10.15.151 ip地址，其他网卡不设置IP地址并且将/etc/sysconfig/network-scripts/下对应的脚本文件修改如下

`ONBOOT=no`
 
这样可以让系统启动后，就eth0可以用来访问。其他网卡都没有对应ip地址 

其他网卡的作用是留给虚拟服务器用的。我们通过修改xen的配置文件，让eth0到eth1绑定到xenbr0到xenbr3上面。然后20台服务器，10台通过xenbr1访问网络，10台通过xenbr2访问 
在rc.local文件里，添加 

`/etc/xen/scripts/new-bridge start`
 
new-bridge是我们自己写的脚本文件，为了创建xenbr0到xenbr3。xen系统默认就创建一个xenbr0

new-bridge文件内容如下

`vi /etc/xen/scripts/new-bridge`

```
#!/bin/sh
# Exit if anything goes wrong.
set -e 
# First arg is the operation.
OP=$1
shift 
script=/etc/xen/scripts/network-bridge.xen 
case ${OP} in
start)
          $script start vifnum=0 bridge=xenbr0 netdev=eth0
          $script start vifnum=1 bridge=xenbr1 netdev=eth1
          $script start vifnum=2 bridge=xenbr2 netdev=eth2
          $script start vifnum=3 bridge=xenbr3 netdev=eth3
#         $script start vifnum=2 bridge=xenbri netdev=dummy0
          ;; 
stop)
          $script stop vifnum=0 bridge=xenbr0 netdev=eth0
          $script stop vifnum=1 bridge=xenbr1 netdev=eth1
          $script stop vifnum=2 bridge=xenbr2 netdev=eth2
          $script stop vifnum=3 bridge=xenbr3 netdev=eth3
#         $script stop vifnum=2 bridge=xenbri netdev=dummy0
          ;; 
status)
          $script status vifnum=0 bridge=xenbr0 netdev=eth0
          $script status vifnum=1 bridge=xenbr1 netdev=eth1
          $script status vifnum=2 bridge=xenbr2 netdev=eth2
          $script status vifnum=3 bridge=xenbr3 netdev=eth3
#         $script status vifnum=2 bridge=xenbri netdev=dummy0
         ;; 
*)
          echo 'Unknown command: ' ${OP}
          echo 'Valid commands are: start, stop, status'
          exit 1
esac
```

安装xen-shell，让用户自己管理自己的虚拟服务器

具体安装步骤就不写了，参考我前面的文章吧

##安装和使用xen-shell##

或者

http://www.xen-tools.org/software/xen-shell/install.html

**注意**
安装这个，需要screen这个软件，可以通过yum install screen安装

再有就是需要/etc/xen/下面的虚拟机配置文件有对应的权限

安装后，用root用户，直接输入xen-shell看看能不能进入，如果报错，根据提示修改。

全部安装完成后，让我们看看20台虚拟服务器跑起来的效果

![xen效果图](http://farm9.staticflickr.com/8531/8509667010_2b76c157b2_z.jpg)
![xen效果图](http://farm9.staticflickr.com/8088/8508563965_7d284d04c4.jpg)
再来看看网络的使用情况

![网络使用情况](http://farm9.staticflickr.com/8111/8509671352_bd087337dd.jpg)

通过上面的brctl show可以清楚的看到，虚拟服务器的网卡，都放到了xenbr1和xenbr2下面了。

再来看看vnc登陆的界面，注意VNC登陆后，不能直接用键盘的Ctrl+Alt+Del登陆，我们可以通过右键点窗口来选择Send Ctrl+Alt+Del来实现发送这三个按键消息。

![windows图片](http://farm9.staticflickr.com/8513/8508569279_fab2c10e80_z.jpg)

配置好虚拟服务器的IP和远程桌面后，我们就可以采用远程桌面连接了。

用户自己管理自己的服务器

其实有了远程桌面，用户就可以通过这个登陆服务器，重启和修改配置了。但是如果关机以后，就没有办法开机了。

我们安装xen-shell就能实现用户自己通过用户名和密码登陆命令行格式的界面来启动服务器。

首先我们要给我们每台虚拟服务器建立一个CentOS系统上的用户，让后按照Xen-shell的要求配置，这个不具体介绍了，参考

安装和使用xen-shell

或者

http://www.xen-tools.org/software/xen-shell/install.html

后用户就可以通过putty登陆到自己的虚拟服务器管理命令行了。

xen-shell可以让用户自己启动、重启、关闭虚拟服务器，还可以查看虚拟服务器的状态、运行时间等。
如果在用户对应目录下编写好image.sh文件，还可以让用户自己rebuild操作系统。
有了这个，基本管理员告诉用户他的xen-shell用户，虚拟服务器的ip就ok了。其他的用户自己都可以管理了。

##其他问题##

如何让guestos随主系统自动启动，这个可以参考xen文档。

如何克隆guestos系统，这个可以参考virt-clone

如何安装多个linxu guest系统，这个可以参考获取XEN guestOS的images文件

整个安装断断续续弄了大约2周，目前总算ok了。
