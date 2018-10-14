---
categories: ['Linux']
title: "CentOS 5.3 64bit安装在HP 380G5服务器"
date: 2009-12-21 18:00:00
---

今天安装了一台CentOS 5.3 64bit服务器。

服务器硬件HP 380G5，4U高

* CPU 4核处理器 2颗
* 内存 4G 硬盘 147G*3

安装很顺利，采用server模式，去掉了不用的软件，加入虚拟化。

安装后启动，占用内存在230M左右，感觉比CentOS 4.7高了很多呀。。。我在一台HP 140的1U服务器安装的CentOS 4.7 启动后，只占用29M内存。

内存占用问题，还需要看看具体是什么原因。

测试下性能，我比较喜欢用unixbench测试。

[http://www.hermit.org/Linux/Benchmarking/](http://www.hermit.org/Linux/Benchmarking/)

我这次下载的5.0版本，因为5.1以后支持2D,3D我没有装X11，运行不了。

结果5.0也是运行了一半就出问题了。奇怪呀。

我的一台CentOS 4.4 装在一个双核 Intel(R) Xeon(TM) CPU 2.66GHz，1G内存的机器上。用4.1.0-wht测试，得分只有145.不过是跑着java、tomcat、apache、mysql和vmserver的时候测试的。内存使用400M左右的时候。

我看到网上很多128M的VPS居然也可以得到160多分，真是奇怪呀。而我这台双4核处理器xeon e7320,4G内存，装CentOS的机器，也只有下面的数据

```
[root@moodle unixbench-4.1.0-wht-2]# ./Run
make all
make[1]: Entering directory `/root/unixbench-4.1.0-wht-2'
Checking distribution of files
./pgms exists
./src exists
./testdir exists
./tmp exists
./results exists
make[1]: Leaving directory `/root/unixbench-4.1.0-wht-2'
   #    # #    # # #    #          #####   ###### #    #   ####   #    #<br>
   #    # ##   # #   # #           #    # #       ##   # #    # #    #<br>
   #    # # # # #    ##            #####   #####   # # # #       ######<br>
   #    # # # # #    ##            #    # #       # # # #       #    #<br>
   #    # #   ## #   # #           #    # #       #   ## #    # #    #<br>
    ####   #    # # #    #          #####   ###### #    #   ####   #    #
                 4        1           Based on the Byte Magazine Unix Benchmark
                44       11
   v   v       4 4        1
    v v       44444       1           v4.1 revisions mostly by David C. Niemi,
     v           4   o   111 - WHT.2 Reston, VA, USA niemi@tux.org
WHT Variant by Andy A. Lee andylee@dslextreme.com
See: http://www.webhostingtalk.com/showthread.php?s=&threadid=308055

Dhrystone 2 using register variables 1 2
Double-Precision Whetstone 1 2
Execl Throughput 1
Filesystem Throughput 1024 bufsize 2000 maxblocks 1
Filesystem Throughput 256 bufsize 500 maxblocks 1
Filesystem Throughput 4096 bufsize 8000 maxblocks 1
Pipe Throughput 1 2
Pipe-based Context Switching 1 2
Process Creation 1
System Call Overhead 1 2
Shell Scripts (8 concurrent) 1
==============================================================
BYTE UNIX Benchmarks (Version 4.1-wht.2)
System -- Linux moodle.ict 2.6.18-128.el5xen #1 SMP Wed Jan 21 11:12:42 EST 2009 x86_64 x86_64 x86_64 GNU/Linux
                     271677672   3293760 254360848   2% /
Start Benchmark Run: Tue Jun 30 06:43:06 CST 2009
06:43:06 up 7:53, 1 user, load average: 0.19, 0.76, 0.81
End Benchmark Run: Tue Jun 30 06:53:17 CST 2009
06:53:17 up 8:03, 1 user, load average: 15.43, 6.42, 3.16

                     INDEX VALUES
TEST                                        BASELINE     RESULT      INDEX
Dhrystone 2 using register variables        376783.7 19031818.6      505.1
Double-Precision Whetstone                      83.1     1448.6      174.3
Execl Throughput                               188.3     1593.9       84.6
File Copy 1024 bufsize 2000 maxblocks         2672.0   118911.0      445.0
File Copy 256 bufsize 500 maxblocks           1077.0    42442.0      394.1
File Read 4096 bufsize 8000 maxblocks        15382.0   946428.0      615.3
Pipe-based Context Switching                 15448.6   253316.5      164.0
Pipe Throughput                             111814.6 1223105.0      109.4
Process Creation                               569.3     3279.0       57.6
Shell Scripts (8 concurrent)                    44.8      508.7      113.5
System Call Overhead                        114433.5   907496.2       79.3
                                                                 =========
     FINAL SCORE                                                     182.5
```

总分 182.5 真不知都那些VPS测试出 400分的，是什么情况。
难道是可以针对unixbench优化？
