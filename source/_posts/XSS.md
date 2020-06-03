---
title: XSS 函数
date: 2020-05-25 14:36:41
categories: 
- 笔记
tags: 
- XSS
- 跨站脚本
---
# Cross-Site Scripting

## Tips & tricks

Les articles suivants présentent des moyens de contourner certains protections (WAF, blacklist, etc.)

- [XSS without event handlers](https://brutelogic.com.br/blog/xss-without-event-handlers/): pour les situations où les events du type onerror sont bloqués

<!--more-->

## Liste des events javascripts

``` text
onclick
oncontextmenu
ondblclick
onmousedown
onmouseenter
onmouseleave
onmousemove
onmouseover
onmouseout
onmouseup
onkeydown
onkeypress
onkeyup
onabort
onbeforeunload
onerror
onhashchange
onload
onpageshow
onpagehide
onresize
onscroll
onunload
onblur
onchange
onfocus
onfocusin
onfocusout
oninput
oninvalid
onreset
onsearch
onselect
onsubmit
ondrag
ondragend
ondragenter
ondragleave
ondragover
ondragstart
ondrop
oncopy
oncut
onpaste
onafterprint
onbeforeprint
onabort
oncanplay
oncanplaythrough
ondurationchange
onended
onerror
onloadeddata
onloadedmetadata
onloadstart
onpause
onplay
onplaying
onprogress
onratechange
onseeked
onseeking
onstalled
onsuspend
ontimeupdate
onvolumechange
onwaiting
onerror
onmessage
onopen
onwheel
ononline
onoffline
onshow
ontoggle
onwheel
```

