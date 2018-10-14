---
title: "rails-best-practices-2"
date: 2013-03-16 10:23:00
categories: ['Rails']
---
#SCOPE IT OUT

和前面一样，让我们先来看一段“SAD CODE”。

/app/controllers/tweets_controller.rb

``` ruby
def index
  @tweets = Tweet.find(
              :all,
              :conditions => {:user_id => current_user.id},
              :order => 'created_at desc',
              :limit => 10
             )
  @trending = Topic.find(
                :all,
                :conditions => ["started_trending > ?", 1.day.ago],
                :order => 'mentions desc',
                :limit => 5
               )
  ...
end
```

我们先来看看@tweets获取这部分，代码是想取得当前用户下面的10条tweet并按照创建时间排序。

通过在model里定义scope来将排序条件转移到model，并且优化上面代码为如下样子：
<!--more-->
``` ruby
def index
  @tweets = current_user.tweets.recent.limit(10)
  ...
end
```

而在tweet model里，添加如下的scope

/app/models/tweet.rb

``` ruby
class Tweet < ActiveRecord::Base
  scope :recent, order('created_at desc')
  ...
end
```

我们也可以把这个排序条件定义为default_scope 那么我们的查询条件就可以省略recent部分了。

下面我们再来看看对@trending部分的改写，我们先在Topic model中定义scope

/app/models/topic.rb

``` ruby
class Topic < ActiveRecord::Base
  scope :trending,lambda { where('started_trending > ?', 1.day.ago).
                           order('mentions desc')}
  ...

```

然后修改/app/controllers/tweets_controller.rb代码如下

``` ruby
def index
  @trending = Topic.trending.limit(5)
  ...
end
```

我们还可以进一步把limit(5)也添加到scope :trending当中
让topic model变成如下样子

/app/models/topic.rb

``` ruby
class Topic < ActiveRecord::Base
  scope :trending,lambda { |num = nil| where('started_trending > ?', 1.day.ago).
                           order('mentions desc')
                           limit(num)}
  ...

```

我们的tweets_controllers就可以改为如下样子了：

``` ruby
def index
  @trending = Topic.trending(5)
  ...
end
```



