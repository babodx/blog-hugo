---
title: "rails中文问题"
date: 2013-03-14 10:56:00
categories: ['Rails']
---
##rails controller中的中文问题

在ruby 1.9环境中，如果controller里出现中文，就会报错。报错如下

```
invalid multibyte char (US-ASCII)
```

**解决办法：**

在这个controller的文件开头加入如下语句

```
#encoding: utf-8
```

##rails validates的中文问题

<!--more-->
当在models里设置了validates以后，提示的错误信息都是英文。而且提示信息前面的字段也都是数据库中的字段名字很不友好。

**解决办法**

这个需要采用i18n 来解决。

首先在我们项目的Gemfile文件中加入如下的gem

```
gem 'rails-i18n'
```

然后在config/application.rb文件中声明采用的语言。我这里是中文的，声明如下

```
config.i18n.default_locale = "zh-CN"
```
这样错误提示信息应该就是中文信息了。不过前面的字段还是数据库中的名字，这个我们需要自定义一个翻译文件放在`config/locales/locales`目录下。我这里是定义了一个zh-CN.yml文件,文件内容如下：

```
zh-CN:
  activerecord:
    attributes:
      student:
        id_num: "身份证号"
        password: "密码"
        password_digest: "密码"
```

**注意** zh-CN: 就是我们`config.i18n.default_locale = "zh-CN" `中定义的。 student 是我model的名字，下面的三项是数据库的字段，也是model的属性。

错误提示界面如下：
![rails中文化错误信息](http://farm9.staticflickr.com/8505/8555747473_f908011335.jpg)

