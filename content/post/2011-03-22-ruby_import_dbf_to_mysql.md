---
categories: ['Ruby']
title: 'ruby将DBF文件导入MYSQL库'
date: 2011-03-22 19:43:00
---
单位招生项目，需要向MySQL灌入一些数据测试。因为原始数据都是DBF文件，就是foxpro数据库。

下面是用ruby代码实现的导入功能。

```
require 'rubygems'
require 'dbf'
require 'mysql2'
require 'iconv'
@the_year="2011"
@start_time=Time.now
@major_list=[]
client = Mysql2::Client.new(:host => "localhost", :username => "root",:password=>"dxzddp",:database=>"enroll")#创建数据库连接
results = client.query("SELECT major_id FROM major WHERE the_year='#{@the_year}'", :as => :array)
results.each do |row|
@major_list.push(row[0])
end
p @major_list
table = DBF::Table.new("d:/ss.dbf")
table.each do |record|
@sfzh=record.sfzh
@name=Iconv.iconv("utf-8", "gb18030", record.xm)
@sfzh=record.sfzh
@ksh=record.ksh
@major=@major_list.choice
@major2=@major_list.choice
@major2=@major_list.choice while @major2==@major
@to_student_info="insert into student_info (student_no,access_pwd,the_year) VALUES ('#{@sfzh}','e10adc3949ba59abbe56e057f20f883e','#{@the_year}')"
@to_candidate="insert into candidate (candidate_name,id_card_no,candidate_no,state,has_submitted,the_year) VALUES ('#{@name}','#{@sfzh}','#{@ksh}','A','Y','#{@the_year}')"
@to_candidate_ideal="insert into candidate_ideal (candidate_id,first_major,second_major,allow_adjustment,is_living_in_college) VALUES (last_insert_id(),'#{@major}','#{@major2}','Y','Y')"
@to_candidate_score="insert into candidate_score (candidate_id) VALUES (last_insert_id())"
@to_contact_info="insert into contact_info (candidate_id) VALUES (last_insert_id())"
@to_school_info="insert into school_info (candidate_id) VALUES (last_insert_id())"
begin
client.query(@to_student_info)
client.query(@to_candidate)
client.query(@to_candidate_ideal)
client.query(@to_candidate_score)
client.query(@to_contact_info)
client.query(@to_school_info)
rescue Mysql2::Error=>e
puts e.to_s
end
end
client=nil if client
p Time.now-@start_time

```
大体思路就是

1、建立数据库连接

2、查询招生专业major

3、打开dbf文件

4、每读取一行，想Mysql数据库写入一行，随机生成报考专业

5、关闭数据库连接

6、打印出用时

