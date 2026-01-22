---
author: OpenCode
title: 博客 Slidev PPT 展示集成指南
date: 2026-01-22
description: 将 Slidev 生成的 PPT 展示集成到 Hugo 博客侧边栏
categories:
  - 技术
tags:
  - hugo
  - slidev
  - presentations
  - integration
---

# Slidev PPT 博客集成指南

## 🎯 概述

本指南展示如何将 Slidev（用 Markdown 创建的演示文稿）集成到 Hugo 博客侧边栏中，让访问者可以查看你的演讲和展示。

---

## 🎬 Slidev 简介

### 什么是 Slidev？

Slidev 是一个由 Vue.js 驱动的演示框架，允许你用 Markdown 创建专业的 PPT：

- ✅ 用 Markdown 编写
- ✅ 内置代码高亮
- ✅ 支持 Vue 组件
- ✅ 实时预览
- ✅ 导出为 PDF 和 HTML

**官网**: https://sli.dev

---

## 🚀 快速开始

### Step 1: 安装 Slidev

```bash
npm create slidev@latest my-presentation
cd my-presentation
npm run dev  # 本地预览
```

### Step 2: 创建演示文稿

```markdown
# my-presentation/slides.md

---

# 我的演讲标题

这是第一张幻灯片

---

# 第二张幻灯片

- 要点 1
- 要点 2
- 要点 3

---

# 代码示例

```javascript
function hello() {
  console.log("Hello Slidev!");
}
`
```

---

# 感谢观看！
```

### Step 3: 生成静态文件

```bash
npm run build  # 构建为 dist/
```

---

## 📁 文件结构组织

### Hugo 项目结构

```
hugo-main/
├── config.toml
├── content/
├── themes/
├── static/
│   └── presentations/          # 新增：演示文稿目录
│       ├── presentation-1/
│       │   ├── index.html
│       │   ├── assets/
│       │   └── ...
│       └── presentation-2/
│           └── ...
└── layouts/
    └── partials/
        └── presentations.html   # 新增：侧边栏部分
```

### 部署演示文稿

```bash
# 从 Slidev 项目构建
npm run build

# 复制到 Hugo static 目录
cp -r dist/ /path/to/hugo-main/static/presentations/my-presentation/

# 或设置自动构建脚本
```

---

## 🔗 创建展示部分

### layouts/partials/presentations.html

```html
<!-- 演示文稿侧边栏小部件 -->

<div class="widget presentations-widget">
  <h3>📊 演讲 & 展示</h3>
  
  <div class="presentations-list">
    <!-- 演示文稿卡片 -->
    {{/*/ range $.Site.Data.presentations /*/}}
      <div class="presentation-card">
        <div class="presentation-header">
          <h4>{{/* .title */}}</h4>
          <span class="date">{{/* .date */}}</span>
        </div>
        
        <p class="description">{{/* .description */}}</p>
        
        <div class="presentation-meta">
          <span class="event">📍 {{/* .event */}}</span>
          <span class="slides-count">{{/* .slides */}} 张</span>
        </div>
        
        <a href="{{/* .url */}}" class="btn-view" target="_blank">
          查看幻灯片 →
        </a>
      </div>
    {{/*/ end /*/}}
  </div>
</div>

<style>
.presentations-widget {
  padding: 1.5rem;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  border-radius: 8px;
  box-shadow: 0 4px 6px rgba(0,0,0,0.1);
}

.presentations-widget h3 {
  margin-top: 0;
  margin-bottom: 1.5rem;
  font-size: 1.2em;
}

.presentations-list {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.presentation-card {
  background: rgba(255, 255, 255, 0.1);
  padding: 1rem;
  border-radius: 6px;
  border-left: 3px solid rgba(255, 255, 255, 0.3);
  backdrop-filter: blur(10px);
  transition: all 0.3s ease;
}

.presentation-card:hover {
  background: rgba(255, 255, 255, 0.15);
  border-left-color: rgba(255, 255, 255, 0.5);
  transform: translateX(4px);
}

.presentation-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 0.5rem;
}

