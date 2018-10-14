---
categories: ['PHP']
title: '批量更换115网盘链接'
date: 2012-08-10 14:31:00
---
今天115网盘关闭了对外分享链接的功能。我的客户有人是用115网盘作为服务器资源存储用的，网站上下载资源都是采用115网盘地址来发布的。这样直接造成全部资源都无法下载了，我临时写了个php脚本来更新全部的115网盘链接到本地服务器地址。
 
**主要思路：**

首先将网盘里的资源全部下载到服务器，然后导出一个网盘地址和文件实际名字的列表

如下所示：

```
http://115.com/file/e78cpfjd#Dark_Room.rar
http://115.com/file/e78cpwkh#Dark_Energy.rar
```

可以看到前面是网盘下载的url，＃号后面是实际的文件名。

我将这个列表保存在一个文件里，便于php脚本获取。

然后编写一个php脚本，把文件里每行的url取出来，到数据库里把这个下载链接替换为我们服务器上的地址。主要是采用mysql的replace函数实现
 
脚本如下：

``` php 
#!/usr/bin/php
<?php
print("=======fix_url v1=========\n");
print("=======write by babodx@gmail.com=======\n");


function update_url($url) {
    list($url_115,$url_tv1926)=explode("#",$url);
    $url_tv1926="http://xiazai.tv1926.com/".$url_tv1926;
    print("update:");
    print($url_115);
    print(">>");
    $link = mysql_connect('localhost', 'username', 'passwd');
    if (!$link) {
        die('Could not connect: ' . mysql_error());
    }

    mysql_select_db('my_database', $link) or die ('Can\'t use my_database : ' .         mysql_error());

    $sql="update p8_article_content_101 set     my_801=replace(my_801,'".$url_115."','".$url_tv1926."')";
    mysql_query("set names gbk");
    $result = mysql_query($sql)
    or die("Invalid query: " . mysql_error());

    mysql_close($link);
    print($url_tv1926);
    print("\n");
}

if (is_null($argv[1])) {
    print("argv1 is null\n");
    exit();
}
$file_name=$argv[1];
print("get files name...\n");
$file_names=array();
$handle = @fopen($file_name, "r");
if ($handle) {
    while (!feof($handle)) {
        $buffer = fgets($handle);
        $tmp_file_name=trim($buffer);
        array_push($file_names,$tmp_file_name);

    }
    fclose($handle);
}

foreach ($file_names as $file_url) {
    update_url($file_url);
}
print("over!");



?>
```
