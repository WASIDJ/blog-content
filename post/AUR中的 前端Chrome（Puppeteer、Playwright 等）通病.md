---
author: Ryou
title: AUR中的 前端Chrome（Puppeteer、Playwright 等）通病
date: 2026-02-16T03:16:57+08:00
lastmod: 
description: 
draft: true
mermaid: true
image: https://w.wallhaven.cc/full/8g/wallhaven-8gejoo.jpg
位置: 上海市 - 上海市
坐标: 
  - 121.4768
  - 31.2243
温度: 6.5℃ 
风速: 16.7 m/s
天气: 小雨
categories:
  - 
tags:
  - 
---


>[!summary] 前情提要
>- arch中 前端凡是涉及 Chrome（Puppeteer、Playwright 等） ，必挂。
>- 通用解决方式：
>	1. 查看 看看能不能导入 直接本地的 `/usr/bin/chromium` (环境变量)
>	2. 使用 `direnv` （更加优雅）
>	- NOTE：通用法 直接配置 `.puppeteerrc.cjs`

### .puppeteerrc.cjs
```js
// ~/.puppeteerrc.cjs (注意后缀是 .cjs，因为 Puppeteer 优先识别 CommonJS 格式)
const { join } = require('path');

/**
 * @type {import("puppeteer").Configuration}
 */
module.exports = {
  // 指定你本地已安装的 Chromium/Chrome 可执行文件路径
  // 不同系统路径示例：
  // Linux: /usr/bin/chromium 或 /usr/bin/google-chrome
  // macOS: /Applications/Google Chrome.app/Contents/MacOS/Google Chrome
  // Windows: C:\\Program Files\\Google\\Chrome\\Application\\chrome.exe
  executablePath: '/usr/bin/chromium',
  
  // 跳过 Puppeteer 自动下载 Chromium (关键：避免重复下载占用空间)
  skipDownload: true,
  
  // 可选：指定缓存目录（如果需要）
  // cacheDirectory: join(process.env.HOME, '.cache', 'puppeteer'),
};

```

