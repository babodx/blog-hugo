---
categories: ['Linux']
title: 'postfix+extmail的附件大小显示和限制问题解决办法'
date: 2010-01-24 21:06:00
---

最近在给一个公司部署了postfix+extmail的邮件系统后，遇到了附件大小限制问题。

extmail默认设置是5M，但是由于extmail的统计大小方法问题，一般附件限制比设置的小了1/3。也就是你设置了5M，也就能到3M多。

这里先说下大小限制的配置。

一、修改extmail的webmail.cf文件

```
SYS_MESSAGE_SIZE_LIMIT = 5242880 注意：以位为单位为5M字节。 

SYS_MESSAGE_SIZE_LIMIT = xxx
```

二、修改/etc/postfix/main.cf文件，

```
message_size_limit = xxx
```

三、重启postfix即可，`service postfix restrart` 和重启apache，`service httpd restart`

**如何解决附件限制大小和系统文件大小相差1/3呢？**

首先是修改/var/www/extsuite/extmail/libs/Ext/App目录下的Compose.pm文件

```
sub is_oversize {   
    my $self = shift;   
    my $sys = $self->{sysconfig};   
    my $maxsize = $sys->{SYS_MESSAGE_SIZE_LIMIT};   
    my $tsize = shift;   
  
    return 0 unless defined $maxsize and $maxsize > 0;   
    return 0 unless defined $tsize and $tsize > 0;   
  
    if ($tsize >= $maxsize) {   
        return $maxsize;   
    }   
    0;   
}   
```

注意到上面的if ($tsize >= $maxsize)了吧，修改为if ($tsize >= 1.29*$maxsize)

这样修改后，基本extmail的附件限制大小就和系统显示的文件大小一样。

目前还有一个文件，就是我们上传一个1M的内容，显示出来的附件大小大约是1.3M，这个问题需要修改一个human函数解决。

在/var/www/extsuite/extmail/libs/Ext目录下，打开Utils.pm文件

找到如下函数

```
sub human_size {   
    my $s = $_[0];   
    if($s<1024) {   
        return sprintf("%0.2f", $s/1024)."K";   
    }elsif($s<1024*1024) {   
        return sprintf("%0.1f", $s/1024)."K";   
    }else {   
        return sprintf("%0.1f", $s/(1024*1024))."M";   
    }   
}   
```
将函数修改如下

```
sub human_size {   
    my $s = $_[0];   
    if($s<1024) {   
        return sprintf("%0.2f", $s/1024/1.36)."K";   
    }elsif($s<1024*1024) {   
        return sprintf("%0.1f", $s/1024/1.36)."K";   
    }else {   
        return sprintf("%0.1f", $s/(1024*1024*1.36))."M";   
    }   
}   
```

这样就解决了extmail的附件大小显示和限制问题。

目前还有一个遗留问题，就是在folder.htm模板界面里显示的邮件大小还是比系统文件大1/3。这个不知道怎么修改了。找到了对应函数，但是它直接显示的多少M或者多少K，用除1.36会出现问题。。。。这个期待高手解决了。


