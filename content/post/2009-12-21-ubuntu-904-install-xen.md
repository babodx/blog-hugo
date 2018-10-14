---
categories: ['Virtualization']
title: "ubuntu 9.04安装xen"
---
主要参考下面两旁文章，先贴过来，以后再整理。

[http://oceanobservatories.org/spaces/display/CIDev/Xen+on+Ubuntu+9.04+(Jaunty)](http://oceanobservatories.org/spaces/display/CIDev/Xen+on+Ubuntu+9.04+(Jaunty))

[http://linux.com/community/blogs/Xen-Create-a-Jaunty-DomU-using-xen-create-image.html](http://linux.com/community/blogs/Xen-Create-a-Jaunty-DomU-using-xen-create-image.html)

**安装xen**

Xen on Ubuntu 9.04 (Jaunty) 

Added by <a href="http://oceanobservatories.org/spaces/display/~thim">Thomas Im</a> , last edited by <a href="http://oceanobservatories.org/spaces/display/~thim">Thomas Im</a> on Jun 11, 2009  (<a href="http://oceanobservatories.org/spaces/pages/diffpages.action?pageId=18353165&originalId=18353171">view change</a>) 
Labels:  
(None) 
Following applies to Ubuntu 9.04 Jaunty 64-bit installation.  Notes taken from rebuild of 'amoeba' development system. 
Installation
<pre>sudo apt-get install ubuntu-xen-server # Jaunty Xen package does not include the dom0 Xen kernel so these must be installed separately  wget http://security.debian.org/debian-security/pool/updates/main/l/linux-2.6/linux-modules-2.6.26-2-xen-amd64_2.6.26-15lenny3_amd64.deb wget http://security.debian.org/debian-security/pool/updates/main/l/linux-2.6/linux-image-2.6.26-2-xen-amd64_2.6.26-15lenny3_amd64.deb  sudo dpkg -i linux-modules-2.6.26-2-xen-amd64_2.6.26-15lenny3_amd64.deb linux-image-2.6.26-2-xen-amd64_2.6.26-15lenny3_amd64.deb  sudo apt-get install python2.5 shutdown -r now   sudo vim /etc/network/interfaces .. auto eth0 iface eth0 inet static   address xxx.xxx.xxx.xxx   gateway xxx.xxx.xxx.xxx   netmask 255.255.255.0   sudo vim /etc/resolv.conf ... domain ucsd.edu search ucsd.edu  nameserver xxx.xxx.xxx.xxx nameserver xxx.xxx.xxx.xxx   sudo /etc/init.d/networking restart  </pre>Create Jaunty DomU (Guest) 
[http://linux.com/community/blogs/Xen-Create-a-Jaunty-DomU-using-xen-create-image.html] 
<img src="http://oceanobservatories.org/spaces/images/icons/emoticons/information.gif" border="0" width="16" height="16" align="absMiddle"><br><strong><strong>References:</strong></strong> 
<a href="https://help.ubuntu.com/community/Xen">https://help.ubuntu.com/community/Xen</a><br><a href="http://www.infohit.net/blog/adate/053109/archive/1.html">http://www.infohit.net/blog/adate/053109/archive/1.html</a><br><a href="http://www.chrisk.de/blog/2008/12/how-to-run-xen-in-ubuntu-intrepid-without-compiling-a-kernel-by-yourself/">http://www.chrisk.de/blog/2008/12/how-to-run-xen-in-ubuntu-intrepid-without-compiling-a-kernel-by-yourself/</a> 
<h3></h3>

<strong>安装guest系统</strong>
These are instructions for creating an Ubuntu Jaunty DomU on Debian Lenny Dom0 using Xen 3.2.1 that comes with Lenny.  I'm installing into an LVM rather than using a file image.  See <a href="http://www.howtoforge.com/xen-live-migration-of-an-lvm-based-virtual-machine-with-iscsi-on-debian-lenny">this HowtoForge article for background of my particular setup</a>.  This tutorial assumes you're working from a similar setup with an iscsi target and LVM-based VMs. 
The reason I'm posting this is because I had such a hard time finding some combination of tools that worked correctly to do this.  I tried a few options but had little success: 
<ul>
<li>debootstrap - Didn't seem to work with Jaunty at all.  It would just crash </li>
<li>vm-install - Doesn't support LVM-based VMs by default, but there is a procedure for converting the qcow image to an LVM</li>
</ul>
What I found is that I could find no way of creating the LVM-based VM in Lenny.  I actually had to create a sid instance first and then install my open-iscsi and xen-utils into the sid VM, then use SID to populate the VM.  <br><u><strong>Perform the following actions in your SID vm or workstation</strong></u><br>
Note:  You'll need your /etc/xen-tools/xen-tools.conf setup like the instructions in the HowtoForge article (things like gateway, netmask, broadcast, passwd, fs type, install-method, etc).<br>
Whenever I would try to build the vm with the default partitions, it would always fail.  For some reason the resulting vm would have an fstab that shows something like sda1 and sda2 instead of xvda1 and xvda2.  I had to create my own partition scheme before anything would work right.  Here's mine: $ <strong>sudo cat /etc/xen-tools/partitions.d/with-data</strong><br>
[root]<br>
size=4G<br>
type=ext3<br>
mountpoint=/<br>
options=sync,errors=remount-ro<br>
[swap]<br>
size=512M<br>
type=swap<br>
[data]<br>
size=4G<br>
type=ext3<br>
mountpoint=/data<br>
options=nodev 
You will need to create a symlink for jaunty so that the option is recognized at the command-line.  
<strong>$ cd /usr/lib/xen-tools</strong> 
<strong>$ sudo ln -s edgy.d jaunty.d  </strong>
Now, finally, you can create the image.  This step may take some time 
<strong>$ xen-create-image --hostname=myserver --dhcp --lvm=vg_xen --dist=jaunty --mirror=http://archive.ubuntu.com/ubuntu --size=4Gb --memory=512Mb --swap=512MB --arch=i386 --partitions=with-data</strong> 
Since you're building the LVM on a different machine than it will eventually run on, you'll need to  copy the resulting xen config to the correct server: 
<strong>$ scp /etc/xen/myserver.cfg root@realhost:/etc/xen/</strong> 
That should be all you need to get this working. 

