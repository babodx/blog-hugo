---
title: "自定义zabbix inventory界面"
date: 2014-06-05 01:58:00
categories: ["Linux"]
---
Zabbix报警后，点击报警的主机会出现一个host inventory连接。进入host inventory后，可以查看报警主机的一些信息。而Overview内的信息并不多，Details栏内信息又太多了。
我想通过自定义这个Overview将比较关系的操作系统，联系人，硬件位置等信息显示在此页面中.

通过查看代码，最终发现此页面对应/usr/share/zabbix/include/views目录下的inventory.host.view.php模板文件

``` php
// inventory (OS, Contact, , Hardware, Location)
if ($this->data['host']['inventory']) {
        if ($this->data['host']['inventory']['os']) {
                $overviewFormList->addRow(
                        $this->data['tableTitles']['os']['title'],
                        new CSpan(zbx_str2links($this->data['host']['inventory']['os']), 'text-field')
                );
        }
        if ($this->data['host']['inventory']['contact']) {
                $overviewFormList->addRow(
                        $this->data['tableTitles']['contact']['title'],
                        new CSpan(zbx_str2links($this->data['host']['inventory']['contact']), 'text-field')
                );
        }
        if ($this->data['host']['inventory']['hardware']) {
                $overviewFormList->addRow(
                        $this->data['tableTitles']['hardware']['title'],
                        new CSpan(zbx_str2links($this->data['host']['inventory']['hardware']), 'text-field')
                );
        }
        if ($this->data['host']['inventory']['location']) {
                $overviewFormList->addRow(
                        $this->data['tableTitles']['location']['title'],
                        new CSpan(zbx_str2links($this->data['host']['inventory']['location']), 'text-field')
                );
        }
}

```

其中每一个if段，对应一个信息。我这里分别显示os,contact,hardware和location



