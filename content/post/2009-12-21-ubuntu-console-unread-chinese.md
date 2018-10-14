---
categories: ['Linux']
title: "ubuntu server 9.04 终端乱码问题"
---
安装了ubuntu server 9.04，安装的时候选择了中文环境。

因为server是没有桌面的，发现终端里面显示提示信息，出错信息都变成乱码了。

**解决办法**
修改/etc/default/locale文件

将zh_CN修改为en_US

