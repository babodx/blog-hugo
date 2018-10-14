---
categories: ['Java']
title: "playframework--Installation guide 安装手册【翻译】"
date: 2009-12-30 14:46:00
---
<h1 id="Installationguide">Installation guide 安装手册</h1>
<h2 id="aPrerequisitesa">
<a>Prerequisites</a> 前提条件</h2>
To run the play framework, you need <a href="http://java.sun.com">Java 5 or later</a>. If you wish to build play from source, you will need the <a href="http://bazaar-vcs.org/">Bazaar source control client</a> to fetch the source code and <a href="http://ant.apache.org/">Ant</a> to build it.
要运行Play框架，你需要Java 5以后的的JDK。如果你想从源码构建play框架，你需要用Bazaar源码控制的客户端去获取源代码并用Ant创建它。
If you are using MacOS, java is just built-in. If you are using linux, make sure to use either the Sun-JDK or OpenJDK (and not gcj which is the default java command on many linux distro). If you are using Windows, just download and install the latest JDK package.
如果你用MacOS系统，java是内建的。如果你用Linux系统，确定你使用的是Sun-JDK或者OpenJDK（不要是gcj，很多linux发行版默认都用gcj作为默认的java环境）。如果你用Windows系统，只要下载并安装最新发行的JDK包。
Be sure to have java in the current path (try to type <b>java -version</b> to check). Play will use the default java or the one available at the <b>$JAVA_HOME</b> path if defined.
确定java在当前路径（可以通过在命令行输入<strong>java -version</strong>检查）。Play框架将调用默认java或者一个在<strong>$JAVA_HOME</strong>环境变量声明的路径里的java。
The play command line utility uses python. So it should work out of the box on any UNIX system. If you’re running Windows, don’t worry, a python runtime is bundled with the framework.
play框架的命令行工具是用python写的。所以它应该可以在任意Unix系统上工作，如果你是Windows系统，别担心，一个python的运行环境已经内建在框架里了。
<h2 id="aDownloadthebinarypackagea">
<a>Download the binary package</a> 下载二进制包</h2>
Download the latest <a href="http://download.playframework.org/">play binary package</a> and extract the archive. For convenience, you should add the framework installation directory to your system PATH. If you’re on UNIX, make sure that the <em>play</em> script is runnable (otherwise simply do a <b>chmod +x play</b>). That’s all.
下载最新的play框架二进制包并解压缩。为了方便，你应该将play框架的安装目录添加到你系统的path环境变量里。如果你用unix，确认play脚本可以运行（否则可以执行<strong>chmod +x play</strong>）<strong>.</strong>
<strong>Tip 建议</strong><br><br>
If you need to rebuild the framework for whatever reason, run <strong>ant</strong> from the <strong>$PLAY_HOME/framework</strong> directory.
如果你需要重建框架，从 <strong>$PLAY_HOME/framework</strong> 目录运行<strong>ant</strong>。
<h2 id="aBuildfromthelatestsourcesa">
<a>Build from the latest sources</a> 从最新源码建立</h2>
To benefit from the latest improvements and bug fixes, you may want to compile play from sources. You’ll need a <a href="http://bazaar-vcs.org/Download">Bazaar client</a> to fetch the sources and <a href="http://ant.apache.org/">Ant</a> to build the framework.
为了最新的改善和bug修复，你也许想从源代码编译play框架。你将需要一个Bazaar客户端去获取源代码并用Ant建立play框架。
From the command line:
通过下面的命令行建立
<pre><code># bzr checkout lp:play
# cd play/framework
# ant
</code></pre>
The Play framework is ready to use.
lp:play currently aliases to http:bazaar.launchpad.net/%7Eplay-developers/play/1.0/
<h2 id="aUsingtheplaycommanda">
<a>Using the play command</a> 是用play命令</h2>
When the framework is correctly installed, open a shell and execute <strong>play</strong>.
当框架正确安装后，打开一个shell并执行<strong>play</strong>
<pre><code>$ play
</code></pre>
You should see the play default message:
你应该看到play的默认信息
<img border="0" alt="" src="http://www.playframework.org/documentation/1.0/images/help">
You can get more help on any specific command using <strong>play help any-command-name-here</strong>. For example, try:
你可以获得更多帮助在任何具体命令上用 <strong>play help any-command-name-here例如，尝试</strong>
<pre><code># play help run
</code></pre>
<h2 id="aCreatinganewapplicationa">
<a>Creating a new application</a> 创建一个新的应用</h2>
Use the <b>new</b> command to create a new application. You must give a non-existent path where the application will be created.
用<strong>new</strong>命令创建一个新的应用。你必须给一个不存在的名字，才可以创建应用（就是给一个以前没有的项目名字）
<pre><code># play new myApp
</code></pre>
<img border="0" alt="" src="http://www.playframework.org/documentation/1.0/images/guide1-1">
Will create a new application. Start it with:
创建一个新的应用，用下面命令
<pre><code># play run myApp
</code></pre>
You can then open a browser at <a href="http://localhost:9000">http://localhost:9000</a> and see the default page for the application.
你可以用浏览器访问 <a href="http://localhost:9000">http://localhost:9000</a>并看到应用的默认页面。
<img border="0" alt="" src="http://www.playframework.org/documentation/1.0/images/guide1-2">
<b>Your play environnement is ready</b><br>
 
<b>你的play框架已经准备好了</b>
<b>关于如何使用play框架，可以参考视频</b>
<b><a href="http://www.xinlogs.com/playframework-video/">http://www.xinlogs.com/playframework-video/</a></b><div id="related_log" style="font-size:12px">
<b>相关日志：</b><a href="http://xinlogs.com/playframework">playframework--play！java on rails框架</a><a href="http://xinlogs.com/playframework-video">playframework--开发视频</a><a href="http://xinlogs.com/playframework-helloworld">playframework--helloworld程序编写</a>
</div>
