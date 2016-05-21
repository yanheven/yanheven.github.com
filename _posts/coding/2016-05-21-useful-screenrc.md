---
layout: post
title: useful screenrc
category: coding
description: 2016-05-21

---

Author:[海峰 http://weibo.com/344736086](http://weibo.com/344736086)

screen result:

![screenrc](/images/githubpages/screenrc.jpg)

screenrc:  

```
termcapinfo xterm|xterms|xs|rxvt ti@:te@
term xterm
defutf8 on
defflow off
vbell off


startup_message off
defscrollback 2048


hardstatus on
hardstatus alwayslastline "%{= wk} %{by} %H %{wk} | %-Lw%{kw} %{= g}%n%f* %t%{wk} %{wk}%+Lw%< %= %{kw} %{= R} [%m/%d %c] %{-}"
```