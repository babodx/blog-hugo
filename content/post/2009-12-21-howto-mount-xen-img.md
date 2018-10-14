---
categories: ['Virtualization']
title: '怎么mount一个xen的img映像。转载'
date: 2009-12-21 18:00:00
---

+ First you need to find out the partitions and the startsector of partitions:

```
[root@xen rruban]# file rheltest.img rheltest.img: x86 boot sector, GRand Unified Bootloader (0.94); 
partition 
1: ID=0x83, active, starthead 1, startsector 63, 208782 sectors; partition 
2: ID=0x8e, starthead 0, startsector 208845, 3871665 sectors, code offset 0x48
```

There are 3 partitions inside the image file. The startsector of each partition is also listed. Boot partition will have start sector 63.

+ Now you need to get the sector size:

```
[root@xen]fdisk -lu rheltest.img
Disk rheltest.img: 0 MB, 0 bytes 255 heads, 63 sectors/track, 0 cylinders, total 0 sectors 
Units = sectors of 1 * 512 = 512 bytes 
Device Boot Start End Blocks Id System 
rheltest.img1 * 63 208844 104391 83 Linux 
rheltest.img2 208845 4080509 1935832+ 8e Linux LVM 
```

The above shows the sector byte size is 512 byte.

+ To calculating the offset: offset = start_sector x sector_byte_size.

The startsector is 63 for the first partition, therefore the first partition offset is: 63x512=32256

+ Finally, to mount the xen image, use:

```
mount -o loop,offset=32256 test.img /foldername
```
