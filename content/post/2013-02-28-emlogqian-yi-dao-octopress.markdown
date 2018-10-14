---
title: "emlog迁移到octopress"
date: 2013-02-28 17:05:00
categories: ['Octopress'] 
---
用emlog的博客系统已经有2年了，本着活着就要折腾的原则。最近开始尝试使用octopress，这个博客还真的很适合技术人员。通过github版本控制，采用自己最喜欢的编辑器本地写博客，`rake deploy`发布。

目前我原来的博客100多篇都迁移到octopress里了，不过因为原来的图片都存放在本地，这次打算全部存放到flickr.com了。这部分只能手动整理了。

下面说说我是怎么迁移的吧
<!--more-->
网站上有很多人介绍了wordpress到octopress的迁移文章，可以搜索下。jekyll也有一篇专门介绍迁移的[wiki文章](https://github.com/mojombo/jekyll/wiki/blog-migrations)

**思路**

因为原来emlog博客是采用mysql来存储文章的，而且文章都是采用html和一些js来控制格式的和octopress差别很大，octopress每篇文章是采用markdown语法的单独文件。

我是通过emlog的rss.php将全部博客导出为一个xml文件，然后通过一个ruby脚本将这个xml拆分为每篇一个markdown文件，并对一些html进行过滤。

以我的博客为例，原来域名是http://xinlogs.com 首先进入后台设置，设置rss输出篇数为全部博客，且输出全文。因为默认只输出10篇并且是摘要部分。

我将导出的xml命名为xinlogs.xml。

然后在我的octopress/source/下面创建_import目录，并进入_import目录执行导入脚

```
ruby -r "./migrate.rb" -e "Jekyll::EmlogDotCom.process('xinlogs.xml')" "xinlogs.xml" 
```

这个脚本是我根据网上的wordpress脚本修改的，主要是一些xml用的标签和wordpress的不同，我已经修改好了。这个脚本将把xml文件按照每篇日志拆分并保存在_post目录下面，每篇对应一个.md的文件。

我们只要将.md文件移动到octopress/source/_post目录里，然后执行

```
rake gen_deploy
```

就可以发布到主机上了。

**注意**
这个migrate.rb脚本调用了一些其他的gem，所以如果提示错误，需要安装相关的gem。
例如`gem install hpricot`

[migrate.rb](https://github.com/babodx/babodx.github.com/blob/source/source/_import/migrate.rb)
