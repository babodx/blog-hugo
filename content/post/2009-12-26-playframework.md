---
categories: ['Java']
title: "playframework--play！java on rails框架"
date: 2009-12-26 18:41:00
---
今天看程序员，发现了play！framework这个java web框架。

以前也接触过SSH框架，struts+spring+hibernate。。。看着每一个部分那厚厚的参考书。。我就害怕。

后来就看了一阵子ruby on rails。彻底喜欢上了那种简洁的风格，约定优先配置也很棒。

直到发现play框架，才知道原来java也可以这样简洁。play框架扔掉了J2EE的各种规范。。。完全像rails一样简洁了

**Play！的主要特性： **
* 简单的开发周期。该框架可自动编译和重新装载已改变的源文件。 
* 智能绑定HTTP参数到Java方法参数。 
* 基于Apache Mina的快速HTTP服务器[http://mina.apache.org/](http://mina.apache.org) 
* 一个基于Groovy的强大的模板引擎，可实现多层继承，定制用户标签等功能。 
* 优秀的错误报告功能：发生异常时，该框架会直接显示出错误的源代码，甚至是模板代码。 

目前playframework网站的资料也比较全。从安装入门到开发实例应有尽有。我打算有时间慢慢翻译过来，发布在我的博客上。也是对自己学习的一种激励。

**官方网站**

[http://www.playframework.org/](http://www.playframework.org/)

各种实例

<h2>Yet Another Blog Engine</h2>The final application from the <a href="http://www.playframework.org/documentation/1.0/guide1">play guide</a> tutorial.<img src="http://www.playframework.org/documentation/1.0/images/yabe" border="0" width="1050" height="612"><h2>Forum</h2>A simple forum application using JPA.<img src="http://www.playframework.org/documentation/1.0/images/forum" border="0" width="996" height="649"><h2>Jobboard</h2>A full faetured job board application that uses the CRUD module to automatically generate the administration area. <img src="http://www.playframework.org/documentation/1.0/images/jobboard" border="0" width="996" height="649"><h2>Chat</h2>An HTTP based chat application. We use the asynchronous request model to be able to handle a lot of simultaneous connected users without resources waste.<img src="http://www.playframework.org/documentation/1.0/images/chat" border="0" width="996" height="649"><h2>Stock watcher</h2>Based on the default GWT tutorial, it shows how to use GWT within a play application.<img src="http://www.playframework.org/documentation/1.0/images/stocks" border="0" width="996" height="649"><h2>My lists</h2>A clone of the the well known 37Signals TadaList application built using the Google App Engine support. It uses Siena as persistence engine.<img src="http://www.playframework.org/documentation/1.0/images/lists" border="0" width="996" height="649">
