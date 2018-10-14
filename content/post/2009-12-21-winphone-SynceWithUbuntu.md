---
categories: ['Linux']
title: "ubuntu 8.10下同步wm6.1手机"
---

参考了

* [http://www.synce.org/moin/SynceWithUbuntu](http://www.synce.org/moin/SynceWithUbuntu)
* http://hi.baidu.com/john_zhy/blog/item/50e39f317240141febc4af5a.html

我的本本 IBM R61

系统 ubuntu 8.10从 8.04升级上来的。

手机 eten X800 wm6.1英文系统

原来一直不能在ubuntu下面同步手机资料，昨天参考了上面两个帖子，最后成功了。

1、首先添加源

```
sudo gedit   /etc/apt/sources.list
```

在末尾添加

```
deb <a href="http://ppa.launchpad.net/synce/ubuntu" target="_blank">http://ppa.launchpad.net/synce/ubuntu</a> hardy main

deb-src <a href="http://ppa.launchpad.net/synce/ubuntu" target="_blank">http://ppa.launchpad.net/synce/ubuntu</a> hardy main
```

保存，关闭

```
sudo apt-get update
```

我没有更新源，但是也成功。可能是8.10的缘故

2、更新usb驱动（仅内核在2.6.24-19之前的，如果比这个新请跳过本步骤）

```
sudo rmmod rndis_host cdc_ether usbnet
sudo rm /lib/modules/`uname -r`/kernel/drivers/net/usb/{rndis_host,cdc_ether,usbnet}.ko
sudo apt-get install usb-rndis-source cdbs
sudo module-assistant auto-install usb-rndis
sudo apt-get install synce-hal librra0-tools librapi2-tools
```

因为我的内核是2.6.27-7的，所以跳过了这步。但是我安装了后面的软件包

```
sudo apt-get install synce-hal librra0-tools librapi2-tools
```

3、安装**SynCE-GNOME**

```
sudo apt-get install python-setuptools
```

 下载[synce-gnome-0.11.tar.gz](http://downloads.sourceforge.net/synce/synce-gnome-0.11.tar.gz)

 解压，在终端进入其目录，运行

```
python setup.py build
sudo python setup.py install
```

安装**notification-daemon**（如果没有安装过的话，用新得立软件包管理器搜索一下就可以了）

这个步骤完全做了一次，notification-daemon系统已经安装过了

4、连接好手机，运行

```
/sbin/ifconfig -a | grep 80:00:60:0f:e8:00   | cut -d " " -f 1
```

记下他输出的东西

```
sudo gedit   /etc/network/interfaces
```

在末尾添加`iface <rndis0> inet dhcp` 

ps:"<>"里面的是我在上面那步里输出的，你应该更换成你自己的，不带“<>“ 保存并关闭，然后运行

```
sudo /etc/init.d/networking restart
```

这次也是按照作的，我的网卡是多出来一个eth1(这个根据自己系统决定)。所以我在interfaces文件后面加入了

```
iface eth1 inet dhcp
```

5、安装OpenSync

```
sudo apt-get install multisync-tools opensync-plugin-evolution opensync-plugin-synce
```

6、现在该安装的都安装好了，就可以开始同步了`synce-sync-engine`如果出现如下错误

```
File "/usr/bin/sync-engine", line 84, in <module>

 configObj = Config.Config(progopts)

   File "/usr/lib/python2.5/site-packages/SyncEngine/config.py", line 292, in __init__

 oldconf = os.path.join(self.path,"config.xml")

AttributeError: Config instance has no attribute 'path'

```

请下载[config.xml](http://synce.svn.sf.net/svnroot/synce/releases/0.11.1/sync-engine/config/config.xml)，保存为~/.synce/config.xml（提示："~"为自己的家目录)

创建同步项目**synce-create-partnership "Linux desktop" "Contacts,Calendar"**

ps：还有别的，可自行选择Contacts， Calendar， Tasks， Files 等

创建OpenSync group 

```
msynctool --addgroup synce-sync

msynctool --addmember synce-sync synce-opensync-plugin

msynctool --addmember synce-sync evo2-sync
```

然后同步synce-sync-engine

```
msynctool --sync synce-sync
```

他会同步到evolution里面

我上面的照着做，全部成功了。

**我的一些问题**

据说安装了synce-gnomevfs后，可以在Nautilus里面看手机里的内容，但是我没有成功
目前只能通过synce-pls、synce-pcp等命令，来操作手机里面文件。
