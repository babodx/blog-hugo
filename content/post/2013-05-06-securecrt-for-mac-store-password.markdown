---
title: "mac下的secureCRT如何保存密码？"
date: 2013-05-06 11:03:00
categories: ['Mac']
---
mac下面的secureCRT默认保存不上密码，我们选择了保存密码后，下次登录还是提示密码错误。

解决办法：
因为secureCRT默认采用mac的keychain来处理密码，所以会出现这个问题。我们只需要去掉这个选项即可正常。

打开secureCRT后，按"command+," 在"advanced"里面去掉"Use Keychain"的选项即可。

![解决secureCRT在mac下无法保存密码问题](http://farm9.staticflickr.com/8417/8711955329_2f851cb291_b.jpg)
