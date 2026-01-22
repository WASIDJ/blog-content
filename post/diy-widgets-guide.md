---
author: Ryou
title: Hugo 博客 DIY 小部件指南
date: 2026-01-22
description: 添加访客统计、文章热度、站点运行时间等自定义小部件
categories:
  - 技术
tags:
  - hugo
  - widget
  - analytics
  - javascript
---

# Hugo 博客 DIY 小部件指南

## 🎯 概述

本指南展示如何为 Hugo 博客添加以下小部件：

| 小部件 | 功能 | 成本 | 难度 |
|--------|------|------|------|
| 访客统计 | 实时访问计数 | 免费/付费 | 简单 |
| 文章热度 | 按浏览量排序 | 免费 | 中等 |
| 站点运行时间 | 显示上线时长 | 免费 | 简单 |
| 最后更新时间 | 文章修改日期 | 免费 | 简单 |

---

## 1️⃣ 访客统计 Widget

### 方案 A: Busuanzi（推荐 - 中文最佳）

**优势**: 
- ✅ 完全免费
- ✅ 支持中文
- ✅ 集成简单
- ✅ 响应快速

**集成方法**:

```html
<!-- 在 layouts/partials/footer.html 或 sidebar.html 中 -->

<div class="widget visitor-stats">
  <h3>站点统计</h3>
  <ul>
    <li>
      <span>👥 访客数</span>
      <span id="busuanzi_value_site_uv">-</span>
    </li>
    <li>
      <span>📊 总浏览</span>
      <span id="busuanzi_value_site_pv">-</span>
    </li>
  </ul>
</div>

<script async src="//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>
```

**样式**:

```css
.widget.visitor-stats {
  padding: 1.5rem;
  border-radius: 8px;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  box-shadow: 0 4px 6px rgba(0,0,0,0.1);
}

.widget.visitor-stats ul {
  list-style: none;
  padding: 0;
  margin: 0;
}

.widget.visitor-stats li {
  display: flex;
  justify-content: space-between;
  padding: 0.5rem 0;
  border-bottom: 1px solid rgba(255,255,255,0.1);
}

.widget.visitor-stats li span:last-child {
  font-weight: bold;
  font-size: 1.2em;
}
```

### 方案 B: GoAccess（自托管，高级）

**优势**:
- ✅ 完全隐私
- ✅ 完整的数据控制
- ✅ 支持实时分析

**配置**: 需要部署 GoAccess 服务器（复杂度较高）

---

## 2️⃣ 文章热度 Widget

### 方案: 基于文章浏览量排序

**实现步骤**:

#### Step 1: 在 Hugo 模板中收集数据

```go
<!-- layouts/partials/article-stats.html -->

<!-- 文章热度 - 基于 frontmatter 自定义字段 -->
{{ with .Params.views }}
  <div class="article-heat" title="浏览量: {{ . }}">
    <span class="heat-icon">🔥</span>
    <span class="heat-value">{{ . }}</span>
  </div>
{{ end }}
```

#### Step 2: 创建热门文章列表

```go
<!-- layouts/partials/hot-articles.html -->

<div class="widget hot-articles">
  <h3>🔥 热门文章</h3>
  <ul>
    {{ range (where .Site.RegularPages ".Params.views" ">" 100) | first 5 }}
      <li>
        <a href="{{ .Permalink }}">{{ .Title }}</a>
        <span class="views">👁️ {{ .Params.views }}</span>
      </li>
    {{ end }}
  </ul>
</div>
```

#### Step 3: 添加样式

```css
.widget.hot-articles {
  padding: 1.5rem;
  background: #fff;
  border-left: 4px solid #ff6b6b;
  border-radius: 4px;
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}

.widget.hot-articles ul {
  list-style: none;
  padding: 0;
}

.widget.hot-articles li {
  padding: 0.75rem 0;
  border-bottom: 1px solid #eee;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.widget.hot-articles li:last-child {
  border-bottom: none;
}

.widget.hot-articles .views {
  font-size: 0.85em;
  color: #666;
}
```

#### Step 4: 在 frontmatter 中添加浏览量

```yaml
---
title: 我的文章
views: 256  # 添加这个字段
---
```

---

## 3️⃣ 站点运行时间 Widget

### 方案: JavaScript 计算

```html
<!-- layouts/partials/uptime.html -->

<div class="widget site-uptime">
  <h3>⏱️ 站点运行时间</h3>
  <div id="uptime-display">计算中...</div>
</div>

<script>
  function calculateUptime() {
    // 设置博客启动时间（修改为你的实际启动日期）
    const launchDate = new Date('2026-01-16T00:00:00Z');
    const now = new Date();
    
    const diff = now - launchDate;
    
    const days = Math.floor(diff / (1000 * 60 * 60 * 24));
    const hours = Math.floor((diff % (1000 * 60 * 60 * 24)) / (1000 * 60 * 60));
    const minutes = Math.floor((diff % (1000 * 60 * 60)) / (1000 * 60));
    
    let uptimeText = '';
    if (days > 0) uptimeText += `${days}天 `;
    if (hours > 0) uptimeText += `${hours}小时 `;
    if (minutes > 0) uptimeText += `${minutes}分钟`;
    
    document.getElementById('uptime-display').textContent = uptimeText || '0分钟';
  }
  
  // 初始计算
  calculateUptime();
  
  // 每分钟更新一次
  setInterval(calculateUptime, 60000);
</script>
```