.presentation-header h4 {
  margin: 0;
  font-size: 1em;
  font-weight: 600;
}

.presentation-header .date {
  font-size: 0.8em;
  opacity: 0.8;
}

.presentation-card .description {
  margin: 0.5rem 0;
  font-size: 0.9em;
  opacity: 0.95;
  line-height: 1.4;
}

.presentation-meta {
  display: flex;
  gap: 1rem;
  font-size: 0.85em;
  margin: 0.75rem 0;
  opacity: 0.9;
}

.btn-view {
  display: inline-block;
  margin-top: 0.75rem;
  padding: 0.5rem 1rem;
  background: rgba(255, 255, 255, 0.2);
  border: 1px solid rgba(255, 255, 255, 0.3);
  color: white;
  text-decoration: none;
  border-radius: 4px;
  font-size: 0.9em;
  font-weight: 500;
  transition: all 0.3s ease;
}

.btn-view:hover {
  background: rgba(255, 255, 255, 0.3);
  border-color: rgba(255, 255, 255, 0.5);
}

@media (max-width: 768px) {
  .presentations-widget {
    margin-bottom: 1.5rem;
  }
  
  .presentation-meta {
    flex-direction: column;
  }
}
</style>
```

---

## 📊 数据配置

### data/presentations.json

```json
[
  {
    "title": "Vue 3 最新特性",
    "description": "深入讲解 Vue 3 的组合 API 和性能优化",
    "event": "2026年1月技术分享",
    "date": "2026-01-15",
    "slides": 42,
    "url": "/presentations/vue3-features/",
    "image": "/presentations/vue3-features/cover.jpg"
  },
  {
    "title": "Hugo 博客系统实战",
    "description": "从零开始构建高性能静态网站生成器",
    "event": "Obsidian 工作坊",
    "date": "2026-01-10",
    "slides": 38,
    "url": "/presentations/hugo-workshop/",
    "image": "/presentations/hugo-workshop/cover.jpg"
  },
  {
    "title": "Markdown 技巧分享",
    "description": "提高写作效率的 Markdown 最佳实践",
    "event": "技术博主沙龙",
    "date": "2025-12-20",
    "slides": 25,
    "url": "/presentations/markdown-tips/",
    "image": "/presentations/markdown-tips/cover.jpg"
  }
]
```

---

## 🔄 自动化构建流程

### 创建发布脚本

```bash
#!/bin/bash
# deploy-presentation.sh

set -e

SLIDEV_PROJECT="$1"
HUGO_STATIC="/path/to/hugo-main/static/presentations"

if [ -z "$SLIDEV_PROJECT" ]; then
  echo "Usage: ./deploy-presentation.sh <project-dir>"
  exit 1
fi

cd "$SLIDEV_PROJECT"

# 构建 Slidev
echo "🔨 Building Slidev presentation..."
npm run build

# 获取项目名称
PROJECT_NAME=$(basename "$SLIDEV_PROJECT")

