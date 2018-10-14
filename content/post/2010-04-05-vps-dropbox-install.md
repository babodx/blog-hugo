---
categories: ['Linux']
title: '一款Linux下同步备份文件的好软件DROPBOX'
date: 2010-04-05 16:03:00
---
原来一直用tar、sync等软件实现备份。需要些脚本还要找一台其他服务器进行同步。现在有了DROPBOX这个网站提供的服务，一切都变容易了。

**DROPBOX网站：**
[http://www.dropbox.com/](www.dropbox.com)
**DROP功能：**
偷懒直接把官方介绍帖过来了。主要就是免费提供2G的同步空间，可以自动同步文件并保留文件的历史版本

<h1>Dropbox Features</h1>
<h2 id="sync">File Sync</h2>
Dropbox allows you to sync your files online and across your computers automatically.
<ul class="blue-bullets">
<li>2GB of online storage for free, with up to 100GB available to paying customers.</li>
    <li>Sync files of any size or type.</li>
    <li>Sync Windows, Mac and Linux computers.</li>
    <li>Automatically syncs when new files or changes are detected.</li>
    <li>Work on files in your Dropbox even if you're offline. Your changes sync once your computer has an Internet connection again.</li>
    <li>Dropbox transfers will correctly resume where they left off if the connection drops.</li>
    <li>Efficient sync - only the pieces of a file that changed (not the whole file) are synced. This saves you time.</li>
    <li>Doesn't hog your Internet connection. You can manually set bandwidth limits. </li>
</ul>
<h2 id="sharing">File Sharing</h2>
Sharing files is simple and can be done with only a few clicks.
<ul class="blue-bullets">
<li>Shared folders allow several people to collaborate on a set of files.</li>
    <li>You can see other people's changes instantly.</li>
    <li>A "Public" folder that lets you link directly to files in your Dropbox.</li>
    <li>Control who is able to access shared folders (including ability to kick people out and remove the shared files from their computers).</li>
    <li>Automatically create shareable online photo galleries from folders of photos in your Dropbox.</li>
</ul>
<h2 id="backup">Online Backup</h2>
Dropbox backs up your files online without you having to think about it.
<ul class="blue-bullets">
<li>Automatic backup of your files.</li>
    <li>Undelete files and folders.</li>
    <li>Restore previous versions of your files.</li>
    <li>30 days of undo history, with unlimited undo available as a paid option.</li>
</ul>
<h2 id="web">Web Access</h2>
A copy of your files are stored on Dropbox's secure servers. This lets you access them from any computer or mobile device.
<ul class="blue-bullets">
<li>Manipulate files as you would on your desktop - add, edit, delete, rename etc.</li>
    <li>Search your entire Dropbox for files.</li>
    <li>A "Recent Events" feed that shows you a summary of activity in your Dropbox.</li>
    <li>Create shared folders and invite people to them.</li>
    <li>Recover previous versions of any file or undelete deleted files.</li>
    <li>View photo galleries created automatically from photos in your Dropbox.</li>
</ul>
<h2 id="security">Security & Privacy</h2>
Dropbox takes the security and privacy of your files very seriously.
<ul class="blue-bullets">
<li>Shared folders are viewable only by people you invite.</li>
    <li>All transmission of file data and metadata occurs over an encrypted channel (SSL).</li>
    <li>All files stored on Dropbox servers are encrypted (AES-256) and are inaccessible without your account password.</li>
    <li>Dropbox website and client software have been hardened against attacks from hackers.</li>
    <li>Dropbox employees are not able to view any user's files.</li>
    <li>Online access to your files requires your username and password.</li>
    <li>Public files are only viewable by people who have a link to the file(s). Public folders are not browsable or searchable.</li>
</ul>
<h2 id="mobile">Mobile Device Access</h2>
The free <a href="http://www.xinlogs.com/iphoneapp">Dropbox iPhone app</a> lets you:
<ul class="blue-bullets">
<li>Access your Dropbox on the go.</li>
    <li>View your files on your iPhone or iPod Touch.</li>
    <li>Download files for offline viewing.</li>
    <li>Take photos and videos and sync them to your Dropbox.</li>
    <li>Share links to files in your Dropbox.</li>
    <li>View interactive photo galleries.</li>
    <li>Sync downloaded files so they're up-to-date.</li>
</ul> 

A mobile-optimized version of the website is available for owners of Blackberry phones and other Internet-capable mobile devices.
 
**Linux下安装**

如果你的Linux服务器安装了Xserver和桌面，完全可以安装官方的指南来安装。但是因为一般的VPS都内存小得可怜，不会奢侈到安装桌面，所以下面主要介绍在没有安装桌面系统的命令行Linux下安装步骤。
关于安装方法的内容转帖自<a href="http://lazyhack.net/install-dropbox-in-vps/">http://lazyhack.net/install-dropbox-in-vps/</a>
<strong>1、登陆进VPS或者是服务器，进入用户目录</strong>
<div class="codeText">
<div id="code_6795">
<ol class="dp-xml" style="border-bottom:0px;border-left:0px;list-style-type:none;margin-left:5px;border-top:0px;border-right:0px;">
<li class="alt">
<span><span>cd </span></span>     <link rel="stylesheet" type="text/css" href="http://www.xinlogs.com/editor/fckeditor/editor/plugins/insertcode/insertcode.css">
<script language="javascript" src="http://www.xinlogs.com/editor/fckeditor/editor/plugins/insertcode/excute.js" type="text/javascript"></script>
</li>
</ol>
</div>
</div>
<strong>2、下载dropbox的客户端，要For linux那个而非For Nautilus的</strong>
这里我是在<a href="http://lazyhack.net/install-dropbox-in-vps/">http://lazyhack.net/install-dropbox-in-vps/</a>文档中找到的地址，官方网站找了半天都是Nautilus的版本
32位下载地址 <a href="http://www.dropbox.com/download?plat=lnx.x86">http://www.dropbox.com/download?plat=lnx.x86</a>
64位下载地址 <a href="http://www.dropbox.com/download?plat=lnx.x86_64">http://www.dropbox.com/download?plat=lnx.x86_64</a>
我这里用的32位系统，所以下载32位客户端
<div class="codeText">
<div id="code_2958">
<ol class="dp-xml" style="border-bottom:0px;border-left:0px;list-style-type:none;margin-left:5px;border-top:0px;border-right:0px;">
<li class="alt">
<span><span>wget http://www.dropbox.com/download?</span><span class="attribute">plat</span><span>=</span><span class="attribute-value">lnx</span><span>.x86 </span></span>     <link rel="stylesheet" type="text/css" href="http://www.xinlogs.com/editor/fckeditor/editor/plugins/insertcode/insertcode.css">
<script language="javascript" src="http://www.xinlogs.com/editor/fckeditor/editor/plugins/insertcode/excute.js" type="text/javascript"></script>
</li>
</ol>
</div>
</div>
<strong>3、解压</strong>
<div class="codeText">
<div id="code_2090">
<ol class="dp-xml" style="border-bottom:0px;border-left:0px;list-style-type:none;margin-left:5px;border-top:0px;border-right:0px;">
<li class="alt"><span><span>tar zxvf dropbox-lnx.x86-0.7.110.tar.gz </span></span></li>
</ol>
</div>
<link rel="stylesheet" type="text/css" href="http://www.xinlogs.com/editor/fckeditor/editor/plugins/insertcode/insertcode.css">
<script language="javascript" src="http://www.xinlogs.com/editor/fckeditor/editor/plugins/insertcode/excute.js" type="text/javascript"></script>
</div>
<strong>4、下载dbmakefilelib.py并运行</strong>
<div class="codeText">
<div id="code_3236">
<ol class="dp-xml" style="border-bottom:0px;border-left:0px;list-style-type:none;margin-left:5px;border-top:0px;border-right:0px;">
<li class="alt"><span><span>wget http://dl.dropbox.com/u/637552/Dropbox/dbmakefilelib.py </span></span></li>
    <li><span>python dbmakefilelib.py </span></li>
</ol>
</div>
<link rel="stylesheet" type="text/css" href="http://www.xinlogs.com/editor/fckeditor/editor/plugins/insertcode/insertcode.css">
<script language="javascript" src="http://www.xinlogs.com/editor/fckeditor/editor/plugins/insertcode/excute.js" type="text/javascript"></script>
</div>
如果出现以下信息，说明它运行正常
dropboxd ran for 15 seconds without quitting - success?
看到它的提示了吗，<a target="_blank" href="http://www.vpser.net/go/dropbox">Dropbox</a>已经启动了，让你运行15秒后再退出，这个过程就是dropbox生成机器信息并保存到db文件的，其实对于国外的<a title="查看 vps 的全部文章" target="_blank" href="http://lazyhack.net/tag/vps/">vps</a>来说几秒时间就够了，我们ctrl－c将其退出，完成接下来的步骤<br>
 
<strong>5、进入</strong><a target="_blank" href="http://www.vpser.net/go/dropbox"><strong>Dropbox</strong></a><strong>的dot目录导出机器信息</strong>
<div class="codeText">
<div id="code_7591">
<ol class="dp-xml" style="border-bottom:0px;border-left:0px;list-style-type:none;margin-left:5px;border-top:0px;border-right:0px;">
<li class="alt"><span><span>cd .~/.dropbox </span></span></li>
    <li><span>$ sqlite3 dropbox.db </span></li>
    <li class="alt"> </li>
    <li><span>SQLite version 3.6.22 </span></li>
    <li class="alt"><span>Enter ".help" for instructions </span></li>
    <li><span>Enter SQL statements terminated with a ";" </span></li>
    <li class="alt">
<span>sqlite</span><span class="tag">></span><span> .dump config </span>
</li>
</ol>
</div>
<link rel="stylesheet" type="text/css" href="http://www.xinlogs.com/editor/fckeditor/editor/plugins/insertcode/insertcode.css">
<script language="javascript" src="http://www.xinlogs.com/editor/fckeditor/editor/plugins/insertcode/excute.js" type="text/javascript"></script>
</div>
我们会在导出的信息中看到如下的一串字符
<div>
<div>
<pre>INSERT INTO "config" VALUES(4,'host_id','VmQ0NWFlMTdmYmQ3OGYzMzgyOTM0NWMzN2Q1MGFkOTIzCnAxCi4=
');</pre> </div>
</div>
这个host_id就是机器的唯一标识，我们需要用它来跟自己的账户进行匹配，不过在这之前还有个工作需要做，因为上面的那长串字符很明显可以看出 是用base64编码后的结果，我们得先把它解码再说，注意这里<a target="_blank" href="http://www.vpser.net/go/dropbox">Dropbox</a>耍了个小花招，这串字符串前面的那个”V“，是无用的，我们在解码后的把它 忽略掉<br>
 
<strong>6、将base64字符串解码</strong> 
 

<strong> </strong>
<div class="codeText"><strong> <span><span>$ echo </span><span class="attribute">mQ0NWFlMTdmYmQ3OGYzMzgyOTM0NWMzN2Q1MGFkOTIzCnAxCi4</span><span>= ｜ base64 -d </span></span>
<div>
<ol class="dp-xml" style="border-bottom:0px;border-left:0px;list-style-type:none;margin-left:5px;border-top:0px;border-right:0px;">
<li><span>Vd45ae17fbd78f33829345c37d50ad923 </span></li>
    <li>
<span>p1 </span>     <link rel="stylesheet" type="text/css" href="http://www.xinlogs.com/editor/fckeditor/editor/plugins/insertcode/insertcode.css">
<script language="javascript" src="http://www.xinlogs.com/editor/fckeditor/editor/plugins/insertcode/excute.js" type="text/javascript"></script>
</li>
</ol>
</div>
</strong></div>
<strong> <pre>我的结果是Vd45ae17fbd78f33829345c37d50ad923，忽略掉”V“，就是d45ae17fbd78f33829345c37d50ad923</pre> </strong>
 
 
 
 
<strong>7、将账户信息与机器信息绑定</strong><br>
这步很简单，只需要访问以下网址，并登陆<br>
HOSTID替换成你刚才解码出来的那串字符就行了
<pre><code>https://www.dropbox.com/cli_link?host_id=HOSTID</code></pre> <strong>8、建立dropbox的同步目录</strong><br>
dropbox的同步目录默认的是~/Dropbox
<pre><code>$ mkdir ~/Dropbox</code></pre> <strong>9、运行，开始你的同步</strong>
<code>$ ~/.dropbox-dist/dropboxd &</code>
到这里dropbox就可以正常运行并同步了，文章标题说的是备份网站数据，那么我们就来使我们的网站数据能够同步到dropbox服务器上<br>
其实很简单，就是建立符号连接而已，windows之前是没有这个功能的<br>
比如要备份/var/www这个目录
<pre><code>cd ~/Dropbox
$ ln -s /var/www web_backup</code></pre> 这就可以了,或者要备份/e<a title="tc" target="_blank" href="http://wiki.lazyhack.net/tc">tc</a>目录
<pre><code>$ cd ~/Dropbox
$ ln -s /etc etc_backup</code></pre> ok.发挥你的想象尽情的使用吧。<br>
 
<strong>推广</strong>
如果你看过这篇文章后，打算也使用dropbox。那么你可以使用我下面的链接注册
<a href="https://www.dropbox.com/referrals/NTU4NDMyNzI5">https://www.dropbox.com/referrals/NTU4NDMyNzI5</a>
采用这个链接注册，你会增加250M空间。注册后是2G，然后会给你再发封邮件告诉你已经提升到2.25G。而我也会增加250M空间。<div id="related_log" style="font-size:12px">
<b>相关日志：</b><a href="http://xinlogs.com/post/20">ubuntu下arp攻击防御和反击！</a><a href="http://xinlogs.com/post/16">一个很好的开源企业内部IM解决方案</a><a href="http://xinlogs.com/post/108">CentOS下的Tomcat停止脚本</a><a href="http://xinlogs.com/Tomcat-reboot-problems">解决Linux下Tomcat不能重启和停止问题</a><a href="http://xinlogs.com/php-eaccelerator-vs-fastcgi-cache">php eaccelerator vs fastcgi_cache性能比较</a>
</div>
