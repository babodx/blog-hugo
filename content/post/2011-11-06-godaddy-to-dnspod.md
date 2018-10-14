---
categories: ['Host']
title: '解决godaddy注册域名无法解析问题'
date: 2011-11-06 18:25:00
---

godaddy.com是全球知名的域名注册商。国内很多站长也是使用的这个注册商。

但是出于各种原因，godaddy的域名服务器经常被国内封掉。导致我们注册的域名无法被解析。直接影响了网站的稳定性。

解决办法无非两个：一、不用godaddy了，二、换域名服务器。

如果不打算用godaddy了，我推荐name.com也不错，不过不支持支付宝了。需要用paypal支付

而我选择了第二种办法，采用国内的dns服务器来代替godaddy自己的，这样可以很好的解决问题。



具体步骤：

到http://www.dnspod.cn去注册一个免费账号，不知道dnspod为何物的，可以参考下面介绍

关于DNSPod

DNSPod建立于2006年3月份，是国内最早提供免费智能DNS产品的网站，致力于为各类网站提供高质量的多线智能DNS免费解析。

DNSPod以稳定性、安全性、功能强大、智能免费、高速等优 势深得广大站长和企业用户们的喜爱，以至于越来越多的企业站、地方门户站、游戏站等等都在使用DNSPod，他们喜欢的是DNSPod给他们带来的稳定性、承受能力强、访问速度快以及极高的用户体验，使用普通的DNS（像域名注册商提供的）不是不可以“用”，但是避免不了因为不稳定导致网站时常经常打不开，由于功 能限制不能实现想要实现的功能、因为用户体验不够好等丢失客户。所以选择一个好的DNS服务商可以为自己的网站带来很大的、意想不到的收益，避开不必要的损失。

功能上：除了智能DNS解析，普通DNS解析也可以满足用户的稳定、高速等需求。相对竞争对手的产品，DNSPod除了实时生效，不限制用户添加的域名和记录数量外，还率先提供 了IPv6的支持和动态域名解析的功能，另外还支持DNS轮询、URL转发、API接口、批量修改管理等先进功能。并且所有功能都是免费提供。

使用DNSPod的站长们知道，DNSPod不仅仅是以雄厚的技术优势，而且还是靠优质的服务赢得市场的，为几十万免费用户和几千VIP用户分别设立相应的技术支持！做到用户与DNSPod随时可以沟通，有问题及时解决！

暴风影音、华军下载、魅族手机、VeryCD、4399、 霏凡下载、雨林木风、深度、手机之家、去看去看、站长之家、51啦、Discuz、cnBeta、 逐浪文学等知名网站都是DNSPod vip用户。

将域名服务器修改到国内

在 dnspod的后台域名管理中，添加自己需要转到国内的解析的域名。如下图

![dnspod后台](http://farm9.staticflickr.com/8251/8513876981_070153af29_z.jpg)

点开我们刚添加的域名，可以看到分配给我们的dns服务器。如下图

![添加dns服务器](http://farm9.staticflickr.com/8105/8513879533_140115711c_z.jpg)

现在我们登录到godaddy下，修改我们的DNS服务器到刚才dnspod分配给我们的dns服务器上。

登录后，如下图所示，点my account

![account截图](http://farm9.staticflickr.com/8530/8513882515_d24da3b0d9_z.jpg)

然后在域名管理界面，点我们要转到国内解析的域名。如图

![域名管理截图](http://farm9.staticflickr.com/8520/8513884849_f2c2282477_z.jpg)

进入域名管理后，找到NameServers项，点set name servers。如图

![nameserver截图](http://farm9.staticflickr.com/8374/8513887055_cf354bfba5_z.jpg)

上面点击“Set Nameservers”后，会弹出一个窗口让你设置DNS Server。如下图

![set nameserver](http://farm9.staticflickr.com/8518/8515007886_405481c010_z.jpg)

将Nameserver1和Nameserver2里面的内容修改为dnspod分配给我们的dns服务器。nameserver3和nameserver4里面清空。

点 “OK”按钮完成设置。

域名服务器设置完，我们回到dnspod的后台，查看我们域名的状态。一般情况会是无法解析或者dns错误。这个说明还没有生效。最慢48小时生效。如果48小时候，还是错误就要找dnspod联系看看怎么解决了。也可以自己使用dnspod提供的域名全面诊断来判断问题。

下面是完全生效后的状态

![设置完成的状态](http://farm9.staticflickr.com/8371/8513897017_913b9e6764_z.jpg)
