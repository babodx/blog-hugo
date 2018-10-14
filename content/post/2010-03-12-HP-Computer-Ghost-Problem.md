---
categories: ['Hardware']
title: "HP台式机器，ghost系统后，提示windows could not start"
date: 2010-03-12 16:36:00
---
今天单位一台HP dx7518的台式机器，ghost了一个winxp系统。但是只要一开机，就提示

```
windows could not start because of a computer disk hardware configuration problem    
could not read from the selected boot disk check boot path and disk harware   
please check the windows documentation about hardware disk configuation and your hardware refer once manuals for additional information   
```

开始我以为是硬盘的设置问题，后来发现硬盘已经设置为IDE模式了。

后来网上查找，发现是和BIOS里的WDRT Support选项有关系。。。

**解决办法**

开机进入bios设置，选择Power management Setup 再选择WDRT Support 将后面值修改为disable

WDRT SUPPORT：watchdog resource table 一种加密狗。通过这种加密保护来拒绝安装非随机配备的操作系统。同样，全系的HP 家用、商用台式机、笔记本，进行GHOST安装后报错，也可以用此方法解决。
