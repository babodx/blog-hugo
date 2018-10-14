---
categories: ['PHP']
title: 微博从bo-blog迁移到了emlog。
date: 2011-10-25 22:05:00
---

今天将微博从原来的bo-blog迁移到了emlog

bo-blog从08年就一直开始使用。感觉还是很不错的。但是一直没怎么更新了，目前很多新功能都不支持。

其实我就打算让博客可以和新浪微博同步。新浪微博里也有个关联博客的功能，据说可以通过博客的rss内容来同步内容。

但是我参考网上文章修改了bo-blog模板，让首页里存在
<link rel="alternate" type="application/rss+xml" href="<a href="http://www.xinlogs.com/feed">http://www.xinlogs.com/feed</a>"  title="鑫的方向" />
然后又测试了<a href="http://www.xinlogs.com/feed">http://www.xinlogs.com/feed</a>，可以正常访问。
但是新浪微博就是不能同步到我博客的内容。
 
最后我发现emlog目前的更新很活跃，而且还有个插件是调用新浪微博的api来将博客内容同步到微博的。就开始了我的迁移过程
迁移主要参考了<a href="http://bbs.bo-blog.com/thread-28348-1-1.html">http://bbs.bo-blog.com/thread-28348-1-1.html</a>
原文如下：
转换过程很简单，先下载并安装emlog_4.0.1，再将原BoBlog中的attachment和data文件夹以及附件中的转换工具上传到新装emlog根目录，执行转换工具按照提示运行即可。<br><br>
        最终可以将BoBlog中的所有数据比较完美地导入emlog_4.0.1，包括原来BoBlog中的附件和自定义URL等等。对于emlog不支持UBB的问题其实应该很好解决：将UBB解析之后再导入数据库即可。由于我的777篇日志绝大部分不含UBB代码所以就没有继续折腾了……
<strong>注意：</strong>
我转换后，没有文章的分类了。但是查看数据库的sort表确实存在分类信息。这个时候，只要在后台建立一个分类。然后其他分类就都出现了。感觉转换效果很不错。

