---
title: "nginx支持thinkphp的pathinfo模式"
date: 2013-04-25 15:06:00
categories: ['Linux']
---

最近帮助一个朋友迁移系统。原有系统是www.pinphp.com开发的一套淘宝客程序，跑在Win03服务器上。因为ddos攻击、性能问题等原因，找我迁移到Linux系统下。

首先我推荐的组合当然是现在流行的Ningx+php-fpm模式。便于优化而且效率很高。

部署的过程中遇到的问题就是伪静态无法生效。一些带中文的url无法正常处理等。

后来看代码发现pinphp这套代码是基于thinkphp框架开发。而thinkphp框架采用一种pathinfo的伪静态模式。而nginx本身是不支持pathinfo模式的，要是通过修改fastcgi 参数来支持，可以实现但是会有安全隐患。

**什么是pathinfo呢？**

因为thinkphp框架要求所有请求都要通过入口文件来转发，所以任何请求都是到index.php后才转发的。为了url好看和便于seo。就产生了pathinfo了，就是我们比如想访问index.php?s=book这样的url，可以直接访问index.php/book. 其实也可以看做是一种特殊的伪静态。index.php后面的url部分作为参数传递给index.php来处理。

**nginx要如何处理**

index.php/book 这种类型url nginx无法很好处理，而且非要通过pathinfo处理可以，但是会有安全隐患问题。

thinkphp文档里的解决办法是采用伪静态，把全部url转发给index.php?s=来处理。
下面就是我在nginx里的转发规则

```
if (!-e $request_filename)
    {
        rewrite  ^(.*)$  /index.php?s=$1  last;
        break;
    }

```

这样/目录后面的url部分就当作index.php?s的参数来处理了。可以完美解决pathinfo问题。而且这样处理后，中文url也可以正常执行了。

