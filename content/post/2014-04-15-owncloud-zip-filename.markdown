---
title: "owncloud打包zip下载文件名乱码"
date: 2014-04-15 21:15:00
categories: ['PHP']
---
最近在单位部署了一套owncloud使用。感觉作为内部网盘使用，效果还是挺好的。
不过今天同事发现，选择多个文件一起下载的时候，会出现乱码问题。
多个文件下载会自动打包成一个zip文件进行下载，下载后的文件在windows机器打开就出现文件名乱码了。

##解决办法##
网站搜索了下解决办法，基本上是因为打包zip的时候，文件名编码处理问题造成的。

打包zip主要调用了/var/www/html/owncloud/lib/private/files.php文件

修改下这个文件，将文件名转为windows可以识别的GBK编码就ok了。

69行附近
``` php
foreach ($files as $file) {
  $file = $dir . '/' . $file;
  if (\OC\Files\Filesystem::is_file($file)) {
    $tmpFile = \OC\Files\Filesystem::toTmpFile($file);
    self::$tmpFiles[] = $tmpFile;
    $u8filename=iconv("UTF-8","GBK",basename($file)); //for utf8
    //$zip->addFile($tmpFile, basename($file));
    $zip->addFile($tmpFile,$u8filename);
  } elseif (\OC\Files\Filesystem::is_dir($file)) {
      self::zipAddDir($file, $zip);
    }
}

```

200行附近
``` php
public static function zipAddDir($dir, $zip, $internalDir='') {
                $dirname=basename($dir);
                $dirname=iconv("UTF-8","GBK",$dirname); //for utf8
                $zip->addEmptyDir($internalDir.$dirname);
                $internalDir.=$dirname.='/';
                $files=OC_Files::getDirectoryContent($dir);
                foreach($files as $file) {
                        $filename=$file['name'];
                        $file=$dir.'/'.$filename;
                        if(\OC\Files\Filesystem::is_file($file)) {
                                $tmpFile=\OC\Files\Filesystem::toTmpFile($file);
                                OC_Files::$tmpFiles[]=$tmpFile;
                                $filename=iconv("UTF-8","GBK",$filename); //for utf8
                                $zip->addFile($tmpFile, $internalDir.$filename);
                        }elseif(\OC\Files\Filesystem::is_dir($file)) {
                                self::zipAddDir($file, $zip, $internalDir);
                        }
                }
        }
```

