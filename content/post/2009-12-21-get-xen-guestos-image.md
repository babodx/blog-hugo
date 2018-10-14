---
categories: ['Virtualization']
title: "获取XEN guestOS的images文件"
date: 2009-12-21 18:00:00
---

我们可以从[http://stacklet.com/](http://stacklet.com/)其实就是原来[http://jailtime.org/](http://jailtime.org/)站点找我们需要的xen image文件

在2009年的时候，jailtime.org发布了一个新闻

######May 4th, 2009######
The jailtime project has been relaunched with a new name and site: <a href="http://stacklet.com/">Stacklet</a> 
As part of the relaunch all of the distributions currently on jailtime have been upgraded to their latest versions. Also, all of the code for creating the images is now publically available – see <a href="http://stacklet.com/">http://stacklet.com/</a> for further details.... 
Jailtime will remain online but downloads will probably be turned off eventually as the transition to stacklet proceeds. 

所以现在就访问[http://stacklet.com/](http://stacklet.com/)就可以了。 

包括如下系统 

<li>
<a href="http://stacklet.com/downloads/images/list/CentOS">CentOS</a> <ul>
<li>
<a href="http://stacklet.com/downloads/images/centos/5.3">5.3</a> </li>
<li><a href="http://stacklet.com/downloads/images/centos/5.3.64bit">5.3 (64-bit)</a></li>
</ul>
</li>
<li>
<a href="http://stacklet.com/downloads/images/list/Debian">Debian</a> <ul>
<li>
<a href="http://stacklet.com/downloads/images/debian/5.0">5.0 (Lenny)</a> </li>
<li><a href="http://stacklet.com/downloads/images/debian/5.0.64bit">5.0 (Lenny, 64-bit)</a></li>
</ul>
</li>
<li>
<a href="http://stacklet.com/downloads/images/list/Fedora">Fedora</a> <ul>
<li>
<a href="http://stacklet.com/downloads/images/fedora/10">10</a> </li>
<li><a href="http://stacklet.com/downloads/images/fedora/10.64bit">10 (64-bit)</a></li>
</ul>
</li>
<li>
<a href="http://stacklet.com/downloads/images/list/Gentoo">Gentoo</a> <ul>
<li>
<a href="http://stacklet.com/downloads/images/gentoo/2008-0">2008.0</a> </li>
<li><a href="http://stacklet.com/downloads/images/gentoo/2008-0.64bit">2008.0 (64-bit)</a></li>
</ul>
</li>
<li>
<a href="http://stacklet.com/downloads/images/list/Mandriva">Mandriva</a> <ul>
<li>
<a href="http://stacklet.com/downloads/images/mandriva/2009.0">2009.0</a> </li>
<li><a href="http://stacklet.com/downloads/images/mandriva/2009.0.64bit">2009.0 (64-bit)</a></li>
</ul>
</li>
<li>
<a href="http://stacklet.com/downloads/images/list/Slackware">Slackware</a> <ul>
<li><a href="http://stacklet.com/node/13">12.2</a></li>
</ul>
</li>
<li>
<a href="http://stacklet.com/downloads/images/list/Ubuntu">Ubuntu</a> <ul>
<li>
<a href="http://stacklet.com/downloads/images/ubuntu/9.04">9.04 (Jaunty)</a> </li>
<li><a href="http://stacklet.com/downloads/images/ubuntu/9.04.64bit">9.04 (Jaunty, 64-bit)</a></li>
</ul>
</li>
</ul></li>
<li>

<strong>下面是一个站点提供的img文件的简单说明</strong>
<strong>All files on this site are provided without guarantee or warranty. Use at your own risk.</strong> 
Each download is a bzipped tarball containing an image and cfg files: 
<ul>
<li>a xen guest filesystem
</li>
<li>sample xen configuration files to be used with <strong>xm create</strong>
</li>
</ul>The naming convention of the download tarball is: 
<strong><distribution>.<distro-version>.<created-date>.img.tar.bz2</strong>. 
For example centos.5-3.x86.20090423.img.tar.bz2 contains 
<ul>
<li>centos.5-3.x86.img
</li>
<li>centos.5-3.x86.xen3.cfg
</li>
<li>centos.5-3.x86.xen3.pygrub.cfg（这个文件比较重要，如果用的是CentOS 5.3的虚拟化，就需要这个文件才可以启动guestOS。因为CentOS 5.3没有提供Xen-U的内核。）
</li>
</ul>All of the packages installed in the image will be the latest versions available as of the created date, which is in yyyy-mm-dd format. 
<h3>Prerequisites</h3>
<ol>
<li>Working installation of Xen version 3
</li>
<li>The xen daemon (xend) must be running
</li>
<li>Enough filesystem space to accomodate the uncompressed tarball (generally 1GB)
</li>
<li>Enough unallocated RAM to run the xen guest (generally 128MB)
</li>
</ol>
<h3>Default Configuration and Networking</h3>
Each filesystem is configured to use dhcp. The *.xen.cfg also is also configured for dhcp. It is recommended that you first test the filesystem using dhcp if possible before attempting other networking parameters. Each download page has additional notes on networking that is appropriate for a particular distribution. 
The root login is root/password. It is highly recommened that you change the root password immediately. 
<h3>Installation</h3>
You will need to uncompress the tarball to a local directory and edit the *.xen.cfg file to point to the uncompressed image and swap file. 
If all is well you will see the guest’s boot messages scrolling by. 
<strong>一些HOWTO文档</strong> 
<h3><strong>HOWTO: Filesystems</strong></h3>
In the following howto’s, <image file> should be replaced with a reference to a specific .img file. 

<h4><strong>How do I mount an image without booting it?</strong></h4>
<ul>
<li>mkdir -p /mnt/loop
</li>
<li>mount -o loop <image file> /mnt/loop/
</li>
</ul>
<h4><strong>How do I resize an image file?</strong></h4>
First, make sure that the image file is not already mounted and is not already running as a xen guest. The following commands increase an image file to 2.5GB. Backup the image before attempting this. 
<ul>
<li>dd if=/dev/zero of=<image file> bs=1M conv=notrunc count=1 seek=2500 
</li>
<li>losetup /dev/loop0 <image file>
</li>
<li>e2fsck -f /dev/loop0
</li>
<li>resize2fs /dev/loop0
</li>
<li>e2fsck -f /dev/loop0
</li>
<li>losetup -d /dev/loop0
</li>
</ul>You may then boot or mount the image to confirm the increased size. The e2fsck checks in this howto are not strictly necessary. 

<h4><strong>How do I move the contents of an img file to a regular partition?</strong></h4>
Mount the img file using the above howto. Assuming the destination partition is mounted at /mnt/dest, execute the following: 
<ul>
<li>cp -a /mnt/loop/* /mnt/dest/
</li>
</ul>You will then be able to boot the filesystem using the partition instead of the image file, which should provide better performance. You will need to update the *.xen.cfg file to reference the partition instead of the img file (the <strong>disk</strong> parameter will need to change, see the <a href="http://www.cl.cam.ac.uk/Research/SRG/netos/xen/readmes/user/user.html">Xen Manual</a>). Also, remember to unmount partitions or img files before booting a xen guest from them! 

</li>
