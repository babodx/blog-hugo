---
categories: ['CSS']
title: '哀悼日网站和bo-blog变灰色设置'
date: 2010-04-21 10:00:00
---
根据国务院文件，5.19-5.21为全国哀悼日，在此期间，全国和各驻外机构下半旗志哀，停止公共娱乐活动，外交部和我国驻外使领馆设立吊唁簿。5月19日14时28分起，全国人民默哀3分钟，届时汽车、火车、舰船鸣笛，防空警报鸣响。 Admin5与很多草根网站都将整站换成素装。并建议中国所有站点更换为素装。

为方便站点哀悼，特提供css滤镜代码，以表哀悼。以下为全站CSS代码。

`html { filter:progid:DXImageTransform.Microsoft.BasicImage(grayscale=1); }  `

使用方法：这段代码可以变网页为黑白，将代码加到CSS最顶端就可以实现素装。建议全国站长动起来。为在地震中遇难的同胞哀悼。

如果网站没有使用CSS，可以在网页/模板的HTML代码<head>和</head> 之间插入：

```


    <style>  
    html{filter:progid:DXImageTransform.Microsoft.BasicImage(grayscale=1);}   
    style>  

```

有一些站长的网站可能使用这个css 不能生效，是因为网站没有使用最新的网页标准协议

```
 <html xmlns=“http://www.w3.org/1999/xhtml”>   
```

请将网页最头部的<html>替换为以上代码。

有一些网站FLASH动画的颜色不能被CSS滤镜控制，可以在FLASH代码的<object …>和</object>之间插入：

```
    “false” name=“menu”/>   
    “opaque” name=“wmode”/>  
```

最简单的把页面变成灰色的代码是在head 之间加
 
```


    <style type=“text/css”>  
      
    html {   
        FILTER: gray   
    }   
    style>  

```

下面再看看如何给上面这些应用到bo-blog的页面中呢。

首先我们到bo-blog的根目录找到我们的template模板目录，在其中找到我们使用的模板所采用的CSS文件。在文件最后加入

```
html { filter:progid:DXImageTransform.Microsoft.BasicImage(grayscale=1); }    
```
另在哀悼日或遇难的新闻，所有专题和主题 图片上不能使用红色标题。
