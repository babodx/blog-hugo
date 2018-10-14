---
categories: ['Hardware']
title: "我的新手机HTC hero g3"
date: 2010-02-02 16:44:00
---
关注HTC hero G3很久了，大约从10月份开始吧。当时就和老婆说生日打算换个手机，后来生日当天去中关村看了看，但是没有购买，报价3000。关键是不知道怎么选购。回来后网上做功课，查询论坛和淘宝。最后决定在淘宝的一家北京实体店购买。在木樨园桥附近。2010年1月25日周一购买的。

购买的最终手机价格是2860元，又选购了8G TF卡，一块MOMAX电池+座充+车充+手机套。3370元。

购买后，由卖家给刷安逸1.2的ROM并给8G卡预装一些软件。

整体感觉比我原来用的windows手机eten x800速度快了不少。android系统也很漂亮。

我的联系人是先用Win7的windows mobile设备管理将原来windows手机的联系人导出到outlook，最后导出到excel并生成cvs文件。

然后导入到Gmail邮件的通讯录。这样通过hero G3的同步gmail联系人，就可以导入到新手机了。

感觉和gmail配合的相当好。短信也可以备份到gmail保存。

**刷机**

手机买来就开始装软件，尝试导航，使用感觉不错。不过在论坛学习，发现很多系统都比我这个新，就产生了刷机的想法。

开始先尝试刷radio。radio是负责手机通讯的模块，可以理解为电话用的驱动吧。

我原来的版本比较低，自己先尝试刷了63.18.55.06JU_6.35.09.26最新版本。

**刷radio办法**

机器买来的时候，又商家默认刷了啊兴的2.5 recovery。我就用了一个1G的卡，格式化为FAT32文件系统。将下载的radio更新文件拷贝到卡的根目录，并改名为update.zip

关闭手机，同时按Home+Power键进入recovery界面(就是小房子+开机键)。

等出现recovery界面，选择第2项，将sd卡上的update.zip刷入手机。

注意刷完后，一定要重启一次手机。

刷这个很简单，一会就完成了。感觉不错。呵呵

关于Rom的选择，我再论坛上找了很久，也发帖求助了很多高手。大家普遍认为安卓1.2就是稳定，但是速度很慢。

大家推荐我的主要是Flzyup的Rom和modaco的Rom

我怕Modaco的Rom中文支持不友好，就决定从Flzyup制作的Rom选择了。目前来说G3的Rom有基于android 1.5的，也有基于2.0

但是普遍认为2.0 Rom目前不成熟，毕竟官方没有出来呢。所以我就只选择基于android 1.5的Rom了。

F大（flzyup的亲切称呼）的Rom基于1.5系统的主要如下

