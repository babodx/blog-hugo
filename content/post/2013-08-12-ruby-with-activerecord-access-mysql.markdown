---
title: "Ruby采用ActiveRecord方式访问Mysql"
date: 2013-08-12 11:03:00
categories: ['Ruby']
---
前段时间一个朋友的DZ论坛因为误操作导致部分帖子被删除，因为发现的比较晚，所以再删除后已经有很多新帖子被发布了。所以就无法简单的还原到帖子被删除的某个时间点，因为这样就会造成数据库回档，很多新的帖子将消失。

我想到的处理办法就是采用一个删除点前的备份恢复到一个新表中，然后用程序判断被删除的帖子导入到现在的数据库中。而最近一直比较迷恋ruby的我，理所当然就采用ruby来写了。

ruby直接访问mysql可以采用mysql2这个gem，但是我发现居然不支持预编译sql语句，导致一些帖子里的特殊字符无法处理。后来就采用了ruby直接加载ActiveRecord的方法来处理。下面就是我最后的代码了。

下面这个代码是为了修复数据导入后，DZ里面两个表直接关联错误，导致的某些版块显示不正常的问题。

<!--more-->

``` ruby
require 'rubygems'
gem 'activerecord'
require 'mysql2'
require 'active_record'

//准备ActiveRecord的连接参数
ActiveRecord::Base.establish_connection(
  :adapter => 'mysql2',
  :database => 'sq_tvtv',
  :username => 'root',
  :password => 'root',
  :host => 'localhost',
  :encoding => 'utf8',
  :socket =>'/Applications/XAMPP/xamppfiles/var/mysql/mysql.sock'
)

//ORM的映射，一共用到了三张表
class Cdb_thread < ActiveRecord::Base
end

class Cdb_typeoptionvar < ActiveRecord::Base
end

class Temp < ActiveRecord::Base
end

//对帖子进行遍历操作，并导入Cdb_typeoptionvar表。
tids=Cdb_thread.all
puts "tids is num : #{tids.count}"

tids.each do |tid|
  #puts tid["tid"]
  vars=Cdb_typeoptionvar.where([ "tid = ?", tid["tid"]])
  if vars.count==0
      puts "fix tid : #{tid["tid"]}"
      ts=Temp.where(["tid = ?",tid["tid"]])
      if ts.count>0
        puts "found tid: #{tid["tid"]}"
        ts.each do |t|
          t1=Cdb_typeoptionvar.new
          t1.sortid=t["sortid"]
          t1.tid=t["tid"]
          t1.optionid=t["optionid"]
          t1.expiration=t["expiration"]
          t1.value=t["value"]
          t1.save
          puts "insert sortid : #{t["sortid"]}"
        end

      end

  end
end
```

我是直接把数据库连接信息写在代码里了，其实也可以单独写在一个配置文件里，通过yaml来加载过来。可以参考如下代码

首先把数据库连接信息提出到一个database.yml文件中

``` ruby
adapter：mysql2
database：sq_tvtv
username：root
password：root
host：localhost
encoding：utf8
socket：/Applications/XAMPP/xamppfiles/var/mysql/mysql.sock
```

然后需要访问数据库的程序按照如下代码加载上这些数据库配置信息即可

``` ruby
require 'rubygems'
require 'active_record'
require 'yaml'

dbconfig = YAML::load(File.open('database.yml'))
ActiveRecord::Base.establish_connection(dbconfig)
```

