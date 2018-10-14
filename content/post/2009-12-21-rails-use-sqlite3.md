---
categories: ['Rails']
title: 'rails下使用sqlite3'
---

参考[http://wiki.rubyonrails.org/rails/pages/HowtoUseSQLite](http://wiki.rubyonrails.org/rails/pages/HowtoUseSQLite)

**安装SQLite**

SQLite 是一个轻量级的sql风格数据库.可以执行大部分sql92标准SQLite全部安装只有244kb,包括命令行客户端和DLL文件SQLite不用安装服务进程，就像Access数据库一样使用方便安装只需要2个文件

* SQLite DLL</span> 
* SQLite command-line client for creating tables

添加sqlite3.exe 和sqlite3.dll 到系统的path下，我放在了c:\windows\system32下了

**安装sqlite3-ruby.gem**

```
gem install sqlite3-rubyAttempting local installation of 'sqlite3'
Local gem file not found: sqlite3*.gem
Attempting remote installation of 'sqlite3'
Select which gem to install for your platform (i386-mswin32)
1. sqlite3-ruby 1.1.0 (mswin32)
2. sqlite3-ruby 1.1.0 (ruby)
3. sqlite3-ruby 1.0.1 (ruby)
...
>
```

选择 1 安装sqlite3-ruby 1.1.0(mswin32) 现在好像2才是sqlite3-ruby 1.1.0(mswin32),总之选择(mswin32)就对了

**安装图像管理工具**（也可以不用，直接用命令行也很方便）[http://sqlitebrowser.sourceforge.net/](http://sqlitebrowser.sourceforge.net/)

**创建数据库**

进入项目所在目录，比如我的项目是testsqld:\work\testsql>sqlite db\test.db上面命令就在db目录下，创建了一个test.db数据库

**配置database.yml文件，访问test.db数据库**

```
development:
   adapter: sqlite3
   database: db/test.db   #username: root
   #password: 
   #host: localhost
```

后面如何操作，就和使用mysql数据库一样了。建立数据表可以用db:migrate来完成
