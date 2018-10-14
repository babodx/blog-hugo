---
title: "linux-rescan-scsi"
date: 2014-06-05 02:49:00
categories: ["Linux"]
---
最近单位一台服务器在连接EMC DD 640的时候，没有识别出来对应的scsi设备。这里记录下解决办法，其实就是linux如何在不重启的情况下重新扫描scsi设备。
比如HBA卡插上光纤后，无法识别。或者插上新的scsi盘无法识别，都可以用这个方法解决。

1、先查看有哪些HBA卡的主机号

```
ls /sys/class/fc_host/
```

2、让系统重新扫描FC总线，大约15秒后可以生效

```
echo 1 >/sys/class/fc_host/host0/issue_lip
```

3、重新扫描host0下的scsi设备

```
echo “- – -” > /sys/class/scsi_host/host0/scan
```

*验证*

```
fdisk -l 
tail -f /var/log/message
```

```
# ls /sys/class/scsi_host
```



