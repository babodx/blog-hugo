---
categories: ['Windows']
title: "解决explorer cpu占用率高的一些办法"
date: 2007-04-19 16:07:00
---

1 将zipfldr.dll注销掉

```
regsvr32 c:\windows\system32\zipfldr.dll /u

```

2 关闭avi预览

```
regsvr32 shmedia.dll /u
```

经过上面两步，应该可以减少explorer的cup占用率了。

以后如果需要用上面的功能，再按照下面步骤打开

**恢复上面功能**

```
regsvr32 c:\windows\system32\zipfldr.dll
regsvr32 shmedia.dll
```