**样式**:

```css
.widget.site-uptime {
  padding: 1.5rem;
  background: linear-gradient(to right, #11998e 0%, #38ef7d 100%);
  color: white;
  border-radius: 8px;
  text-align: center;
}

#uptime-display {
  font-size: 1.5em;
  font-weight: bold;
  font-family: 'Courier New', monospace;
}
```

---

## 4️⃣ 最后更新时间 Widget

```html
<!-- layouts/partials/last-modified.html -->

<div class="widget last-modified">
  <span class="label">最后更新</span>
  <time datetime="{{ .Lastmod.Format "2006-01-02T15:04:05" }}">
    {{ .Lastmod.Format "2006-01-02 15:04" }}
  </time>
</div>
```

**样式**:

```css
.widget.last-modified {
  padding: 0.75rem 1rem;
  background: #f5f5f5;
  border-radius: 4px;
  font-size: 0.9em;
  color: #666;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.widget.last-modified .label {
  font-weight: 500;
}

.widget.last-modified time {
  color: #999;
  font-family: 'Courier New', monospace;
}
```

---

## 📦 完整的 Sidebar 示例

将所有小部件组合到 sidebar 中：

```html
<!-- layouts/partials/sidebar.html -->

<aside class="sidebar">
  <!-- 访客统计 -->
  {{ partial "visitor-stats.html" . }}
  
  <!-- 运行时间 -->
  {{ partial "uptime.html" . }}
  
  <!-- 热门文章 -->
  {{ partial "hot-articles.html" . }}
  
  <!-- 文章信息 -->
  <div class="widget article-info">
    {{ if .IsPage }}
      {{ partial "last-modified.html" . }}
    {{ end }}
  </div>
</aside>
```

**样式组织**:

```css
.sidebar {
  display: flex;
  flex-direction: column;
  gap: 1.5rem;
  width: 300px;
}

.widget {
  background: white;
  border-radius: 8px;
  box-shadow: 0 2px 8px rgba(0,0,0,0.1);
  transition: transform 0.3s ease, box-shadow 0.3s ease;
}

.widget:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 12px rgba(0,0,0,0.15);
}

@media (max-width: 768px) {
  .sidebar {
    width: 100%;
    flex-direction: row;
    flex-wrap: wrap;
  }
  
  .widget {
    flex: 1;
    min-width: 200px;
  }
}
```

---

## 🎯 高级功能：页面浏览量跟踪

使用 JavaScript 和 localStorage 跟踪文章浏览量：

```html
<!-- layouts/partials/page-views.html -->

<script>
  // 存储页面浏览量
  function trackPageView() {
    const key = `page_views_${window.location.pathname}`;
    const current = parseInt(localStorage.getItem(key) || '0');
    localStorage.setItem(key, current + 1);
    
    // 显示浏览量
    document.getElementById('page-view-count').textContent = current + 1;
  }
  
  // 页面加载时执行
  if (document.readyState === 'loading') {
    document.addEventListener('DOMContentLoaded', trackPageView);
  } else {
    trackPageView();
  }
</script>

<div id="page-view-count" class="page-views">
  <!-- 动态填充 -->
</div>
```

**局限性**:
- ⚠️ 仅基于本地浏览器存储
- ⚠️ 无法跨设备统计
- ⚠️ 清除浏览器数据后重置

**改进方案**: 集成服务端统计（如 GoAccess、Umami）

---

## 🚀 生产就绪清单

- [ ] 添加 Busuanzi 访客统计
- [ ] 配置热门文章列表
- [ ] 设置站点运行时间
- [ ] 添加最后更新显示
- [ ] 编写响应式样式
- [ ] 测试所有小部件
- [ ] 优化加载性能
- [ ] 添加隐私政策说明
- [ ] 考虑集成第三方分析
- [ ] 定期维护和更新

---

## 📊 性能优化

### 异步加载小部件

```html
<script>
  // 延迟加载非关键小部件
  document.addEventListener('DOMContentLoaded', () => {
    // 先加载关键小部件
    
    // 延迟加载其他小部件
    setTimeout(() => {
      const script = document.createElement('script');
      script.src = '//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js';
      document.body.appendChild(script);
    }, 2000);
  });
</script>
```

### 缓存策略

```css
/* 减少重排和重绘 */
.widget {
  contain: layout;
  will-change: transform;
}
```

---

## 🔗 相关资源

- [Busuanzi 官网](https://busuanzi.ibruce.info/)
- [Hugo 部分模板文档](https://gohugo.io/templates/partial-templates/)
- [CSS 性能优化](https://web.dev/css-performance/)

---

## ✅ 实施检查表

- [ ] 选择合适的小部件方案
- [ ] 创建对应的 partial 文件
- [ ] 编写 CSS 样式
- [ ] 添加到 layout 中
- [ ] 测试响应式设计
- [ ] 检查加载性能
- [ ] 发布并监控

---

**最后更新**: 2026-01-22
**相关 TODO**: blog-content/post/TODO.md (item #6)
