---
title: "iterm2给ssh窗口上色"
date: 2013-10-02 21:58:00
categories: ['Mac']
---

#给iterm2的ssh窗口上颜色#

经常开ssh登陆远程服务器，有时自己都分不清那个是远程那个是本地了。

今天看到一个技巧，可以让iterm2 给ssh到远程的窗口上个颜色。这样一看颜色就知道不是本地了。

首先就是编辑~.zshrc文件,在末尾加入

```
source iterm2.zsh

```

然后在用户目录创建一个iterm2.zsh文件，内容如下

```
tab-color() {
  echo -ne "\033]6;1;bg;red;brightness;$1\a"
  echo -ne "\033]6;1;bg;green;brightness;$2\a"
  echo -n
}

tab-reset() {
  echo -ne "\033]6;1;bg;*;default\a"
}

color-ssh() {
  if [[ -n "$ITERM_SESSION_ID" ]]; then
    trap "tab-reset" INT EXIT
    if [[ "$*" =~ "production|ec2-.*compute-1" ]]; then
      tab-color 255 0 0
    else
      tab-color 0 255 0
    fi
  fi
  ssh $*
}
compdef _ssh color-ssh=ssh
alias ssh=color-ssh

```
<!--more-->
看看我ssh窗口，用绿色显示了

![ssh的彩色窗口](http://farm8.staticflickr.com/7361/10054335793_5a68ac7791.jpg)
