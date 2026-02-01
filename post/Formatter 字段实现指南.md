---
author: Ryou
title: Formatter 字段实现指南
date: 2026-01-22T00:00:00Z
description: 添加动态 formatter 字段到 YAML 前置的完整实现指南
categories:
  - 技术
tags:
  - obsidian
  - template
  - hugo
lastmod: 2026-02-01T03:00:44Z
---

# Formatter 字段实现指南

## 📋 概述

本指南展示如何在 Obsidian 模板中添加动态的 formatter 字段，包括：
- 🌤️ **天气信息** - 从 Open-Meteo API 获取 (基于高德地图定位)
- 📍 **位置信息** - 自动获取当前城市/地区 (基于高德地图 IP 定位)
- 📝 **Git 日志** - 文件的提交历史
- ⏰ **时间戳** - 生成时的 ISO 时间

---

## 🎯 已实现的字段

### 1. 自动定位与天气
```yaml
位置: 上海市 - 上海市
坐标: 
  - 121.4737
  - 31.2304
温度: 30℃ 
风速: 5.2 m/s
天气: 晴朗
```

**来源**: 
- **位置**: [高德地图 IP 定位](https://lbs.amap.com/api/webservice/guide/api/ipconfig) (国内最准，需要 API Key)
- **天气**: [Open-Meteo API](https://open-meteo.com/) (免费，无需 Key)

**更新频率**: 每次生成模板时自动获取

### 2. Timestamp（时间戳）
```yaml
timestamp: 2026-01-22T22:33:21+08:00
```

**格式**: ISO 8601 标准

### 3. Git Log（Git 日志）
```yaml
git_log: <!-- git-log: auto-generated during build -->
```

**说明**: 需要额外的 build script 支持

---

## 🔧 使用方法

### 方案 1: 使用增强版模板（推荐快速开始）

1. 在 Obsidian 中创建新笔记
2. 输入 `/blog-enhanced` 并选择 `blog-base-enhanced` 模板
3. 模板自动添加所有 formatter 字段

### 方案 2: 手动集成到现有模板

复制以下代码到你的 Templater 模板文件最上方：

```javascript

```

然后在 YAML Frontmatter 区域使用变量：

```yaml
---
位置: Unknown - Unknown
坐标: 
  - 0
  - 0
温度: 0℃ 
风速: 0 m/s
天气: Unknown
---
```

---

## ⚙️ API 说明

### 高德地图 IP 定位 API
- **需要 Key**: 前往 [高德开放平台](https://lbs.amap.com/) 申请 "Web服务" Key
- **准确度**: 国内 IP 识别率极高，精确到城市/区县
- **IPv6 限制**: 目前免费版接口对 IPv6 地址支持有限，可能无法定位。建议在仅 IPv4 环境下使用，或通过系统配置优先 IPv4。

### Open-Meteo 天气 API
- **无需 Key**
- **无需认证**
- **全球覆盖**

---

## 📊 字段对比

| 字段 | 来源 | 更新 | 自动化 | 成本 |
|------|------|------|--------|------|
| 位置 | 高德地图 | 每次 | ✅ 国内最准 | 免费 (需申请Key) |
| 天气 | Open-Meteo | 每次 | ✅ 基于定位 | 免费 |
| timestamp | 系统 | 每次 | ✅ 完全自动 | 免费 |
| git_log | Git | 手动 | ⚠️ 需要脚本 | 免费 |

---

## 🐛 故障排除

### 坐标显示为 Unknown 或 0
```
原因 1: 网络问题 (代理)
如果开启了全局代理，高德 API 请求会通过国外节点发出，导致无法定位到国内位置。
解决: 将 restapi.amap.com 加入直连白名单。

原因 2: IPv6 问题
高德免费 API 无法识别某些 IPv6 地址。
解决: 禁用系统 IPv6，或者等待高德 API 升级。
```

### 模板执行卡顿
```
原因: 等待 API 响应。
解决: 高德 API 在国内响应极快 (通常 <100ms)，如果卡顿请检查 DNS 或网络连接。
```

---
