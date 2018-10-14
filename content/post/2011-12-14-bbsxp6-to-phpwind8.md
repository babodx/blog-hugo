---
categories: ['PHP']
title: "bbsxp 6.0数据导入phpwind 8.7方法"
date: 2011-12-14 23:12:00
---
今天帮助猪八戒网上的一个客户将原来的bbsxp 6.0的论坛数据迁移到phpwind 8.7下。这里主要记录下思路

phpwind官方论坛有各种的迁移程序，但是继续bbsxp的最早版本是2008. 而我这个客户的是bbsxp 6的access库。

完成这个任务，我顺利升级到猪三戒！呵呵

没有现成的，只能自己分析了。

为了导入方便，我先将需要用到的数据从access的库导入mysql里。

这样bbsxp的表和新安装的phpwind表都在一个库了。

**数据分析**

这个步骤主要分析用户数据、论坛的板块数据、帖子、回帖等数据的格式。找到两个表的数据关系，为后面导入数据打下基础。

**数据迁移**

用户数据

```
INSERT INTO pw_members(uid,username,password,email,icon,gender,regdate,signature,site,bday) SELECT ID,UserName,lower(UserPass),UserMail,UserFace,UserSex,UNIX_TIMESTAMP(UserRegTime),UserSign,UserHome,Birthday From bbsxp_users;
```
```
INSERT INTO pw_memberdata(uid,postnum,rvrc,money) SELECT ID,PostTopic,PostRevert,UserMoney From bbsxp_users;
```

帖子数据

```
INSERT INTO pw_threads(tid,fid,author,subject,ifcheck,postdate,lastpost,lastposter,hits,replies) SELECT ID,ForumID,UserName,Topic,1,UNIX_TIMESTAMP(PostTime),UNIX_TIMESTAMP(LastTime),LastName,Views,Replies From bbsxp_threads;
```

```
INSERT INTO pw_tmsgs(tid,content) SELECT ThreadID,Content From bbsxp_posts2 where IsTopic=1;
```

```
update pw_threads set authorid = (SELECT uid from pw_members WHERE username=pw_threads.author);
```

回帖数据

```
INSERT INTO pw_posts(pid,tid,author,postdate,subject,ifcheck,content) SELECT ID,ThreadID,UserName,UNIX_TIMESTAMP(PostTime),Subject,1,Content From bbsxp_posts2 where IsTopic=0;
```

```
update pw_posts set authorid = (SELECT uid from pw_members WHERE username=pw_posts.author);
```

**最后注意：**

导入数据后，论坛进入板块后没有分页。这个需要进入后台，数据-》全部缓存-》更新各种统计数据。

还有就是需要看下bbsxp做没做分表处理，有可能将bbsxp_posts分表为bbsxp_posts1、bbsxp_posts2等表
