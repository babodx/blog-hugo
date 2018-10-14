---
categories: ['Hardware']
title: "HTC hero g3 openvpn设置"
date: 2010-03-17 17:53:00
---
现在YouTube和google博客等站点都被屏蔽，为了打破限制，只能用vpn来穿越了。

原来一直用pptpd类型vpn，这种类型速度快，但是要求防火墙或者网关支持。对内外支持不好（反正我在单位和家里歌华宽带都不支持）

后来改用openvpn类型的vpn了，发现虽然openvpn配置麻烦，需要安装客户端。但却是对内网支持良好，穿透能力强。

openvpn可以支持多系统（服务器我肯定用linux，这里多系统主要指客户端）

Windows系统可以采用openvpn gui，这个在我博客以前文章提到了，今天要说的是我的手机，htc hero这次也可以让手机连接openvpn服务器上youtube等站点了。

**手机型号及系统**

我的手机是HTC hero g3

采用的ROM是Flyzup制作的基于android 1.5修改的。

Rom可以到Flyzup网站下载
[http://www.innovative-space.com/2010/02/21/flzyup-custom-rom-v1-6-final-release/](http://www.innovative-space.com/2010/02/21/flzyup-custom-rom-v1-6-final-release/)

其实只要手机用的Rom可以支持openvpn，存在tun.so文件就可以

**安装相关软件**

首先需要需要安装openvpn，这个软件我们可以到下面地址下载已经编译好的版本。因为是static编译的，所以不需要任何动态库支持直接解压就可以使用。
[http://github.com/downloads/fries/android-external-openvpn/openvpn-static-2.1.1.bz2](http://github.com/downloads/fries/android-external-openvpn/openvpn-static-2.1.1.bz2)

将解压后得到的openvpn-static-2.1.1文件改名为openvpn 并拷贝到手机的/system/xbin/目录下

我是采用手机上安装的Better Terminal Emulator Pro来操作的，也可以用adb连接上usb操作。

有了openvpn后，我们还要创建两个软连接才可以让后面的openvpn顺利建立连接

用Better Terminal Emulator Pro进入终端模式，输入如下命令

```
mkdir /system/xbin/bb
ln -s /system/xbin/ifconfig /system/xbin/bb/ifconfig
ln -s /system/xbin/route /system/xbin/bb/route
```

上面创建两个命令的软连接，是因为后面安装的TunnelDroid软件，总是调用/system/xbin/bb目录下的命令，如果没有命令，就会出错。

用google的电子市场，查找TunnelDroid软件，安装TunnelDroid软件。

**配置TunnelDroid**

其实和配置Windows下的客户端一样，也需要一个配置文件和3个密钥文件

我们在sd卡的跟目录创建openvpn目录

在openvpn目录放入client.ovpn配置文件和ca.crt、client-1.crt和client-1.key密钥文件

这样启动TunnelDroid后，就会出现client.ovpn的选项，直接选择连接就可以。上面的配置文件和密钥，我都是从配置好的Windows机器拷贝过来的。

目前我测试的android手机、windowsxp、windows7和Linux全部都可以连接openvpn服务器。

