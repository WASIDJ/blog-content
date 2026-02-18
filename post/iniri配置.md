---
author: Ryou
title: iniri配置
date: 2026-02-15T18:48:10+08:00
lastmod: 2026-02-15T21:10:37+08:00
description: 
draft: true
mermaid: true
image: https://w.wallhaven.cc/full/8g/wallhaven-8gejoo.jpg
位置: 上海市 - 上海市
坐标: 
  - 121.4768
  - 31.2243
温度: 12.6℃ 
风速: 13.6 m/s
天气: 阴天
categories:
  - 
tags:
  - 
---
niri msg outputs

>[!summary] 前情提要
>- 好处：不是线性，而是二维，范围大，更加专注。
>- 缺陷：panel内切换不方便
>- 拓展：3维+VR

### [-]最大的问题：sddm 登录进入 niri后 一段时间后 又回退到 `sddm`中不稳定
### OCR 不识别中文
```yaml
snipProc.command = ["/usr/bin/bash", "-c", `${cropInPlace} && /home/ryou/bin/ocr-text '${StringUtils.shellSingleQuoteEscape(root.screenshotPath)}' | /usr/bin/wl-copy && ${cleanup} && /usr/bin/notify-send "Text recognized" "OCR text copied to clipboard" -a "OCR" -i edit-find -t 3000`]
```

### 翻译功能不能用

### Voxtype 快捷键 涉及问题


### 快捷键

| 按键                   | 功能                 | 备注             |     |
| -------------------- | ------------------ | -------------- | --- |
| 系统                   |                    |                |     |
| Mod+Tab              | 切换 Overview（工作区概览） | star           |     |
| Mod+Shift+E          | 退出 Niri            |                |     |
| Mod+Escape           | 切换键盘快捷键禁用          |                |     |
| ii 窗口切换器             |                    |                |     |
| Alt+Tab              | 下一个窗口              | star           |     |
| Alt+Shift+Tab        | 上一个窗口              |                |     |
| ii 组件                |                    |                |     |
| Super+G              | 切换 ii Overlay      | star           |     |
| Mod+Space            | 切换 ii Overview     | star           |     |
| Mod+V                | 剪贴板管理器             | star           |     |
| Mod+Alt+L            | 锁屏                 |                |     |
| Mod+Shift+S          | 区域截图               |                |     |
| Mod+Shift+X          | OCR 识别             | bug 无法识别中 精度不高 |     |
| Mod+Shift+A          | 区域搜索（什么东西？）        |                |     |
| Ctrl+Alt+T           | 壁纸选择器              |                |     |
| Mod+Comma            | 打开设置               |                |     |
| Mod+Slash            | 快捷键速查表             |                |     |
| Mod+Shift+W          | 切换面板样式             | 没有用            |     |
| 应用程序                 |                    |                |     |
| Mod+T / Mod+Return   | 打开终端               |                |     |
| Super+E              | 打开文件管理器 (Dolphin)  |                |     |
| Mod+Q                | 关闭窗口               |                |     |
| Mod+C                | vscode             | 加入             |     |
| Mod+X                | obsidian           | 加入             |     |
| Mod+Z                | zotero             | 加入             |     |
| Mod+H                | feishu             | 加入             |     |
| 窗口管理                 |                    |                |     |
| Mod+D                | 最大化当前列             |                |     |
| Mod+F                | 窗口全屏               |                |     |
| Mod+A                | 切换窗口浮动             |                |     |
| 焦点移动                 |                    |                |     |
| Mod+←/→/↑/↓          | 焦点在窗口间移动           |                |     |
| Mod+H/J/K/L          | 同上（vim 风格）         |                |     |
| 窗口移动                 |                    |                |     |
| Mod+Shift+←/→/↑/↓    | 将窗口移动到其他位置         |                |     |
| Mod+Shift+H/J/K/L    | 同上（vim 风格）         |                |     |
| 工作区                  |                    |                |     |
| Mod+1~9              | 切换到工作区 1-9         |                |     |
| Mod+Shift+1~5        | 将窗口移到工作区 1-5       |                |     |
| 截图                   |                    |                |     |
| Print                | 截图                 |                |     |
| Ctrl+Print           | 截取整个屏幕             |                |     |
| Alt+Print            | 截取当前窗口             |                |     |
| 媒体                   |                    |                |     |
| XF86AudioRaiseVolume | 音量增加               |                |     |
| XF86AudioLowerVolume | 音量减少               |                |     |
| XF86AudioMute        | 静音                 |                |     |
| XF86AudioMicMute     | 麦克风静音              |                |     |
| Ctrl+Mod+Space       | 播放/暂停              |                |     |
| Mod+Alt+N            | 下一曲                |                |     |
| Mod+Alt+P            | 上一曲                |                |     |

自动启动的服务

| 命令 | 功能 |
|------|------|
| wl-paste --watch cliphist store & | 剪贴板历史记录 |
| /usr/lib/mate-polkit/polkit-mate-authentication-agent-1 | Polkit 认证代理 |
| qs -c ii | Quickshell ii 桌面环境 |

### niri有没有类似 hyprland super + s 的常驻区？



