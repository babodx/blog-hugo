---
categories: ['PHP']
title: '修改EMiDream模板'
date: 2011-10-26 19:40:00
---
今天修改了EMiDream模板。这个模板是给wordpress制作的，应用在<a href="http://www.templatesnext.org/">http://www.templatesnext.org/</a>网站
我是在emlog论坛找到了“通信民工”修改的过来的版本。
我所做的修改如下：
1、首页每条博客下面变为了中文的 评论 和 浏览
这个修改主要是在log_list.php文件中，将28、29行改为如下内容


``` 
    <span class="postcomments"><a href="<?php echo $value['log_url']; ?>#comments" title="查询评论">评论：<?php echo $value['comnum']; ?></a></span>
    <span class="postmore">浏览：<?php echo $value['views']; ?></span>
```

 2、友情链接只在首页的时候显示，这个有利于SEO。
主要是通过修改module.php实现。找到文件中关于友情链接段代码，然后在代码前后加入判断是否为首页的代码。
具体代码如下：


``` 
<?php
  //判断是否是首页，不是首页，不显示友情链接
  if("http://".$_SERVER['HTTP_HOST'].$_SERVER['REQUEST_URI'] == BLOG_URL):?>
<?php
//widget：链接
function widget_link($title){
	global $CACHE; 
	$link_cache = $CACHE->readCache('link');
	?>
	<div class="widget-cat">
	<h4><?php echo $title; ?></h4>
	<ul id="link">
	<?php foreach($link_cache as $value): ?>
	<li><a href="<?php echo $value['url']; ?>" title="<?php echo $value['des']; ?>" target="_blank"><?php echo $value['link']; ?></a></li>
	<?php endforeach; ?>
	</ul>
	</div>
<?php }?>
<?php endif;?>
```

  
3、修改了下module.php的blog_comments函数。让这个函数和default模板保持一致。<br>
具体代码如下：


``` 
<?php
//blog：博客评论列表
      function blog_comments($comments){
        //var_dump($comments);
    extract($comments);
    if($commentStacks): ?>
	<a name="comments"></a>
	<p class="comment-header"><b>评论：</b></p>
	<?php endif; ?>
	<?php
	$isGravatar = Option::get('isgravatar');
	foreach($commentStacks as $cid):
    $comment = $comments[$cid];
	$comment['poster'] = $comment['url'] ? '<a href="'.$comment['url'].'" target="_blank" rel="nofollow">'.$comment['poster'].'</a>' : $comment['poster'];
	?>
	<div class="comment" id="comment-<?php echo $comment['cid']; ?>">
		<a name="<?php echo $comment['cid']; ?>"></a>
		<?php if($isGravatar == 'y'): ?><div class="avatar"><img src="<?php echo getGravatar($comment['mail']); ?>" /></div><?php endif; ?>
		<div class="comment-info">
			<b><?php echo $comment['poster']; ?> </b><br /><span class="comment-time"><?php echo $comment['date']; ?></span>
			<div class="comment-content"><?php echo $comment['content']; ?></div>
			<div class="comment-reply"><a href="#comment-<?php echo $comment['cid']; ?>" onclick="commentReply(<?php echo $comment['cid']; ?>,this)">回复</a></div>
		</div>
		<?php blog_comments_children($comments, $comment['children']); ?>
	</div>
	<?php endforeach; ?>
    <div id="pagenavi">
	    <?php echo $commentPageUrl;?>
    </div>
<?php }?>
```

  
4、修复了置顶博客，不能正常显示“小星星”图标问题。
主要是将images目录下，放入import.gif文件
5、将评论里的对外链接加入了nofollow属性。这样有利于SEO，而且也不用担心垃圾评论来混外链了。
主要是修改module.php文件中blog_comments函数里面关于评论作者的那句。
具体代码如下


``` 
$comment['poster'] = $comment['url'] ? '<a href="'.$comment['url'].'" target="_blank" rel="nofollow">'.$comment['poster'].'</a>' : $comment['poster'];
```

