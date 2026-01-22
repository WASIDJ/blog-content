---
author: Ryou
title: Formatter 字段实现指南
date: 2026-01-22
description: 添加动态 formatter 字段到 YAML 前置的完整实现指南
categories:
  - 技术
tags:
  - obsidian
  - template
  - hugo
---

# Formatter 字段实现指南

## 📋 概述

本指南展示如何在 Obsidian 模板中添加动态的 formatter 字段，包括：
- 🌤️ **天气信息** - 从 Open-Meteo API 获取
- 📍 **坐标信息** - 可配置的地理位置
- 📝 **Git 日志** - 文件的提交历史
- ⏰ **时间戳** - 生成时的 ISO 时间

---

## 🎯 已实现的字段

### 1. Weather（天气）
```yaml
weather: 30°C - ☀️ 晴朗
```

**来源**: Open-Meteo API (免费，无需 API Key)
**更新频率**: 每次生成模板时
**优势**: 
- 免费
- 无需身份验证
- 支持全球任何位置

### 2. Coordinates（坐标）
```yaml
coordinates: 30.5728,114.3055
```

**说明**: 
- 默认: 武汉坐标 (30.5728, 114.3055)
- 可在模板中修改 `LOCATION` 对象

### 3. Timestamp（时间戳）
```yaml
timestamp: 2026-01-22T22:33:21+08:00
```

**格式**: ISO 8601 标准

### 4. Git Log（Git 日志）
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

复制以下代码到你的模板中：

```javascript

```

---

## 🌍 修改位置

修改模板中的 `LOCATION` 对象以支持不同城市：

```javascript
const LOCATION = { 
  lat: 30.5728,    // 纬度
  lng: 114.3055    // 经度
};
```

### 常见城市坐标

| 城市 | 纬度 | 经度 |
|------|------|------|
| 北京 | 39.9042 | 116.4074 |
| 上海 | 31.2304 | 121.4737 |
| 广州 | 23.1291 | 113.2644 |
| 深圳 | 22.5431 | 114.0579 |
| 武汉 | 30.5728 | 114.3055 |
| 成都 | 30.5728 | 104.0668 |
| 西安 | 34.3416 | 108.9398 |
| 纽约 | 40.7128 | -74.0060 |
| 伦敦 | 51.5074 | -0.1278 |
| 东京 | 35.6762 | 139.6503 |

---

## 🔌 Hugo 集成

在你的 Hugo 主题中使用这些字段：

```go
{{ with .Params.weather }}
<div class="weather-badge">
  <span>{{ . }}</span>
</div>
{{ end }}

{{ with .Params.coordinates }}
<a href="https://maps.google.com/?q={{ . }}" class="geo-link">
  📍 {{ . }}
</a>
{{ end }}
```

---

## ⚙️ API 说明

### Open-Meteo 天气 API

**端点**: `https://api.open-meteo.com/v1/forecast`

**参数**:
- `latitude` - 纬度
- `longitude` - 经度
- `current` - 当前天气字段
- `temperature_unit` - 温度单位 (celsius/fahrenheit)

**示例**:
```
https://api.open-meteo.com/v1/forecast?latitude=30.5728&longitude=114.3055&current=temperature_2m,weather_code&temperature_unit=celsius
```

**响应**:
```json
{
  "current": {
    "temperature_2m": 28.5,
    "weather_code": 0,
    "time": "2026-01-22T22:30"
  }
}
```

**优势**:
- ✅ 免费使用
- ✅ 无需 API Key
- ✅ 无速率限制
- ✅ 支持全球任何位置

---

## 🚀 未来改进

### Git Log 实现

需要在 Hugo build 过程中执行脚本：

```bash
#!/bin/bash
for file in content/post/*.md; do
  gitlog=$(git log -1 --format="%h %s" "$file" 2>/dev/null || echo "No git history")
  sed -i "s|<!-- git-log: auto-generated during build -->|git_log: $gitlog|g" "$file"
done
```

### 动态位置切换

可在 frontmatter 中添加位置字段，使不同文章使用不同位置：

```yaml
coordinates: 30.5728,114.3055  # 覆盖默认位置
```

---

## 📊 字段对比

| 字段 | 来源 | 更新 | 自动化 | 成本 |
|------|------|------|--------|------|
| weather | API | 每次 | ✅ 完全自动 | 免费 |
| coordinates | 配置 | 静态 | ✅ 完全自动 | 免费 |
| timestamp | 系统 | 每次 | ✅ 完全自动 | 免费 |
| git_log | Git | 手动 | ⚠️ 需要脚本 | 免费 |

---

## 🐛 故障排除

### 天气 API 无法连接

```
原因: 网络问题或 API 服务不可用
解决:
1. 检查网络连接
2. 手动访问: https://api.open-meteo.com/v1/forecast?latitude=30.5728&longitude=114.3055&current=temperature_2m
3. 如果仍然失败，使用其他天气 API
```

### 模板执行出错

```
原因: async/await 不正确或网络延迟
解决:
1. 确保在  执行块中使用 await
2. 增加超时时间
3. 添加更详细的错误处理
```

---

## 📚 参考资源

- [Templater 文档](https://silentvoid13.github.io/Templater/)
- [Open-Meteo API 文档](https://open-meteo.com/en/docs)
- [Hugo 参数文档](https://gohugo.io/content-management/front-matter/)
- [ISO 8601 时间标准](https://en.wikipedia.org/wiki/ISO_8601)

---

## ✅ 完成检查表

- [x] Obsidian 模板实现
- [x] Weather API 集成
- [x] 坐标字段支持
- [x] 时间戳生成
- [x] Hugo 集成指南
- [ ] Git Log 自动化（需要 build 脚本）
- [ ] 地理编码反向查询（可选）
- [ ] 多语言天气描述（可选）

---

**最后更新**: 2026-01-22
**维护者**: Ryou
