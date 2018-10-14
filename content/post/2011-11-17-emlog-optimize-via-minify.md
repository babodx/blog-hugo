---
categories: ['PHP']
title: 'emlog访问速度优化之minify整合css与js文件'
date: 2011-11-17 20:56:00
---
我们知道网站的访问速度和很多因素相关，但是今天这里只是优化我们能力范围内的首页文件加载数量与大小。

如果首页文件很大，肯定加载慢。还有一个因素就是首页如果加载很多css和js文件，因为在加载过程中会建立很多次连接从而降低访问首页的速度。

下面是我博客默认情况下的加载css和js文件的数量

![请求数截图](http://farm9.staticflickr.com/8246/8515051510_535fdf1923_z.jpg)

![文件数](http://farm9.staticflickr.com/8087/8513940611_b373e87aea_z.jpg)

从图上可以看到加载了4个css文件和5个js文件。大小分别为54.2kb和45.5kb。
 
下面我们的主要工具出场：<b>minify</b>

这个程序是纯php编写的开源程序，用来将我们网站的css和js文件进行整合与压缩。这样我们就可以通过整合css与js文件并压缩大小从而提高首页加载速度。
 
首先将minify下载，我这里采用的是minify_2.1.3版本。将下载后的min目录放到网站的根目录。
接着我们改造我们的程序，让css与js文件通过minify程序合并压缩。
 
我是自己写了一个类，我起名为merge.php用来调用minify整合。下面是类代码



```
<?php
/**
 * JS和CSS文件合并类
 *
 * @copyright (c) xinlogs.com All Rights Reserved
 * $Id: merge.php 2011-11-16 09:54:58Z  xinlogs.com$
 */

class Merge {
  //定义两个数组用来存储全部css和js文件的位置
  static private $css_list=array();
  static private $js_list=array();
  static private $js_code=array();
  //定义addCssFile方法，用来添加css文件到数组
  static function addCssFile($addFilePath) {
    array_push(self::$css_list,$addFilePath);
  }
  //定义getCssList方法，用来返回css文件字符串
  static function getCssList(){
    return implode(",",self::$css_list);
  }
  //定义addJsFile方法，用来添加js文件到数组
  static function addJsFile($addFilePath) {
    array_push(self::$js_list,$addFilePath);
  }
  //定义getJsList用来返回js文件字符串
  static function getJsList(){
    return implode(",",self::$js_list);
  }
  //加入js代码
  static function addJsCode($addJsCode)
  {
    array_push(self::$js_code,$addJsCode);
  }

  static function includejscode(){
    return implode(" ",self::$js_code);
  }
  //用来返回加入CSS代码的语句
  static function includecss() {
    $css="<link href=".BLOG_URL."min/?f=".Merge::getCssList()." rel=\"stylesheet\" type=\"text/css\" />";
    return $css;
  }
  //用来返回插件js代码的语句
  static function includejs() {
    $js="<script src=".BLOG_URL."min/?f=".Merge::getJsList()." type=\"text/javascript\"></script>";
    return $js;
  }
}
```

将merge.php放到网站include/lib目录下，emlog就会自动加载这个类了。
 
接着我们修改我们的模板与插件，让所有css与js文件通过Merge这个类来处理。

一般css与js文件都是在模板的header.php文件内加载的，这里以默认模板为例说明

我们打开header.php文件，然后修改css文件加载的语句。将原来加载css的语句，采用Merge::addcssfile方法代替

`Merge::addCssFile("content/templates/default/main.css");`

最后在doAction后面统一输出我们合并后的css文件。

```
<?php doAction('index_head'); ?>
<?php echo Merge::includecss();?>
```

我们再来处理js文件，我是将模板的js文件调整到了fooder.php里加载，下面以这个footer.php为例说明
首先将加载js文件的语句，替换为Merge::addJsFile方法

```
<?php Merge::addJsFile("include/lib/js/common_tpl.js");?>
```

然后在doAction后面再统一调用合并后的js文件

```
<?php doAction('index_footer'); ?>

<?php echo Merge::includejs();?>

<?php echo Merge::includejscode();?>
```
 
以上只是针对模板的修改，我的这个博客还采用了高亮代码的插件，这个插件会调用一些js与css文件。所以下面我还修改这个插件的代码，使它也通过merge类来合并输出css与js。

这个插件主要修改drp_highlighter.php文件，在drp_highlighter_index_head方法里加入css的引入

```
function drp_highlighter_index_head() {

  Merge::addCssFile("content/plugins/drp_highlighter/css/editor.css");

}
```
 
在drp_highlighter_index_footer方法里加入对js文件与代码的引入。

```
function drp_highlighter_index_footer(){

        include(EMLOG_ROOT.'/content/plugins/drp_highlighter/config.php');

        Merge::addJsFile("content/plugins/drp_highlighter/js/jquery.js");

        Merge::addJsFile("content/plugins/drp_highlighter/js/jquery.highlighter.src.js");

        $jscode="

<script type='text/javascript'>

$(document).ready(function(){

        var option={

                dir:'".BLOG_URL."content/plugins/drp_highlighter/js/syntaxhighlighter/',

                name:'code',

                showControls:true,

                skin:'".constant('highlighter')."'

        };

        $.SyntaxHighlighter(option);

});

</script>";

        Merge::addJsCode($jscode);

}
```

最后在文件最后面找到注册action的代码，和上面方法名修改对应上。

```
addAction('adm_sidebar_ext', 'drp_highlighter_adm_sidebar');

addAction('index_head','drp_highlighter_index_head');

addAction('index_footer', 'drp_highlighter_index_footer');

addAction('adm_writelog_head','drp_highlighter_adm_head');
```

下面是我整合css与js代码后的效果

![整合后效果1](http://farm9.staticflickr.com/8088/8515066606_1e38661e7f_z.jpg)

css文件降低为3个，其中有两个为新浪微博的代码，这个不好整合了。。。

![整合后效果2](http://farm9.staticflickr.com/8234/8515070122_92e6aab78e_z.jpg)

js文件数降低为3个，其中一个是微博的，一个是百度统计的。我们自己的代码已经整合到一个请求里调用了。
