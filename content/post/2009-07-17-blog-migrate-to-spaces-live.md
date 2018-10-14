---
categories: ['Host']
title: "从百度空间转移到spaces live"
date: 2009-07-17 16:00:00
---

本来打算购买一个在美国的VPS，来自己架设个blog程序用的，但是老婆说我太糟蹋钱了，不让玩。

只好选择live spaces的25G免费空间了。为了这次转移，我写了一个小程序帮忙。

现在已经把原来[http://hi.baidu.com/babodx](http://hi.baidu.com/babodx)的博客内容全部搬到了[http://babodx.spaces.live.com/](http://babodx.spaces.live.com)

而且还申请了一个域名玩，可以直接通过[http://www.xinlogs.com/](www.xinlogs.com)访问

这次域名是在[http://www.namecheap.com/](www.namecheap.com)直接用美元买的，据说这样比较保险。

国内的域名服务商信誉很差。

**转移步骤**

我的想法是先用一个程序把百度空间上，我的博客全部保存到本地。然后在用个程序把保存下来的内容发送到spaces live上，就完成了转移。

一、将百度空间的博客保存到本地

首先用程序抓取百度空间的博客内容这个程序并不完全是我自己写的，我参考了很多人的文章和程序。参考如下

[Blog Importer in Ruby](http://www.agileprogrammer.com/dotnetguy/articles/BlogImporterInRuby.aspx)

[Blog content migration from MovableType to Typo using XML-RPC](http://crafterm.net/blog/articles/2006/09/22/blog-content-migration-from-movabletype-to-typo-using-xml-rpc)

[lsblog改进版本](http://cxc200026.blog.163.com/blog/static/342686720092895235567/)

根据前人的经验，我写了一个程序，根据用户名抓取百度空间的博客，然后保存为用户名.xml文件文件格式如下

```
<baidu_space total="70" id="babodx" pagecnt="7">

 <page blogcnt="10" index="0">

   <item href="http://hi.baidu.com/babodx/blog/item/fb1ebcb14ea2075d08230218.html">

     <date>2009-07-12 00:23</date>

     <title>ubuntu server 9.04 终端乱码问题</title>

     <category>Linux</category>

     <body><p>安装了ubuntu server 9.04，安装的时候选择了中文环境。</p> <p>因为server是没有桌面的，发现终端里面显示提示信息，出错信息都变成乱码了。</p> <p>解决办法</p> <p>修改/etc/default/locale文件</p> <p>将zh_CN修改为en_US</p></body>

    </item>
```

用ｉｔｅｍ标签来包含每个博客，用date,title,category,body分别代表发表时间、标题、分类、内容。

RUBY代码主要参考了lsblog的改进版。

[savelog.rb](http://cid-4986f8f322cc617b.skydrive.live.com/self.aspx/.Public/saveblog.rb)

只要提供用户名，就可以将全部博客保存为ｘｍｌ文件

二、将本地保存的xml文件，上传到spaces live空间

这里遇到很多麻烦，以至最终这部分没有能用程序实现。

我的想法是用ruby调用metaweblog api接口来发送blog到spaces live空间

可以参考

[Windows Live Spaces MetaWeblog API](http://msdn.microsoft.com/en-us/library/bb259702.aspx)

后来发送简单的文本已经可以实现了。但是问题出在对ｂｏｄｙ的处理，从百度空间抓过来的ｂｏｄｙ是ｈｔｍｌ代码，里面包含了文章和图片的引用。

如果直接用ＭｅｔａＷｅｂｌｏｇ　ＡＰＩ发送，这些ｈｔｍｌ就直接显示在ｂｌｏｇ上面了，而不是应该有的格式。

我查看了一些文档，也没有找到解决办法，因而只要放弃了。

后来是手动通过live writer把本地的blog.xml发送到live spaces空间的。那个累呀。。。。

**待解决问题**

一是如何让程序自动发送html格式的blog内容到live spaces空间。

二是如何处理博客里面的图片，原来图片都是放在百度空间的，如果在live spaces引用，会出现错误。

ruby可以直接从百度抓图片，问题还是如何上传到live spaces
