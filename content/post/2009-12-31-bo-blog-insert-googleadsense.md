---
categories: ['PHP']
title: "bo-blog 2.1.1如何加入google广告条"
date: 2009-12-31 16:29:00
---
刚开始使用的bo-blog的时候也不知道如何加入google广告条，经过这几天的咕哝，现在已经成功在右边和下面加入。方法记录如下

**右侧边栏加入google广告**

进入后台管理->常规管理->模块配置

点击左侧的新建/编辑项目 如图

![编辑项目截图](http://farm9.staticflickr.com/8102/8512175362_3d5fd9e7c9.jpg)

然后在手动添加项目中选择侧边模块，确定。如图

![侧边截图](http://farm9.staticflickr.com/8370/8512175480_2bb7b4d188.jpg)

确认后，进入到新增/编辑项目页面

项目名称--这个是在后台编辑的时候，给自己分辨模块用的，随意命名。我的叫做googleadsense，注意：只能字母和数字

项目描述--随意填写

抬头文字--显示在首页侧边栏上，我的叫做广告栏，根据个人情况写

栏目内容--这里写入google的广告代码就可以了

这个办法最容易，不用修改模板。如果加入后，首页显示google广告栏，里面内容是白的，不要着急，过段时间就可以显示了。我第一个广告大约在30分钟后，才正确显示出来。

博客底部加入google广告

底部加入广告，也可以用上面类似的办法，通过加入底部模块实现。但是我尝试了，不好对齐，而且有的模板会破坏底部风格。所以我没有用添加底部模块的办法。

我采用直接修改模板的elements.php文件方法。

到bo-blog的template目录下，找到自己使用的模板文件夹，我用的neowin2，所以对应目录就是neowin2

在模板目录下，打开elements.php文件。找到文件里的displayfooter位置，在displayfooter段里面加入google广告代码。入下所示

``` php

$elements['displayfooter']=<<<eot
<div class="clear"> </div>
</div>
<div class="content-box-b">
<div class="content-box-b-r">
<div class="content-box-b-l"></div>
</div>
</div>
/* 下面为google广告代码 */
<center>
<script type="text/javascript"><!--
google_ad_client = "pub-2759888315234430";
/* 728x90, ... 09-12-21 */
google_ad_slot = "9531615827";
google_ad_width = 728;
google_ad_height = 90;
//-->
</script>
<script type="text/javascript"
src="http://pagead2.googlesyndication.com/pagead/show_ads.js">
</script>
</center>
/*google广告代码结束*/
<div id="footer">
<div id="footer-r">
<div id="footer-l">
<div id="footer-misc">
Skin By <a href="http://www.neowin.net/" target="_blank">Neowin</a><br />
Transplanted By <a href="http://blog.meiu.cn/" target="_blank">Lingter</a>
</div>
<div id="footer-copyright">
{section_foot_components}
<div id="processtime">
</div>
</div>
<div class="clear"></div>
</div>
</div>
</div>
</div>
eot;
```
上面的代码就是在博客下面，footer信息的上面，加入一条google广告。
