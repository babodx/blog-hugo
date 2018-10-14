---
categories: ['Database']
title: 'Linux源码安装MySQL多版本共存'
date: 2011-11-25 18:48:00
---

原来我的服务器上是MySQL 5.5.16。本来是用的挺稳定。但是当时编译的时候由于手误将DDEFAULT_COLLATION=utf8-general_ci \弄错啦。。。应该是utf8_general_ci。导致很多程序用的时候报错，或者需要修改。
最近发现MySQL更新到了5.5.18了，就打算迁移到新数据库正好修正原来问题。
为了不影响访问，就打算在5.5.16运行的情况下，继续安装好5.5.18.等顺利转移到5.5.18后，再关闭原来MySQL5.5.16版本。
也就有了今天这篇文章。在Linux下从源码安装多版本或者多个MySQL数据库。

**重点：多版本或者多个MySQL同时运行，主要就是多个MySQL需要运行在不同目录，不同的端口，采用不同的数据库路径。**

**步骤**

**1、首先是下载需要安装的MySQL**

下载mysql 5.5.16

```
wget http://dev.mysql.com/get/Downloads/MySQL-5.5/mysql-5.5.16.tar.gz/from/http://mysql.mirrors.pair.com/
```

**下载mysql 5.5.18**

```
wget http://dev.mysql.com/get/Downloads/MySQL-5.5/mysql-5.5.16.tar.gz/from/http://mysql.mirrors.pair.com/
```

**2、安装支持程序**

原来版本的MySQL源码安装都是采用./configure和make这些。但是新版本的都是用cmake来弄了。所以如果系统中没有的，需要先安装cmake

```
yum install cmake
```

**3、创建MySQL用户和用户组**

```
/usr/sbin/groupadd mysql

/usr/sbin/useradd -g mysql mysql
```

**4、解压、编译、安装MySQL**

**解压**
``
tar zxvf mysql-5.5.16.tar.gz
cd mysql-5.5.16
``

**编译参数**

```
cmake . \
  -DCMAKE_INSTALL_PREFIX=/usr/local/webserver/mysql5516/ \
  -DMYSQL_DATADIR=/data0/mysql/3306/data \
  -DMYSQL_UNIX_ADDR=/data0/mysql/3306/mysqld.sock \
  -DWITH_MYISAM_STORAGE_ENGINE=1 \
  -DWITH_INNOBASE_STORAGE_ENGINE=1 \
  -DWITH_BLACKHOLE_STORAGE_ENGINE=0 \
  -DWITH_ARCHIVE_STORAGE_ENGINE=0 \
  -DWITHOUT_PARTITION_STORAGE_ENGINE=1 \
  -DENABLED_LOCAL_INFILE=1 \
  -DMYSQL_TCP_PORT=3306 \
  -DEXTRA_CHARSETS=all \
  -DDEFAULT_CHARSET=utf8 \
  -DDEFAULT_COLLATION=utf8_general_ci \
  -DWITH_DEBUG=0 \
  -DWITH_EMBEDDED_SERVER=1 \
  -DWITH_SSL=system \
  -DWITH_READLINE=1
```

**注意：编译参数里有几项需要根据自己具体情况设置。我这里将MySQL5.5.16运行于3306端口，并安装在mysql5516目录。**

```
-DCMAKE_INSTALL_PREFIX=/usr/local/webserver/mysql5516/   设置mysql的安装目录

-DMYSQL_DATADIR=/data0/mysql/3306/data 设置mysql数据库文件的位置

-DMYSQL_UNIX_ADDR=/data0/mysql/3306/mysqld.sock 设置mysql启动后sock文件位置

-DMYSQL_TCP_PORT=3306 设置mysql的监听端口
```

以上这些设置需要根据不同版本的mysql相应修改。不然多个版本同时监听3306端口，肯定冲突了。

**编译**

`make`

**安装**

`make install`

**5、配置目录权限、创建目录、建立初始数据。**

**配置目录权限**

```
chmod +w /usr/local/webserver/mysql5516

chown -R mysql:mysql /usr/local/webserver/mysql5516

cd ../
```

**创建相关目录**

```
mkdir -p /data0/mysql/3306/data/
mkdir -p /data0/mysql/3306/binlog/
mkdir -p /data0/mysql/3306/relaylog/
chown -R mysql:mysql /data0/mysql/
```

**建立初始数据**

```
/usr/local/webserver/mysql5516/scripts/mysql_install_db --basedir=/usr/local/webserver/mysql --datadir=/data0/mysql/3306/data --user=mysql
```

