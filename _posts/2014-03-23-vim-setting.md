---
layout: post
title: "Vim setting"
description: ""
category: "tool"
tags: [vim]
---
{% include JB/setup %}

    set nu  " line number
    syntax on " highlight
    set shiftwidth=4
    set tabstop=4
    set expandtab  " replace tab with spaces
    set autoindect
    set cindent    " C style indent

undo `u`
redo `control r` both mac and windows

####命令备忘录：（太常用的比较hjkl 3dd y p之类就不说了）

| 命令      | 语法      | 说明      |
| ----------|-----------|-----------|
| replace one place | :s/foo/test | |
| replace all in line | :s/foo/test/g | |
| replace all in file | :%s/foo/test | |
| find ignoring case | /foo\c | |

