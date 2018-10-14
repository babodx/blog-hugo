---
categories: ['Virtualization']
title: '安装和使用xen-shell'
date: 2009-12-21 18:00:00
---
转自：[http://www.xen-tools.org/software/xen-shell/install.html](http://www.xen-tools.org/software/xen-shell/install.html)

Installing & using the Xen-Shell 

This shell makes several assumptions in the way that it operates: 

* That you have a Xen 3.x host running. 
* Then Xen host has several guests running. 
* That you don't wish to give your users shell access on the host. 

If these assumptions are not valid for your environment then you might find that you have problems. 

**Installation**

Download either [a release](http://www.xen-tools.org/releases.html), or a current version of [the CVS repository](http://www.xen-tools.org/cvs.html) and install it by executing:

`make install`

**Configuring Users**

Each user is expected to login directly to the xen-shell, and not have any other access to the host system. To do this change their login shell by running:


`chsh -s /usr/local/bin/xen-login-shell username`

This will ensure when they connect to the system they will login to the shell, and run it within GNU Screen. 

**Note:**CVS and Debian binary packages use /usr/bin instead of /usr/local/. 
Once you've changed the shell for the user you must update your Xen configuration files so that they include the username(s) of the users who can control them. 
To allow the user "bob" and the user "steve" to control the Xen guest "builder.my.flat" you would add the following to /etc/xen/builder.my.flat.cfg:

```
# All instances must have a name name = 'builder.my.flat'     
# These users may control this instance. xen_shell = 'bob, steve' 
```

**Bandwidth Accounting**

The shell allows the user to view the bandwidth they have transferred over a given period, using the bandwidth command. 
For this to work the vnstat tool must be installed and configured upon dom0. It is assumed that each Xen guest will have their network interface named the same as the guest name. 
For example a Xen guest called 'skx' will have a network interface 'skx' upon dom0. If you're using the standard Xen configuration setup you can achieve this by changing your skx.cfg file to read:

`vif = [ 'vifname=skx,ip=1.2.3.4' ]`

**Note:** if you have more than one IP address allocated to a guest you will have to create a more complicated setup. 


**Security**

Since the xen administrator command xm requires root privileges to run you will need to configure sudo to allow the users who are running your shell to execute that command, without being prompted for a password, as root. 
Add something similar to the following to your /etc/sudoers file:

```
User_Alias   XENUSERS = steve,bob,fred,foo 
Cmnd_Alias   XEN      = /usr/sbin/xm 
Cmnd_Alias   XENIMG   = /usr/bin/xen-create-image 
XENUSERS     ALL      = NOPASSWD: XEN,XENIMG 
```

**Note:**you only need to grant the ability to run xen-create-image if you wish to enable reimaging support in the shell. 

**DNS Updates**

When the shell starts it will look for the file ips.txt in the home directory of the user who connects. If that file doesn't exist then the rdns command will be disabled. 

If you wish to allow users to control their reverse DNS create that file and give it contents such as these:

`192.168.1.1 foo.my.domain 192.168.1.2 bar.my.domain`

This file will be updated by the user when they invoke the rdns command. It is up to you to read the files and create the corresponding zonefiles / DNS updates. The shell will only manipulate the file - not push DNS updates itself. 

**Reimaging Support**

If a user connects to the shell and has the executable file image.sh in their home directory the reimage command will be enabled. 
When the user invokes the command the image.sh script is executed after prompting for confirmation, and performing a last-chance count-down. 
It is up to you to create and configure this script to do the right thing. A sample file for user bob could look like this:

```
#!/bin/sh /usr/bin/xen-create-image --hostname=bob \  
--ip=192.168.1.1  \  
--ip=192.168.1.2 \  
--size=9.5G \  
--swap=512M  --mem=256M \  
--force 
```

This will require a correctly configured instance of [xen-tools](http://www.xen-tools.org/software/xen-tools/) and the sudoer file to contain the ability for the user to run xen-create-image.

Any remaining questions? [contact the author](http://www.xen-tools.org/contact/)

