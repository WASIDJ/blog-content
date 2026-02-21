---
author: Ryou
title: vscode中使用nvim
date: 2026-02-19T22:07:41+08:00
lastmod: 2026-02-21T15:41:21+08:00
description: 
draft: true
mermaid: true
image: https://w.wallhaven.cc/full/5y/wallhaven-5yz8z8.jpg
位置: 上海市 - 上海市
坐标: 
  - 121.4768
  - 31.2243
温度: 7℃ 
风速: 5.4 m/s
天气: 主要多云
categories:
  - 
tags:
  - 
---
>[!summary] 前情提要

- `w` to move word by word
- `b` to move backwards word by word
-  `e` to jump to the end of a word
- `ge` to jup to the end of the previous word
- `E` is like `e` but operates on WORDS
- `gE` is like `ge` but operates on WORDS
- Use `f{character}` (find) to move to the next occurrence of a character in a line.
- Use `F{character}` to find the previous occurrence of a character
- Use `t{character}` to move the cursor just before the next occurrence of a character (think of `t{character}` of moving your cursor until that character).
- Again, you can use `T{character}` to do the same as `t{character}` but backwards
- `0`: Moves to the first character of a line
- `^`: Moves to the first non-blank character of a line
- `$`: Moves to the end of a line
- `g_`: Moves to the non-blank character at the end of a line
- `}` jumps entire paragraphs downwards
- `{` similarly but upwards
- `CTRL-D` lets you move down half a page by scrolling the page
- `CTRL-U` lets you move up half a page also by scrolling
- Use `gd` to **g**o to **d**efinition of whatever is under your cursor.
- Use `gf` to **g**o to a **f**ile in an import.

- Type `gg` to go to the top of the file.
- Use `{line}gg` to go to a specific line.
- Use `G` to go to the end of the file.
- Type `%` jump to matching `({[]})`.




