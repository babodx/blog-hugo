---
categories: ['Linux']
title: "CentOS 5.3下OpenVPN安装和Win7下OpenVPN GUI安装"
date: 2010-03-15 14:28:00
---
本来我是在美国的VPS服务器上安装的pptp vpn，这个vpn可以用windows自带的拨号连接，配置也很方便。刚配置好的时候很好用，可以开youtube也可以访问一些被封闭的站点。但是后来家里的歌华有线好像调整了一些路由配置，导致我在家里就不能连接vpn了。单位也不能连接。我用老婆家里的adsl尝试连接正常，用联通3G连接也正常。。。。这个既然是网络的问题，估计个人也解决不了了

最近单位也开始搞起来封锁了，开心、verycd等都被封。。。又不能用pptpd vpn了。。。看来该想想其他办法了。代理尝试了，不管用，看来不是基于域名的限制。
于是就开始尝试采用openvpn了。

参考了

[http://www.throx.net/2008/04/13/openvpn-and-centos-5-installation-and-configuration-guide/](http://www.throx.net/2008/04/13/openvpn-and-centos-5-installation-and-configuration-guide/)

[http://www.xiaohui.com/dev/server/20070514-install-openvpn.htm](http://www.xiaohui.com/dev/server/20070514-install-openvpn.htm)

**2010年10月14日更新**

加入官方CentOS下,iptables规则修改.保证顺利nat上网

**整体方案**

采用位于美国的CentOS 5.3 Linux服务器搭建openvpn服务器，并通过iptables的nat功能使openvpn服务器当做客户端网关。

客户端安装OpenVPN GUI程序连接服务器

**服务器**

服务器采用位于美国的vps,系统CentOS 5.3

安装openvpn作为vpn服务器软件

**OpenVPN服务器安装**

kernel 需要**支持 tun 设备**, 需要加载 iptables 模块. 

检查 tun 是否安装:

```
modinfo tun
     
或者
     
find / -name tun.o -print

```

安装需要的相关软件

```
 yum install rpm-build
 yum install autoconf.noarch
 yum install zlib-devel
 yum install pam-devel
 yum install openssl-devel
```

安装环境准备好后，我们下载需要安装的软件。一共需要下载两个软件openvpn 2.0.9和lzo-1.08-4

```
wget http://openvpn.net/release/openvpn-2.0.9.tar.gz
wget http://openvpn.net/release/lzo-1.08-4.rf.src.rpm
```

安装lzo

```
rpmbuild --rebuild lzo-1.08-4.rf.src.rpm
rpm -Uvh /usr/src/redhat/RPMS/i386/lzo-*.rpm

安装openvpn

```
rpmbuild -tb openvpn-2.0.9.tar.gz
rpm -Uvh /usr/src/redhat/RPMS/i386/openvpn-2.0.9-1.i386.rpm
```

安装后，复制配置文件

```
cp -r /usr/share/doc/openvpn-2.0.9/easy-rsa/ /etc/openvpn/
cp /usr/share/doc/openvpn-2.0.9/sample-config-files/server.conf /etc/openvpn/

配置生成CA脚本需要的配置文件vars

```
vi /etc/openvpn/easy-rsa/vars
```

打开vars后，找到文件最后的如下内容

```
export KEY_COUNTRY=KG
export KEY_PROVINCE=NA
export KEY_CITY=BISHKEK
export KEY_ORG="OpenVPN-TEST"
export KEY_EMAIL="me@myhost.mydomain"
```

根据自己信息修改上面内容,下面是具体含义

```
export KEY_COUNTRY=KG 设置国家
export KEY_PROVINCE=NA 设置省份
export KEY_CITY=BISHKEK 设置城市
export KEY_ORG="OpenVPN-TEST" 设置组织
export KEY_EMAIL="me@myhost.mydomain" 设置邮件
```

设置好后，执行如下命令

```
cd /etc/openvpn/easy-rsa/
. ./vars
./clean-all
```

注意上面的. ./vars两个点之间有一个空格

建立CA证书

```
./build-ca
```
生成后，ls keys 可以看到ca.crt ca.key文件

建立服务器密钥

```
./build-key-server xinlogs
```

注意这里的xinlogs是我给密钥起的名字，可以根据个人情况修改

生成Diffie-Hellman文件

```
./build-dh
```

以上文件都正确生成后，拷贝文件到正确目录

```
cd /etc/openvpn/easy-rsa/
cp keys/ca.crt ../
cp keys/dh1024.pem ../
cp keys/xinlogs.key ../
cp keys/xinlogs.crt ../
```

生成客户端密钥

```
./build-key client-1
```

这里client-1是客户端密钥的文件名，如果需要创建多个客户端密钥，就修改client-1名字多次生成即可。

修改/etc/openvpn/server.conf配置

```


    local 204.74.212.217
    #修改local后面ip为服务器地址
     
    dev tap
    ;dev tun
    #默认是dev tun修改为dev tap,tap是可以路由模式 tun 是以太网隧道模式。具体区别我也不太清楚
     
    ca ca.crt
    cert xinlogs.crt
    key xinlogs.key
    #cert后面修改为生成的服务器crt文件xinlogs.crt
    #key后面修改为生成的服务器key文件xinlogs.key
    dh dh1024.pem
    #dh后面修改问生成的dh1024.pem
    server 10.8.0.0 255.255.255.0
    #server后面基本就用默认的10.8.0.0 255.255.255.0即可
    ifconfig-pool-persist ipp.txt
    #这个默认的ipp.txt就可以
    push "route 10.8.0.0 255.255.255.0"
    #添加客户端路由
    push "redirect-gateway"
    #修改客户端默认路由
    push "dhcp-option DNS 8.8.8.8"
    #修改客户端默认dns
    client-to-client
    #允许连接到vpn的客户端可以互相访问
    duplicate-cn
    keepalive 10 120
    comp-lzo
    #启用lzo压缩
    user nobody
    group nobody
    persist-key
    persist-tun
    status /var/log/openvpn-status.log
    log /var/log/openvpn.log
    log-append /var/log/openvpn.log
    verb 3

```

**启动停止openvpn**

```
    service openvpn start
    #启动openvpn服务
     
    service openvpn stop
    #停止openvpn服务
```

**配置iptables**

```
    iptables -t nat -A POSTROUTING -s 10.8.0.0/24 -o eth0 -j SNAT --to-source 214.174.212.217
    #上面命令最后的214.174.212.217是服务器的公网地址，需要根据自己情况修改
    service iptables save
```

确认net.ipv4.ip_forward = 1后，服务器就全部配置完成。

如果是用的CentOS官方版本,不是VPS.那因为它自带的iptables规则限制,还需要在/etc/sysconfig/iptables加入下面语句才可以顺利nat上网

```
-A RH-Firewall-1-INPUT -i tap+ -j ACCEPT  
```

```
    cat /etc/sysctl.conf |grep forward
    # Controls IP packet forwarding
    net.ipv4.ip_forward = 1
     
    #如果不是1，请用vi修改/etc/sysctl.conf文件
```

**Win7下Openvpn GUI安装**

从 http://openvpn.se下载安装文件

Latest stable release: 1.0.3 with OpenVPN 2.0.9 (2006-10-17)

我们直接下载http://openvpn.se/files/install_packages/openvpn-2.0.9-gui-1.0.3-install.exe

注意：下载后，别着急双击安装。先右键属性，设置兼容模式为windosxp sp3 并用管理员身份运行。

![openvpn gui 属性截图](http://farm9.staticflickr.com/8378/8514766422_351fae319a.jpg)

然后再运行安装。

**客户端配置**

安装后，将服务器/etc/openvpn/easy-rsa/keys/目录下的ca.crt、client-1.crt和client-1.key三个文件拷贝到C:Program FilesOpenVPNconfig目录下

再将C:Program FilesOpenVPNsample-config目录下的client.ovpn文件拷贝到C:Program FilesOpenVPNconfig目录下。

在开始->所有程序里找到openvpn，进入里面右键点击OpenVPN GUI属性，同样修改兼容模式为windows xp sp3 以管理员运行

修改后，运行openvpn gui程序

正确运行后，电脑的右下角会出现openvpn的图标，右键点击选择Edit Config来修改客户端配置文件

下面是我全部客户端配置文件

```


    ##############################################
    # Sample client-side OpenVPN 2.0 config file #
    # for connecting to multi-client server. #
    # #
    # This configuration can be used by multiple #
    # clients, however each client should have #
    # its own cert and key files. #
    # #
    # On Windows, you might want to rename this #
    # file so it has a .ovpn extension #
    ##############################################
     
    # Specify that we are a client and that we
    # will be pulling certain config file directives
    # from the server.
    client
     
    # Use the same setting as you are using on
    # the server.
    # On most systems, the VPN will not function
    # unless you partially or fully disable
    # the firewall for the TUN/TAP interface.
    dev tap
    ;dev tun
     
    # Windows needs the TAP-Win32 adapter name
    # from the Network Connections panel
    # if you have more than one. On XP SP2,
    # you may need to disable the firewall
    # for the TAP adapter.
    ;dev-node MyTap
     
    # Are we connecting to a TCP or
    # UDP server? Use the same setting as
    # on the server.
    ;proto tcp
    proto udp
     
    # The hostname/IP and port of the server.
    # You can have multiple remote entries
    # to load balance between the servers.
    remote 214.174.212.217 1194
    ;remote my-server-2 1194
     
    # Choose a random host from the remote
    # list for load-balancing. Otherwise
    # try hosts in the order specified.
    ;remote-random
     
    # Keep trying indefinitely to resolve the
    # host name of the OpenVPN server. Very useful
    # on machines which are not permanently connected
    # to the internet such as laptops.
    resolv-retry infinite
     
    # Most clients don't need to bind to
    # a specific local port number.
    nobind
     
    # Downgrade privileges after initialization (non-Windows only)
    ;user nobody
    ;group nobody
     
    # Try to preserve some state across restarts.
    persist-key
    persist-tun
     
    # If you are connecting through an
    # HTTP proxy to reach the actual OpenVPN
    # server, put the proxy server/IP and
    # port number here. See the man page
    # if your proxy server requires
    # authentication.
    ;http-proxy-retry # retry on connection failures
    ;http-proxy [proxy server] [proxy port #]
     
    # Wireless networks often produce a lot
    # of duplicate packets. Set this flag
    # to silence duplicate packet warnings.
    ;mute-replay-warnings
     
    # SSL/TLS parms.
    # See the server config file for more
    # description. It's best to use
    # a separate .crt/.key file pair
    # for each client. A single ca
    # file can be used for all clients.
    ca ca.crt
    cert client-1.crt
    key client-1.key
     
    # Verify server certificate by checking
    # that the certicate has the nsCertType
    # field set to "server". This is an
    # important precaution to protect against
    # a potential attack discussed here:
    # http://openvpn.net/howto.html#mitm
    #
    # To use this feature, you will need to generate
    # your server certificates with the nsCertType
    # field set to "server". The build-key-server
    # script in the easy-rsa folder will do this.
    ;ns-cert-type server
     
    # If a tls-auth key is used on the server
    # then every client must also have the key.
    ;tls-auth ta.key 1
     
    # Select a cryptographic cipher.
    # If the cipher option is used on the server
    # then you must also specify it here.
    ;cipher x
     
    # Enable compression on the VPN link.
    # Don't enable this unless it is also
    # enabled in the server config file.
    comp-lzo
     
    # Set log file verbosity.
    verb 3
     
    # Silence repeating messages
    ;mute 20
     
    route-method exe
    route-delay 2

```

其实主要修改的就是如下地方
复制内容到剪贴板

```
    client
    #说明这个是客户端配置文件
     
    dev tap
    ;dev tun
    #这个和服务器一样就可以
     
    remote 214.174.212.217 1194
    #这个ip要修改为服务器的公网ip地址
     
    resolv-retry infinite
    nobind
    persist-key
    persist-tun
     
     
    ca ca.crt
    cert client-1.crt
    key client-1.key
    #上面三行一定要根据自己生成的密钥配合
     
    comp-lzo
    #启用lzo压缩
     
    # Set log file verbosity.
    verb 3
     
    route-method exe
    route-delay 2 

    #最后这两行win7如果不加上，就不能启动修改路由，导致拨vpn成功，但是不能通过远程服务器做网关上网
```

这些配置完成后，右键点openvpn gui在桌面右下角图标选择Connect连接

我正确连接后的日志如下

```


    Mon Mar 15 13:03:14 2010 OpenVPN 2.0.9 Win32-MinGW [SSL] [LZO] built on Oct 1 2006
    Mon Mar 15 13:03:14 2010 IMPORTANT: OpenVPN's default port number is now 1194, based on an official port number assignment by IANA. OpenVPN 2.0-beta16 and earlier used 5000 as the default port.
    Mon Mar 15 13:03:14 2010 WARNING: No server certificate verification method has been enabled. See http://openvpn.net/howto.html#mitm for more info.
    Mon Mar 15 13:03:14 2010 LZO compression initialized
    Mon Mar 15 13:03:14 2010 Control Channel MTU parms [ L:1574 D:138 EF:38 EB:0 ET:0 EL:0 ]
    Mon Mar 15 13:03:14 2010 Data Channel MTU parms [ L:1574 D:1450 EF:42 EB:135 ET:32 EL:0 AF:3/1 ]
    Mon Mar 15 13:03:14 2010 Local Options hash (VER=V4): 'd79ca330'
    Mon Mar 15 13:03:14 2010 Expected Remote Options hash (VER=V4): 'f7df56b8'
    Mon Mar 15 13:03:14 2010 UDPv4 link local: [undef]
    Mon Mar 15 13:03:14 2010 UDPv4 link remote: 204.74.212.217:1194
    Mon Mar 15 13:03:16 2010 TLS: Initial packet from 204.74.212.217:1194, sid=3d4cf00a 84deb309
    Mon Mar 15 13:03:18 2010 VERIFY OK: depth=1, /C=US/ST=Beijing/L=BEIJING/O=xinlogs.com/CN=babodx/emailAddress=babodx@gmail.com
    Mon Mar 15 13:03:18 2010 VERIFY OK: depth=0, /C=US/ST=Beijing/O=xinlogs.com/CN=babodx/emailAddress=babodx@gmail.com
    Mon Mar 15 13:03:20 2010 Data Channel Encrypt: Cipher 'BF-CBC' initialized with 128 bit key
    Mon Mar 15 13:03:20 2010 Data Channel Encrypt: Using 160 bit message hash 'SHA1' for HMAC authentication
    Mon Mar 15 13:03:20 2010 Data Channel Decrypt: Cipher 'BF-CBC' initialized with 128 bit key
    Mon Mar 15 13:03:20 2010 Data Channel Decrypt: Using 160 bit message hash 'SHA1' for HMAC authentication
    Mon Mar 15 13:03:20 2010 Control Channel: TLSv1, cipher TLSv1/SSLv3 DHE-RSA-AES256-SHA, 1024 bit RSA
    Mon Mar 15 13:03:20 2010 [babodx] Peer Connection Initiated with 214.174.212.217:1194
    Mon Mar 15 13:03:21 2010 SENT CONTROL [babodx]: 'PUSH_REQUEST' (status=1)
    Mon Mar 15 13:03:21 2010 PUSH: Received control message: 'PUSH_REPLY,route 10.8.0.0 255.255.255.0,redirect-gateway,dhcp-option DNS 8.8.8.8,route-gateway 10.8.0.1,ping 10,ping-restart 120,ifconfig 10.8.0.3 255.255.255.0'
    Mon Mar 15 13:03:21 2010 OPTIONS IMPORT: timers and/or timeouts modified
    Mon Mar 15 13:03:21 2010 OPTIONS IMPORT: --ifconfig/up options modified
    Mon Mar 15 13:03:21 2010 OPTIONS IMPORT: route options modified
    Mon Mar 15 13:03:21 2010 OPTIONS IMPORT: --ip-win32 and/or --dhcp-option options modified
    Mon Mar 15 13:03:21 2010 TAP-WIN32 device [本地连接 3] opened: .Global{A958F4F2-14AF-49E6-9FBF-4FC25B8D8786}.tap
    Mon Mar 15 13:03:21 2010 TAP-Win32 Driver Version 8.4
    Mon Mar 15 13:03:21 2010 TAP-Win32 MTU=1500
    Mon Mar 15 13:03:21 2010 Notified TAP-Win32 driver to set a DHCP IP/netmask of 10.8.0.3/255.255.255.0 on interface {A958F4F2-14AF-49E6-9FBF-4FC25B8D8786} [DHCP-serv: 10.8.0.0, lease-time: 31536000]
    Mon Mar 15 13:03:21 2010 Successful ARP Flush on interface [29] {A958F4F2-14AF-49E6-9FBF-4FC25B8D8786}
    Mon Mar 15 13:03:23 2010 TEST ROUTES: 2/2 succeeded len=1 ret=1 a=0 u/d=up
    Mon Mar 15 13:03:23 2010 route ADD 214.174.212.217 MASK 255.255.255.255 192.168.2.1
    操作完成!
    Mon Mar 15 13:03:23 2010 route DELETE 0.0.0.0 MASK 0.0.0.0 192.168.2.1
    操作完成!
    Mon Mar 15 13:03:23 2010 route ADD 0.0.0.0 MASK 0.0.0.0 10.8.0.1
    操作完成!
    Mon Mar 15 13:03:23 2010 route ADD 10.8.0.0 MASK 255.255.255.0 10.8.0.1
    操作完成!
    Mon Mar 15 13:03:23 2010 Initialization Sequence Completed

```
