---
categories: ['Linux']
title: "试用ubuntu 8.04和LVM动态增加分区"
---

我在虚拟机VMware里面安装了ubuntu 8.04.

安装的时候，只是选择了openssh这个服务。安装后感觉系统很干净

**磁盘占用**
```
/dev/mapper/ubuntu-root                      5.6G 321M 5.3G   6% /
```

系统就用了不到400M

**内存**

```
                   total       used       free     shared    buffers     cached
Mem:           249         39        209          0          3         16
```
减掉buffers和cached的内容，还有我ssh远程连接的占用。实际系统启动后内存也就需要17M

**LVM**

安装系统的时候，我就选择了整个磁盘启用LVM，采用ubuntu默认分区的方式。

ubuntu默认分了2个区/sda1和/sda5。sda1用来做/boot，sda5用来做LVM。文件系统都是默认的ext3。

然后在LVM里面默认建立了2个LV( Logical volume)，一个/dev/ubuntu/root用来做/ ，一个/dev/ubuntu/swap_1用来做swap交换分区。我又手动修改了文件系统，采用reiserfs。

**动态添加/分区大小**

我先添加了一个硬盘，系统识别为sdb

对这个盘**创建分区**（LVM可以不用分区的，但是需要动态添加到/分区，所以需要有文件系统，不能添加后格式化/分区吧）

```
sudo fdisk /dev/sdb
n -->创建分区
t -->修改分区类型为8e
w -->保存结果
```

现在系统可以识别/dev/sdb1这个设备了。

**创建PV(Physical volume)**

```
sudo pvcreate /dev/sdb1
```

**创建VG(Volume group)**

```
sudo vgextend ubuntu /dev/sdb1
```

**扩展LV(Logical volume)**

```
sudo lvextend -L +2G /dev/ubuntu/root<br>
```

这样就给/dev/ubuntu/root这个设备大小增加了2G。可以通过sudo lvdisply查看
但是这个时候df -h还是没有变化的，需要调整文件系统。

```
sudo resize_reiserfs -s +2G /dev/ubuntu/root<br>
```

这样就完成了全部动态扩展分区。而且是服务器一直在线状态，不用关机。

调整Swap

还是和上面一样的情景，只要VG里面还有剩余空间就可以用来扩展swap用。

```
sudo swapoff -a
```

关闭正在用的swap分区

然后

```
sudo lvextend -L +200M /dev/ubuntu/swap_1
```

给/dev/ubuntu/swap_1扩展了200M容量

```
sudo mkswap /dev/ubuntu/swap_1
```

建立swap的文件系统

最后

```
sudo swapon -a
```

启用新的swap系统。

