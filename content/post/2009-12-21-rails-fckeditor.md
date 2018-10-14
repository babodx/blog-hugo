---
categories: ['Rails']
title: 'rails配合fckeditor实现文本编辑工具条'
date: 2009-12-21 18:00:00
---
主要参考了

[http://blog.caronsoftware.com/articles/2006/08/07/fckeditor-plugin-for-rails](http://blog.caronsoftware.com/articles/2006/08/07/fckeditor-plugin-for-rails)

[http://www.blogjava.net/rocky/archive/2006/11/04/rails-fckeditor-integration.html](http://www.blogjava.net/rocky/archive/2006/11/04/rails-fckeditor-integration.html)

**安装**

下载[http://rubyforge.org/projects/fckeditorp/](http://rubyforge.org/projects/fckeditorp/)项目的 FCKEditor Plugin for Rails，将下载的文件加压缩后，保存到fckeditor文件夹中

将fckeditor文件夹拷贝到自己项目的vendor/plugins目录下

运行rack fckeditor:install

然后在需要用到这个插件的页面，输入`<%= javascript_include_tag "fckeditor/fckeditor" %>`或者将这句话添加到app/view/layouts/下的对应文件中

**使用**

需要使用这个工具条的地方，输入

`<%=fckeditor_textarea("post", "body", :toolbarSet => 'Simple', :width => '100%', :height => '500px' )%>`

注意fckeditor_textarea的一个参数post必须是action传过来的一个实体类，如果action没有传过来，就会提示出错

比如我这里用到了post,我的action如下

```
def new<br>
      @post = Post.new<br>
    end
```

第二个参数，body对应表单里的字段

第三个参数toolbarSet=>"Simple"为工具条风格，这个风格可以在public/javascripts/fckcustom.js文件里面定义（修改后，要关闭浏览器再开才起作用）

第四、五个参数，代表文本区域的大小

**图片上传**

默认安装后，图片上传不能用，需要修改vendor/plugins/fckeditor/app/controllers/fckeditor_controller.rb文件的upload_file action如下

```
def upload_file
      @new_file = params[:NewFile]
      @url = upload_directory_path
      begin
        ftype = @new_file.content_type.strip
        if ! MIME_TYPES.include?(ftype)
          @errorNumber = 202
          puts "#{ftype} is invalid MIME type"
          raise "#{ftype} is invalid MIME type"
        else
          path = current_directory_path + "/" + @new_file.original_filename
          File.open(path,"wb",0664) do |fp|
            FileUtils.copy_stream(@new_file, fp)
          end
          @errorNumber = 0
        end
      rescue => e
        @errorNumber = 110 if @errorNumber.nil?
      end
    
      # Fix provided by Nicola Piccinini -- <a href="http://superfluo.org/">http://superfluo.org</a>
      render :text => %Q'<script>window.parent.OnUploadCompleted(<a href="mailto:#%7B@errorNumber%7D,%20%22#%7BUPLOADED+%22/%22+params%5B:Type%5D+%22/%22+@new_file.original_filename%7D%20%22);</script>%20">#{@errorNumber},\"#{UPLOADED+"/"+params[:Type]+"/"+@new_file.original_filename}\");</script>'</a>
    
    end
```

关于工具条具体某个工具怎么显示，提供那些功能，可以在public/javascripts/fckeditor/fckconfig.js文件里面定义
