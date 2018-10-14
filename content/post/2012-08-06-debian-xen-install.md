---
categories: ['Virtualization']
title: 'debian6.0.5(squeeze)安装配置xen-4.0虚拟化'
date: 2012-08-06 17:50:00
---

#系统安装#

我是通过http://mirrors.163.com下载的     debian-6.0.5-amd64-CD-1.iso
最小安装有一张CD就可以了，安装的是debian6.0.5(squeeze) 64位版本

安装没啥好说的，最小化安装，除了基本系统就多选择了一个ssh server。

安装好系统后，进入系统首先设置网卡，不要用dhcp选择static模式
编辑/etc/network/interfaces文件如下

```
# The loopback network interfaceauto lo
 
iface lo inet loopback
 
# The primary network interface
 
#allow-hotplug eth0
 
auto eth0
 
iface eth0 inet static
 
  address 192.168.1.10
 
  netmask 255.255.255.0
 
  broadcast 192.168.1.255
 
  network 192.168.1.0
 
  gateway 192.168.1.1
```

我这里只有一个eth0网卡，采用192.168.1.10的IP地址

接下来修改apt的源，采用mirrors.163.com提供的服务器，这样速度比较快
修改/etc/apt/sources.list
内容如下

```
deb http://mirrors.163.com/debian/ squeeze main non-free contrib
deb http://mirrors.163.com/debian/ squeeze-proposed-updates main non-free contrib
deb-src http://mirrors.163.com/debian/ squeeze main non-free contrib 
deb-src http://mirrors.163.com/debian/ squeeze-proposed-updates main non-free contrib
```

执行apt-get update更新源

经过这些设置，系统基本就是一个干净的最小系统。并且采用163的服务器作为软件源。

##安装XEN-4.0##

下面开始安装xen-4.0的相关软件包和配置

```
apt-get install xen-hypervisor-4.0-amd64 xen-linux-system-2.6.32-5-xen-amd64 xen-utils* xenwatch xen-tools
```

需要下66.2M的软件包

安装后，再次启动选择带XEN 4.0 amd64的内核启动项

注意：（默认的启动项虽然带xen但是不带4.0选项，启动后不能正确启动xend）
也可以修改/etc/default/grub里面的选项，默认从这个内核启动

修改GRBU_DEFAULT=4

###配置xen的网络###
修改/etc/xen/xend-config.sxp文件

打开`(network-script network-bridge)`

采用桥接方式 

重启后，系统会将物理网卡修改为peth0，而eth0为桥接的网卡

brctl show 显示如下

```
root@node1:~# brctl show
bridge name     bridge id                    STP enabled     interfaces
eth0                 8000.001c42d8fe43     no                    peth0
```

###通过xen-create-image安装debian虚拟系统###

```
xen-create-image --hostname=vm01 --size=2G --swap=128M --ide \ 

--ip=192.168.1.21 --netmask=255.255.255.0 --gateway=192.168.1.1 \

--force --dir=/vm --memory=128M -arch=i386 \

--kernel=/boot/vmlinuz-2.6.32-5-xen-amd64 \

--dist=squeeze --mirror=http://mirrors.163.com/debian/ --passwd \

--install-method=debootstrap
```

安装以后，需要调整vm01.cfg文件，否则无法启动
首先调整磁盘的相关配置

```
root        = '/dev/xvda2 ro'
disk        = [
                  'file:/vm/domains/vm01/disk.img,xvda2,w',
                  'file:/vm/domains/vm01/swap.img,xvda1,w',
              ]
```

将原来的hda2修改为xvda2 

然后是调整网卡的相关配置

```
vif         = [ 'ip=192.168.1.21,mac=00:16:3E:9B:1A:90,bridge=eth0' ]
```

主要是加上bridge=eth0

这样就可以通过xm create vm01.cfg启动虚拟系统了

```
root@node1:~# xm list
Name                                        ID   Mem VCPUs      State   Time(s)
Domain-0                                   0   879     1               r-----     26.6
vm01                                         1   128     1                -b----      1.7

root@node1:~#  
```

启动后，dom0下面会出现一个vif的虚拟网卡

```
root@node1:~# brctl show
bridge name     bridge id                      STP enabled     interfaces
eth0                 8000.001c42d8fe43         no                      peth0
```
                                                                                                vif1.0 
如果在guest系统只能ping到dom0的eth0，而不能ping到外网
可以检查下

```
root@node1:~# sysctl net.ipv4.ip_forward

net.ipv4.ip_forward = 0
```

只要打开这个ip_forward就可以了

修改/etc/sysctl.conf里面的net.ipv4.ip_forward=1
或者
`echo 1>/proc/sys/net/ipv4/ip_forward`

也可以用命令临时生效
`sysctl -w net.ipv4.ip_forward=1 `


