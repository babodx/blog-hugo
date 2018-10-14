---
categories: ['Linux']
title: 'CENTOS下安装MUDOS跑侠客行'
date: 2010-08-26 15:05:00
---
最近在放假，我又玩了玩很久以前的网络游戏北美侠客行。一个纯文字的MUD游戏。而且自己在CentOS下也架设了一个，下面就说说我的安装过程，给自己做个记录，怕以后忘记。

要架设一个MUD游戏，需要MUDOS和MUDLIB两部分。这里简单解释一下，其实我也刚接触，有不对的地方还望见谅。

MUDOS我理解就是一个跑MUD游戏的平台或者理解为模拟器，只要符合MUDOS要求的MUDLIB都可以通过MUDOS驱动起来。

MUDLIB才是具体游戏的代码，各种MUD游戏其实就是MUDLIB不同，而底层平台基本都是MUDOS。

当然现在除了MUDOS还可以采用FluffOS作为运行平台。

下面贴个我架设的侠客行MUD截图（采用XKX早期代码修改来的，和北美侠客行最接近）
![xkx截图](http://farm9.staticflickr.com/8087/8514869856_f372e947e4_z.jpg)

**下载MUDOS**
我采用的是<a href="http://www.pkuxkx.net/download/soft.php?id=20"><b>MudOSv22pre11 For Linux</b></a> 从北大侠客行官方网站下载的。

**下载MUDLIB**

至于mudlib才是能不能玩的重点，网上可以找到很多版本。我主要下载了如下几种

金庸梦（个人感觉很完整的一套代码，基本是基于98年xkx修改而来）

xkx2001（这个版本是早期一个xkx版本，和北美xkx比较接近）

xkx6dao（和早期xkx最接近的一个版本）

以上三个版本都可以在windows下正常运行，我们要做的就是选择一款移植到linux下。

我是基于xkx6到的mudlib，将xkx2001和金庸梦的部分代码加入过来。这样基本实现了北美侠客行的大部分任务系统，包括斧头帮、镖局、门派任务等

**安装mudos**

1、将我们下载的<a href="http://www.pkuxkx.net/download/soft.php?id=20"><b>MudOSv22pre11 For Linux</b></a>解压缩，然后进入到解压后的目录。

2、运行 ./build.MudOS

提示如下

```
Preparing to build standard MudOS driver ...
Trying out some stuff to see what works; ignore errors ...
gmake: Nothing to be done for `nothing'.
./build.MudOS: line 170: xlc: command not found
mkdir: ÎÞ·¨´´½¨Ä¿Â¼¡®tmp¡¯: ÎÄ¼þÒÑ´æÔÚ
install£ºÎÞÐ§Ñ¡Ïî -- f
Çë³¢ÊÔÖ´ÐÐ¡®install --help¡¯À´»ñÈ¡¸ü¶àÐÅÏ¢¡£
rm: ÎÞ·¨É¾³ý¡®tmp/insttest¡¯: Ã»ÓÐÄÇ¸öÎÄ¼þ»òÄ¿Â¼
mkdir: ÎÞ·¨´´½¨Ä¿Â¼¡®obj¡¯: ÎÄ¼þÒÑ´æÔÚ
***************** Configuration completed **************
Installing MudOS on Linux
Using install -c to install binaries in ../bin.
Using gcc -E for preprocessing.
Using gcc -O2 -fomit-frame-pointer -fstrength-reduce to compile.
Using bison -d -y to make the compiler.
Edit GNUmakefile if this is not what you want
Otherwise, type 'gmake' to build MudOS, then 'gmake install'.
```

上面有些是乱码，因为linux的中文不能正常贴过来，主要是提示一些目录存在和一些安装选项无效。可以不用管。
在安装前，我们用gmake clean 清理一下

3、执行gmake和gmake install进行编译和安装

正常的话，我们就可以在目录下发现driver文件了，这个driver就是为了驱动MUDLIB的。

**配置MUDLIB和运行**

配置mudlib的过程麻烦一些，一个是driver是否符合mudlib需要，另外一个是mudlib的配置。

先说下mudlib的配置，一般是在mudlib的根目录下存在一个config.h的文件（这个名字可以自己修改为任意名字）

这个配置文件一般需要根据自己的实际路径进行修改

```
name: 侠客行      (指定你的游戏名字)
port number: 5555  (指定游戏运行的端口)
mudlib directory: /mud/xkx/ (指向到你mudlib的目录)
binary directory: /mud/xkx/ (同样指向到你mudlib目录，其实是你配置文件目录)
```

其他默认就可以

如果配置全部完成，没有问题。

只要将driver文件拷贝到mudlib目录，我这里是/mud/xkx目录。

然后

```
cd /mud/xkx
./driver config.h &
```

下面是我的运行提示

```
[root@ict xkx]# using config file: config.h
Initializing internal tables....
----------------------------------------------------------------------------
侠客行 (MudOS v22pre11) starting up on Linux - Thu Aug 26 10:21:29 2010

Connection to address server (localhost 3992) refused.
]simul_efun loaded successfully.
Loading preloaded files ...
Initializations complete.
Accepting connections on port 5555.
[root@ict xkx]#
```

**常见问题**

**编码问题：**因为我们的mudlib是从windows平台移植过去的，所以文件都是dos格式，需要进行转换。采用如下命令

```
find . -name *.c|xargs dos2unix
find . -name *.h|xargs dos2unix
find . -name *.o|xargs dos2unix
```

以上命令将所有.c .h .o结尾的文件转换为unix文件格式

**driver编译问题：**很多时候我们编译的driver并不能很好的驱动我们的mudlib。这个时候需要自己查看log目录下的debug.log文件找出具体原因。主要是通过修改 options.h  文件进行调整。
