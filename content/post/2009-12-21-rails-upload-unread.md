---
categories: ['Rails']
title: 'rails下上传文件并且解决乱码问题'
---
这个问题是我在[www.javaeye.com](http://www.javaeye.com/)论坛上提出的，并且由mathsfan给出了解决办法

首先创建一个项目，叫**test**

在app目录下的controllers目录下，找到**test_controller.rb**文件

添加一个upload的action

```
def upload

end
```

然后在view目录下的test目录下，创建一个**upload.rhtml**文件

```
<%=form_tag({:action=>'save'},:multipart=>true)%>
<br/>upload your file:<%=file_field("file","file")%>
<br/>
<%=submit_tag("upload")%>
<%=end_form_tag%>
```

现在访问[http://localhost:3000/test/upload](http://localhost:3000/test/upload)，应该可以看到一个上传的界面了。

不过还没有处理上传后的方法呢，所以还不能实现上传。因为上面的表单是提交给save这个action 的，现在还没有写呢。

在写save之前，先要完成一些相关的方法

在**application.rb**文件里，加入

```
def uploadFile(file) #处理上传后的文件保存
       if !file.original_filename.empty? 
          #生成一个随机的文件名
          @filename=getFileName(file.original_filename) 
          #向dir目录写入文件 
          File.open("#{RAILS_ROOT}/public/images/#{@filename}", "wb") do |f|
           f.write(file.read) 
          end 
          #返回文件名称
          return @filename 
        end 
end 
```

```
def getFileName(filename) 
#获取文件名
  if !filename.nil? 
     return filename 
  end 
end 
```

现在在**test_controller.rb**里写出save的action

```
def save 
  unless request.get? 
    if filename=uploadFile(params[:file]['file']) #调用application.rb</strong>里写的uploadFile对文件保存
       render :text=>filename 
    end 
  end 
end 
```

文件可以正确上传到public/images下 
但是中文文件名变为了乱码,但是render :text=>filename返回的名字,并不是乱码.

 ###解决办法###

在ApplicationController加上： 

```
before_filter:set_charset 

def set_charset 
     @headers["Content-Type"]="text/html;charset=gb2312" 
end 
```

我试验了一下，用utf8也可以正常处理,比如按照如下写法 

```
def set_charset
  @headers["Content-Type"]="text/html;charset=utf8" 
end 
```

