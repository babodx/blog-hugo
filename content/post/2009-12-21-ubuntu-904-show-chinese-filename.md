--- 
categories: ['Linux']
title: "ubuntu server 9.04显示中文文件"
---
前面提到了用VMware的share folder功能，可以将主机系统的一个目录，自动共享到ubuntu /mnt/hgfs目录下。

但是winxp的中文目录，到了ubuntu server下面就显示乱码了。

**解决办法**

安装相关软件包

```
sudo apt-get install language-pack-zh
```

**配置中文环境**

修改/etc/environment

```
sudo vim /etc/environment
```

将LANG和LANGUAGE内容修改为下面的 

```
LANG=zh_CN.UTF8<br>
LANGUAGE="zh_CN:zh:en_US:en 
```

修改/etc/default/locale

```
sudo vim /etc/default/locale
``` 
修改内容如下

```
LANG="zh_CN.UTF-8"<br>
LANGUAGE="zh_CN:zh" 
```

保存后，重启系统。 

现在用putty登陆，还是发现看中文乱码。我们还要继续设置putty才可以。 

**设置putty使用utf-8**

putty的Windows->Translation->Received data assumed to be in which character set:选择UTF-8

![putty使用utf8设置](http://farm9.staticflickr.com/8236/8512011412_e138d2c072.jpg)

这样设置后，就可以正确在putty里显示中文了。

![putty显示中文文件名](http://farm9.staticflickr.com/8514/8510902673_84be6e3e30.jpg)

以上这些就可以正常在putty里显示中文了。但是如果你用键盘鼠标从本地登陆ubuntu server，会发现，ubuntu server还是乱码。

这个问题我查了一些文档，还是没有解决。有的说是console下的字体问题。但是/usr/share/consolefonts里的字体好像没有支持中文的。

还有说console fonts最多支持256这个字符。。。

反正最终这个问题没有解决掉。现在在linux自己的控制台里，显示还是乱码

建议大家如果不是特别需要，其实服务器没有必要采用中文编码的。可以正常显示中文就好了。

建议默认还是用en_US:UTF-8的好。