**6、创建My.cnf配置文件和Mysql启动脚本**

创建my.cnf配置文件

```
cp support-files/my-small.cnf /usr/local/webserver/mysql5516/my.cnf
```

**注意：原来mysql默认的配置文件放在/etc/my.cnf。现在多个版本同时运行，需要每个版本都将配置文件单独放在自己的安装目录内。**

**创建MySQL启动脚本**

```
cp support-files/mysql.server /etc/init.d/mysql5516
```

**注意：原来mysql默认采用mysqld脚本，现在每个mysql需要有自己的启动脚本，并通过修改启动脚本来为每个mysql指定采用的配置文件。**

修改/etc/init.d/mysql5516，用来指定采用的配置文件位置。

```
# Try to find basedir in /etc/my.cnf 
1	 conf=/etc/my.cnf
```

在文件的213和214行内容如上，我们需要修改conf=/etc/my.cnf为我们指定的my.cnf文件位置。

在我们这里是

```
conf=/usr/local/webserver/mysql5516/my.cnf
```

修改mysqld5516权限为可执行

```
chmod +x mysqld5516
```

**7、启动mysql并配置为开机运行**

启动

```
service mysqld5516 start
```

开机运行

```
chkconfig mysqld5516 on
```

以上是MySQL 5.5.16的安装。下面5.5.18版本的安装 

5.5.18版本的安装基本和MySQL 5.5.16版本一样。我们因为要两个数据库同时运行。所以再给cmake的参数时候，需要安装上面注意的内容设定5.5.18的安装目录为/usr/local/webserver/mysql5518 再调整运行端口为3307
具体的cmake参数如下

```
cmake . \
  -DCMAKE_INSTALL_PREFIX=/usr/local/webserver/mysql5518/ \
  -DMYSQL_DATADIR=/data0/mysql/3307/data \
  -DMYSQL_UNIX_ADDR=/data0/mysql/3307/mysqld.sock \
  -DWITH_MYISAM_STORAGE_ENGINE=1 \
  -DWITH_INNOBASE_STORAGE_ENGINE=1 \
  -DWITH_BLACKHOLE_STORAGE_ENGINE=0 \
  -DWITH_ARCHIVE_STORAGE_ENGINE=0 \
  -DWITHOUT_PARTITION_STORAGE_ENGINE=1 \
  -DENABLED_LOCAL_INFILE=1 \
  -DMYSQL_TCP_PORT=3307 \
  -DEXTRA_CHARSETS=all \
  -DDEFAULT_CHARSET=utf8 \
  -DDEFAULT_COLLATION=utf8_general_ci \
  -DWITH_DEBUG=0 \
  -DWITH_EMBEDDED_SERVER=1 \
  -DWITH_SSL=system \

```

还需要为mysql5518配置相应权限和建立3307端口的相关目录。因为上面已经有具体步骤，这里只是罗列下命令，不具体说明

```
chmod +w /usr/local/webserver/mysql5518

chown -R mysql:mysql /usr/local/webserver/mysql5518

cd ../

mkdir -p /data0/mysql/3307/data/
mkdir -p /data0/mysql/3307/binlog/
mkdir -p /data0/mysql/3307/relaylog/
chown -R mysql:mysql /data0/mysql/
```

最后的启动脚本也要修改为调用5518目录下的my.cnf配置。 而且my.cnf的[mysqld]段也要指定运行端口为3307
下面是我mysql5518目录下的my.cnf中的一些需要修改的项
注意：下面文件并不是完整的my.cnf，只是将需要修改的列出来了

```
[client]
port    = 3307
socket  = /data0/mysql/3307/mysql.sock

[mysqld]
port    = 3307
socket  = /data0/mysql/3307/mysql.sock
basedir = /usr/local/webserver/mysql5518
datadir = /data0/mysql/3307/data
log-error = /data0/mysql/3307/mysql_error.log
pid-file = /data0/mysql/3307/mysql.pid
log-bin = /data0/mysql/3307/binlog/binlog
relay-log-index = /data0/mysql/3307/relaylog/relaylog
relay-log-info-file = /data0/mysql/3307/relaylog/relaylog
relay-log = /data0/mysql/3307/relaylog/relaylog
```

这样弄完后，我服务器上就可以同时运行mysql5516与mysql5518两个版本的数据库了。

启动、关闭、重启mysql 5.5.16全部采用service mysqld5516脚本来控制。

启动、关闭、重启mysql 5.5.18全部采用service mysqld5518脚本来控制。


**总结：同时运行多个版本数据库，可以方便我们升级数据库或者对不同版本进行测试。**