* [FLZYUP Custom ROM v1.5 final(惊喜一)](http://www.innovative-space.com/2010/01/14/flzyup-custom-rom-v1-5-final/)

* [FLZYUP Custom ROM v1.4 (Paid APPs+New ICONs)](http://www.innovative-space.com/2010/01/10/flzyup-custom-rom-v1-4-paid-apps-new-icons/)

* [FLZYUP_Custom_ROM_v1.3_final大内存版本](http://www.innovative-space.com/2009/12/23/flzyup_custom_rom_v1-3_final-large-me/)

* [FLZYUP Custom ROM v1.3 final all fixed [特效包已发布]](http://www.innovative-space.com/2009/12/07/flzyup-custom-rom-v1-3-release/)

1.4版本是基于1.3的基础上优化完善的，1.5是基于全新内核制作的。

因为我看论坛上很多人反映F大1.5版本耗电，本来G3也就坚持2天，如果刚买手机经常玩，1天就够呛了。。。所以没敢使用这个费电的新版本。我保守的选择了1.4版本。

**准备刷机**

论坛坚持了2天，大家一直讨论刷那个版本好，可以参考[http://www.androidin.net/bbs/thread-51055-1-1.html](http://www.androidin.net/bbs/thread-51055-1-1.html)

最终决定刷F大的1.4版本。看到F大1.4是自动支持app2sd技术的，但是前提是你的卡必须有ext分区。我的8G卡目前就1个Fat32分区，于是考虑先分区。

但是Win7没有找到合适的分区软件，后来发现xda论坛发布的<strong>RA-hero-v1.5.2</strong> recovery可以支持在手机的recovery模式给卡分区。我就先刷了这个recovery

**刷recovery**

主要参考[http://forum.xda-developers.com/showthread.php?t=561124](http://forum.xda-developers.com/showthread.php?t=561124)

There are several ways to install a custom recovery, choose the one that suits you most (I probably forgot a few).

```
via adb 
-> Requires a custom recovery (with test-keys) like Cyanogen's v1.4 or my previous recovery

Copy recovery-RA-hero-v1.5.2.img to the root of your sdcard
Boot into your current custom recovery (boot while holding HOME)
Connect your Hero via usb to your pc/mac/...
adb shell
$su (not required if you have root already)
#mount -a
#flash_image recovery /sdcard/recovery-RA-hero-v1.5.2.img

```
我采用的上面这个方法安装的。

不过首先要在机器上安装adb工具和手机驱动

驱动采用网上下载的AndroidDriverFiles驱动程序.rar

adb本来是包含在android sdk工具里面的，但是sdk太大了，好几百兆。。。。我就在网上找了一个adb_win.zip的分离包。572k，就2个文件。

先将adb_win.zip解压缩，得到adb.exe和AdbWinApi.dll文件。将2个文件复制到Windows目录下的System32目录。

这样在windows的命令行窗口输入adb命令，可以看到如下输出

Android Debug Bridge version 1.0.20下面还有很多使用说明，就不用管了。

现在准备好adb了，再把AndroidDriverFiles驱动程序.rar解压到一个目录，准备连接手机的时候使用。

以上都准备好后，关闭手机，按Home+Power键进入recovery模式，将USB线连上电脑。如果提示安装驱动，就手动到AndroidDriverFiles驱动程序解压后的目录找。

驱动识别完成后，就打开windows的命令行窗口（Win+R，输入cmd回车）

输入adb shell

然后su回车

mount -a回车

上面的su和mount -a 都可能返回错误，不用管它。

最后输入flash_image recovery /sdcard/recovery-RA-hero-v1.5.2.img 回车。

这样就把新的recovery刷入了。

重启进入recovery模式，如图

![recovery截图](http://farm9.staticflickr.com/8096/8512558972_02deaf66fe.jpg)

然后在Wipe里面执行如下3项

```
Wipe

Wipe data/factory reset :: Wipe /data and /cache
Wipe Dalvik-cache :: Wipe Dalvik-cache both on /data and ext
Wipe SD:ext partition : Wipe the ext partition on your sdcard
```

再给sd卡分区。

```
Partition sdcard

Partition SD :: Interactive SD partitioning
```

这样就给sd卡分好去了，我的8G卡，默认是32M的swap分区 512M的ext分区。最后是一个大的Fat32分区。
这样就可以关机了。

**刷FLZYUP_Custom_ROM_v1.4-signed.zip系统**

上面的准备工作全完成了，再来刷系统。

将分好区的sd卡，放到电脑读卡器上，从电脑将FLZYUP_Custom_ROM_v1.4-signed.zip拷贝到根目录。因为win7只能识别出FAT32分区，所以肯定是拷贝到FAT32分区上。

然后返回手机开机进入recovery模式，选择

```
Flash zip from sdcard :: Flash a zip update file from your sdcard
```

按照提示往下刷就可以了。刷好后提示如下

![rom_update截图](http://farm9.staticflickr.com/8511/8511451731_17da3c1a26.jpg)

然后重启手机就可以了。

**注意**

刷完后，第一次重启很慢很慢，这个正常。我当时以为手机刷坏了，总停在HTC那屏上。。。还到论坛发帖求助呢。。

后来发现第一次重启后，看到绿色小人和听到声音大约1分20秒，然后就是那个HTC三个字母的屏幕，大约等了10分钟。。。就会进入下面屏幕

![android 1.4](http://farm9.staticflickr.com/8369/8511453551_4a0cd686ef.jpg)

按照提示设置好手机，就可以享受新系统了。
