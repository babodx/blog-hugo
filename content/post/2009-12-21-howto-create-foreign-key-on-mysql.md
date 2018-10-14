---
categories: ['Database']
title: '如何建立两个表间的外键'
date: 2009-12-21 18:00:00
---

建立Mysql两个表间的外键,需要满足三个条件

1. 两个表必须是InnoDB表类型
2. 使用在外键关系的域必须为索引型(Index)
3. 使用在外键关系的域必须与数据类型相似

下面举例说明

我们创建一个班级表和一个学生表,并建立一个外键

创建班级表

```
CREATE TABLE class (id INT NOT NULL AUTO_INCREMENT, classname VARCHAR(50) NOT NULL, PRIMARY KEY(id)) ENGINE=INNODB;
```

插入班级数据

```
INSERT INTO class VALUES (1,"一班"),(2,"二班"),(3,"三班");
```

创建学生表

```
CREATE TABLE students (id INT(4) NOT NULL, name VARCHAR(50) NOT NULL, FK_class INT(4) NOT NULL, INDEX (FK_class), FOREIGN KEY (FK_class) REFERENCES class (id), PRIMARY KEY(id)) ENGINE=INNODB;
```

验证外键约束性,我们向students表插入一个学生数据,并让FK_class的值为一个在class表里面不存在的值.

```
mysql> INSERT INTO students VALUES (1,"zhang",5);
```

马上就会看到Mysql报错

```
ERROR 1452 : Cannot add or update a child row: a foreign key constraint fails (`test/students`, CONSTRAINT `students_ibfk_1` FOREIGN KEY (`FK_class`) REFERENCES `class` (`id`))
```

以上证明外键可以正常工作了

在Mysql下测试了以下,不建立index,并且不指定innodb格式.
版本

+----------------------+
| version()            |
+----------------------+
| 5.0.24a-community-nt |
+----------------------+

建立class表

```
CREATE TABLE class (id INT NOT NULL AUTO_INCREMENT, classname VARCHAR(50) NOT NULL, PRIMARY KEY(id));
```

建立students表

```
CREATE TABLE students (id INT(4) NOT NULL, name VARCHAR(50) NOT NULL, FK_class INT(4) NOT NULL, FOREIGN KEY (FK_class) REFERENCES class (id), PRIMARY KEY(id));
```

通过Navicat查看,上面语句建立的两个表,也会默认用INNODB存储,而且也在students表建立了index和外键.一切正常.
