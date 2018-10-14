---
title: "rails-best-practices-1"
date: 2013-03-16 09:43:00
categories: ['Rails']
---
#FAT MODEL,SKINNY CONTROLLER#

最近看了codeschool的教程，打算把里面讲解的rails best practices总结下。

视频里的第一部分就是讲解的Fat model，skinny controller。我理解的就是要尽量把业务代码写到model中，而controller要尽量的简单。

第一个例子是tweets_controller.rb的代码

``` ruby
class TweetsController < ApplicationController
  def retweet
    tweet = Tweet.find(params[:id])

    if tweet.user == current_user
      flash[:notice] = "Sorry, you can't retweet your own tweets"
    elsif tweet.retweets.where(:user_id => current_user.id).present?
      flash[:notice] = "You already retweeted!"
    else
      t = Tweet.new
      t.status = "RT #{tweet.user.name}: #{tweet.status}"
      t.original_tweet = tweet
      t.user = current_user
      t.save
      flash[:notice] = "Succesfully retweeted"
    end

    redirect_to tweet
  end
end
```

上面的代码就是所谓的“SAD CODE”，并不理想。最佳实践里好的代码，被codeschool称作“HAPPY CODE”,很有意思。

下面我们看看上面的代码该如何实现“HAYYP CODE”，首先要把一部分代码转移到tweet model里，然后上面的代码变成如下样子
<!--more-->
``` ruby
class TweetsController < ApplicationController
  def retweet
    tweet = Tweet.find(params[:id])
    flash[:notice] = tweet.retweet_by(current_user)
    redirect_to tweet
  end
end
```

可以看到上面tweets_controller.rb明显比之前简单多了，主要是将大部分代码转移到了tweet model的retweet_by方法中了。

下面的/app/models/tweet.rb的部分代码

``` ruby

class Tweet < ActiveRecord::Base
  def retweet_by(retweeter)
    if self.user == retweeter
      "Sorry,you can't retweet your own tweets"
    elsif self.retweets.where(:user_id => retweeter.id).present?
      "You already retweeted!"
    else
      ...
      "Succesfully retweeted"
    end
  end
end
```

##小结##
其实这个fat model,skinny controller很好理解。就是我们要经常检查我们自己的controller，一旦发现过于复杂或者代码过多。就要开始考虑将一些重复的代码或者涉及到复杂的业务逻辑代码提取到model当中实现。上面例子当中就是将转发tweet的业务逻辑代码转移到了model中，在model中判断tweet转发的是不是自己和是否已经转发过了。而最初这些代码放在controller中就造成了controller过于复杂，涉及了业务逻辑。

我们只要把controller定位为action转发，而把model定义为model层和service层。涉及到业务逻辑或者各种条件判断的代码全扔到model中实际就可以了。