# 复制到 Hugo
echo "📋 Copying to Hugo..."
mkdir -p "$HUGO_STATIC/$PROJECT_NAME"
cp -r dist/* "$HUGO_STATIC/$PROJECT_NAME/"

# 生成演示卡片数据
echo "📊 Updating presentations data..."
node << 'EOF'
const fs = require('fs');
const path = require('path');

const presentationsJson = path.join(process.env.HUGO_STATIC, '..', '..', 'data', 'presentations.json');
const presentations = JSON.parse(fs.readFileSync(presentationsJson, 'utf-8'));

// 更新或添加演示数据
const found = presentations.find(p => p.url === `/presentations/${PROJECT_NAME}/`);

if (!found) {
  presentations.unshift({
    title: process.env.PROJECT_NAME,
    description: "新增演示",
    event: new Date().toLocaleDateString('zh-CN'),
    date: new Date().toISOString().split('T')[0],
    slides: 0,
    url: `/presentations/${process.env.PROJECT_NAME}/`
  });
  
  fs.writeFileSync(presentationsJson, JSON.stringify(presentations, null, 2));
}

console.log('✅ Presentations data updated');
EOF

echo "✅ Deployment complete!"
echo "📍 View at: http://localhost:1313/presentations/$PROJECT_NAME/"
```

### 使用脚本

```bash
chmod +x deploy-presentation.sh
./deploy-presentation.sh ./my-presentation
```

---

## 🎨 自定义展示

### 创建演示列表页面

```markdown
<!-- content/_index.md -->

+++
title = "演讲 & 展示"
+++

# 我的演讲和展示

这里收集了我的所有公开演讲和技术分享。


```

### 创建单个演示页面

```markdown
<!-- content/presentations/vue3-workshop.md -->

+++
title = "Vue 3 最新特性工作坊"
date = 2026-01-15
+++

## 会议信息

- **主题**: Vue 3 最新特性
- **日期**: 2026年1月15日
- **地点**: 技术中心 201 室
- **参加人数**: 45+

## 演讲大纲

1. Vue 3 概览
2. Composition API 详解
3. 性能优化技巧
4. 实战项目演示
5. Q&A

---


```

---

## 📱 响应式设计

```css
/* 确保演示在移动设备上显示正确 */

@media (max-width: 1024px) {
  .presentations-widget {
    grid-column: 1 / -1;  /* 占满宽度 */
  }
}

/* 全屏演示 */
.presentation-frame {
  width: 100%;
  height: 100vh;
  border: none;
}

@media (max-width: 768px) {
  .presentation-frame {
    height: 50vh;
  }
}
```

---

## 🔐 GitHub Actions 自动部署

### .github/workflows/deploy-presentations.yml

```yaml
name: Deploy Presentations

on:
  push:
    paths:
      - 'presentations/**'
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '18'
      
      - name: Build presentations
        run: |
          for dir in presentations/*/; do
            if [ -f "$dir/package.json" ]; then
              cd "$dir"
              npm ci
              npm run build
              cd ../../
            fi
          done
      
       - name: Deploy to hugo-main
         uses: peter-evans/repository-dispatch@v3
         with:
           token: ${{/* secrets.GH_PAGE_ACTION_TOKEN */}}
           repository: WASIDJ/hugo-main
           event-type: presentations-updated
```

---

## 🎯 最佳实践

### ✅ 推荐

- ✅ 使用一致的命名规范
- ✅ 为每个演示添加描述
- ✅ 定期更新演示列表
- ✅ 备份原始 Slidev 源文件
- ✅ 提供演示的 PDF 版本

### ❌ 避免

- ❌ 在静态文件中存储 Slidev 源代码
- ❌ 忘记更新演示数据
- ❌ 使用过大的视频或媒体文件
- ❌ 链接到外部 CDN 的演示

---

## 📊 性能优化

### 懒加载演示

```html
<iframe
  src="/presentations/my-presentation/"
  loading="lazy"
  class="presentation-frame"
  title="演示文稿"
></iframe>
```

### 预缩放图像

```bash
# 为演示封面图生成缩略图
for img in static/presentations/*/cover.jpg; do
  convert "$img" -resize 300x200 "${img%.jpg}-thumb.jpg"
done
```

---

## ✅ 发布检查表

- [ ] Slidev 项目已创建并本地测试
- [ ] 演示内容完成
- [ ] PDF 导出已验证
- [ ] 演示数据已添加到 JSON
- [ ] Hugo 侧边栏小部件已集成
- [ ] 样式已适配所有设备
- [ ] 演示链接已测试
- [ ] GitHub Actions 工作流已配置
- [ ] 性能已优化
- [ ] 备份已创建

---

## 📚 参考资源

- [Slidev 官方文档](https://sli.dev/)
- [Hugo Shortcodes 文档](https://gohugo.io/content-management/shortcodes/)
- [Web 组件嵌入](https://developer.mozilla.org/en-US/docs/Web/Web_Components/)

---

**最后更新**: 2026-01-22
**复杂度**: 中等
**相关 TODO**: blog-content/post/TODO.md (item #9)
