---
categories: ['Linux']
title: "ubuntu8.04 玩魔兽世界 2.4.3(8606)"
date: 2009-12-21 10:05:00
---
最近一直在用ubuntu 8.04当桌面系统。已经很少用windows了。随着采用ubuntu也就一直没有玩魔兽世界了。

今天将Windows下的WOW目录直接拷贝到了ubuntu下面。我的wine是用sudo apt-get install默认安装的，没有特殊设置。

将wow目录拷贝到了用户目录的 .wine/drive_c/Program Files目录下。然后直接双击wow.exe运行。

这个时候会提示如下画面的错误。

![wow报错](http://farm9.staticflickr.com/8519/8512093598_321828a3ef.jpg)

这个不要紧，我们在启动的时候加入-opengl 参数就可以解决。

下面这个是成功启动的命令和截图

`wine .wine/drive_c/Program\ Files/World\ of\ Warcraft/Wow.exe -opengl -nosound`

![ubuntu运行wow](http://farm9.staticflickr.com/8098/8510986535_97c4797180_z.jpg)

已经成功启动了，感觉还不错。这个是在我IBM R61的笔记本上运行结果。

下面进入游戏看看速度

![IBM R61 wow速度](http://farm9.staticflickr.com/8376/8512101836_cf8ce35689_z.jpg)

开始的时候，我的运行速度不行。只有可怜的35帧每秒。

在我关闭了所有特效后，速度到了惊人的100帧每秒。。。这个速度完全可以正常游戏了。

再看看我桌面右侧的状态cpu 53%占用 内存709.88MB 感觉还可以吧。

虽然已经AFK了，但是ubuntu能玩魔兽世界，还是让我很开心的。 

