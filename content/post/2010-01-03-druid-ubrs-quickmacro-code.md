---
categories: ['Game']
title: '野D带黑上，后台挂机按键精灵代码'
date: 2010-01-03 19:34:00
---
代码是运行在按键精灵里的。在winxp+按键精灵7和win7+按键精灵7下测试通过

```
Plugin hwnd=Window.GetKeyFocusWnd()
Rem start
For 30
Plugin Bkgnd.KeyPress(hwnd,70)
Delay 300
EndFor
Delay 1000
Plugin Bkgnd.KeyDown(hwnd,16)
Plugin Bkgnd.KeyPress(hwnd,87)
Plugin Bkgnd.KeyUp(hwnd,16)
Delay 500
For 30
Plugin Bkgnd.KeyPress(hwnd,70)
Delay 300
EndFor
Delay 1000
Plugin Bkgnd.KeyDown(hwnd,18)
Plugin Bkgnd.KeyPress(hwnd,68)
Plugin Bkgnd.KeyUp(hwnd,18)
Delay 500
For 30
Plugin Bkgnd.KeyPress(hwnd,70)
Delay 300
EndFor
Delay 1000
For 8
Plugin Bkgnd.KeyDown(hwnd,16)
Plugin Bkgnd.KeyPress(hwnd,82)
Plugin Bkgnd.KeyUp(hwnd,16)
EndFor
Delay 500
Goto start

```
代码说明：

上面是我自己使用的代码，我的快捷键设置和大家不一样，所以别人使用，请注意修改快捷键。

精灵之火（野性） Shift+R

树皮 Shift+W

狂暴回复 Alt+D

横扫+重殴 宏 F （其实就是个AOE宏，需要不断切换目标，保证重殴可以放出来就可以）

以上就是我带刷的代码，基本保证装备不红，人物不死。
