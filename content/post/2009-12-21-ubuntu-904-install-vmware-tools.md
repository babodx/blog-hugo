--- 
categories: ['Linux']
title: "ubuntu 9.04 server 安装VMware Tools解决share folders问题"
---
我的主系统是Winxp在vmware 6.5.2 build-156735下安装的ubuntu server 9.04

因为安装vmware tools后，可以使用vmware 提供的share folders让winxp的主系统可以将某个目录映射到虚拟的ubuntu系统里。

我就开始在vmware里面安装vmware tools工具。下面是过程，包括不能用vmhgfs的解决办法

**安装VMware tools

安装前，先安装需要的软件包

```
apt-get install autoconf automake binutils make cpp gcc linux-headers-$(uname -r)
```

安装后，在vmware上面选择VM-》Install Vmware Tools

然后切换到ubuntu server 9.04里，运行mount命令，将Vmware tools的光盘挂载
`sudo mount /dev/cdrom /mnt`

拷贝安装VMware Tools需要的安装文件到/tmp目录 
`sudo cp /mnt/VMwareTools-7.8.5-156735.tar.gz /tmp`
 
卸载/mnt目录，因为安装的时候，需要在/mnt目录下创建hgfs目录，如果这时候挂载着CDROM，就会导致创建hgfs目录失败。`sudo /mnt`

解压安装文件并修改会出现问题的page.c文件

```
cd /tmp 
tar zxvf VMwareTools-7.8.5-156735.tar.gz
cd /tmp/vmware-tools-distrib/lib/modules/source</span> 
sudo cp ./vmhgfs.tar /tmp/</span> 
tar xvf vmhgfs.tar </span>
cd vmhgfs-only/ 
sudo vi page.c 
```

修改page.c里面867行的一个错误函数，

```
page = __grab_cache_page(mapping, index); 
```

将这个函数修改为 

```
page = grab_cache_page_write_begin(mapping, index,flags);
``` 
这个函数是在/usr/src/linux-headers-2.6.28-11-server/include/linux$下的，pagemap.h里面定义的，内容如下

```
struct page *grab_cache_page_write_begin(struct address_space *mapping,
                        pgoff_t index, unsigned flags); 
```

修改后，保存。然后重新打包成vmhgfs.tar文件，放置到/tmp/vmware-tools-distrib/lib/modules/source目录下。
完成上面工作后，就可以

```
cd /tmp/vmware-tools-distrib
sudo ./vmware-install.pl
```

安装提示安装就可以了。 

当看到如下提示，代表已经正确编译了vmhgfs模块了。应该可以顺利使用share folders功能。 

The vmhgfs module loads perfectly into the running kernel.
 
也可以运行

`lsmod |grep vmhgfs`

如果有如下显示，表示安装成功

```
vmhgfs                 53728  1 
```

这样就可以用vmware提供的share folders功能和ubuntu server 9.04共享文件了。

