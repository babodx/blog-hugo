---
categories: ['Rails']
title: 'Rails下request.raw_post的一些问题'
---

我在学习Agile Web Development with Rails在第18章 Observers的例子中

```
<%=text_field_tag :search%>
<%=observe_field( :search,
                  :frequency => 0.5,
                  :update => :results,
                  :url => {:action => :search})%>
```

将text_field_tag里面的内容，每间隔0.5就提交给search这个action,然后将结果显示在<div id="results"></div>层

在search方法里面可以通过request.raw_post获取提交的数据

```
def search
     render:text=>request.raw_post   
end
```

问题是上面这样返回的内容，总是在提交的内容后多一个=号

比如在文本框输入div层显示的就是a=

后来只能通过把最后2位抹去，来达到正常效果render:text=>request.raw_post.to(request.raw_post.length-2)
