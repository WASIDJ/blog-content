---
author: Ryou
title: apifox MCP 自动配置 API文档
date: 2026-02-14T19:11:41+08:00
lastmod: 2026-02-14T19:30:00+08:00
description: 详细介绍 Apifox MCP 自动配置 API 文档的功能、配置方法和使用场景
draft: true
mermaid: true
image: https://w.wallhaven.cc/full/8g/wallhaven-8gejoo.jpg
位置: 上海市 - 上海市
坐标: 
  - 121.4768
  - 31.2243
温度: 15.9℃ 
风速: 6.8 m/s
天气: 主要多云
categories:
  - API开发
tags:
  - Apifox
  - MCP
  - API文档
  - AI编程
  - Cursor
---

> [!summary] 前情提要
> MCP（Model Context Protocol，模型上下文协议）是一种让 AI 应用与外部数据源和工具之间建立标准化通信的开放协议。Apifox 现已全面支持 MCP，让 AI 助手可以直接访问你的 API 在线文档，实现"文档即代码"的高效开发体验。

## 什么是 Apifox MCP

Apifox MCP Server 是 Apifox 推出的基于 Model Context Protocol 协议的 API 文档服务，它可以将 Apifox 项目内的接口文档作为数据源提供给 Cursor、VSCode + Cline、Trae 等支持 AI 编程的 IDE 工具。

### MCP 的核心功能

MCP 将 LLM 与外部世界的交互抽象为三种核心功能：

| 功能 | 英文名称 | 描述 |
|------|----------|------|
| 工具 | `Tools` | 服务端提供的可被执行的函数，例如发送邮件、查询天气或调用其他 API |
| 数据资源 | `Resources` | 服务端提供的结构化数据，例如产品列表、用户信息或知识库文档 |
| 提示词 | `Prompts` | 服务端预定义的提示词模板，可用于生成标准化的、高质量的 LLM 输入 |

## MCP 能做什么

### 1. 根据 API 文档生成数据模型代码
通过 MCP 获取 API 文档，然后生成数据模型的定义代码，例如 Pet 及其相关模型。

### 2. 修改 API 文档后自动同步代码
重新读取 API 文档数据，在数据模型里添加 API 文档新增的字段。

### 3. 根据 API 文档生成接口的 MVC 代码
根据 API 文档，生成接口相关的所有 MVC 代码。

### 4. 更多场景
开发者可以通过 AI 助手完成：
- 根据接口文档生成或修改代码
- 搜索接口文档内容
- 自动生成 API 调用代码
- 自动编写接口测试用例

## 前置条件

在开始配置之前，请确保满足以下条件：

- **Apifox 版本**: ≥ 2.7.2（旧版本没有 MCP 功能入口）
- **Node.js 环境**: 版本 ≥ 18（推荐最新的 LTS 版本）
- **支持 MCP 的 IDE**:
  - Cursor
  - VSCode + Cline 插件
  - Trae
  - Claude Code

## 配置方法

### 方法一：公开发布 API 文档（推荐）

适用于希望 AI 可以直接访问公开发布的在线文档。

> **注意**：仅支持公开发布、任何人都可以直接访问的在线文档，不支持设置了密码、邮箱白名单、IP 白名单的在线文档。

#### 步骤 1：开启 MCP 服务

1. 进入 Apifox 项目
2. 依次点击「分享文档 -> 发布文档站 -> AI 功能」
3. 开启 MCP 服务

![开启 MCP 服务](https://cdn.apifox.com/blog/2025/03/image-45.png)

#### 步骤 2：获取配置文件

1. 开启 MCP 服务后，访问在线文档的接口页面
2. 页面将显示「MCP」或「AI 编程（使用 MCP）」按钮
3. 点击按钮，弹出详细的使用说明和 MCP 配置文件
4. 文件内已自动填充当前文档的 `site-id`
5. 复制该配置用于 IDE 中接入 MCP 服务

![获取配置文件](https://cdn.apifox.com/blog/2025/03/image-47.png)

### 方法二：使用项目内文档（私有）

适用于不希望公开接口文档，仅内部人员可以访问的场景。

需要使用项目 ID 和个人访问令牌读取 Apifox 项目数据。

#### 获取访问令牌

1. 打开 Apifox，点击左侧【头像】→【账号设置】→【API 访问令牌】
2. 点击【新建令牌】，填写令牌名称
3. 点击【保存并生成令牌】，复制令牌

#### 获取项目 ID

1. 打开 Apifox 项目
2. 在项目设置或 URL 中找到项目 ID

## IDE 配置指南

### Cursor 中配置 MCP

#### 全局配置

1. 打开 Cursor 编辑器，点击右上角「设置」图标
2. 选择左侧「MCP」选项
3. 点击「+ Add new global MCP server」按钮
4. 在打开的 `mcp.json` 文件中添加配置

**macOS / Linux:**
```json
{
  "mcpServers": {
    "API 文档": {
      "command": "npx",
      "args": [
        "-y",
        "apifox-mcp-server@latest",
        "--site-id=123456"
      ]
    }
  }
}
```

**Windows:**
```json
{
  "mcpServers": {
    "API 文档": {
      "command": "cmd",
      "args": [
        "/c",
        "npx",
        "-y",
        "apifox-mcp-server@latest",
        "--site-id=123456"
      ]
    }
  }
}
```

#### 项目级配置

在项目根目录下创建 `.cursor` 文件夹，并在其中创建 `mcp.json` 文件，添加上述配置。

### VSCode + Cline 中配置 MCP

1. 在 VSCode 扩展市场搜索并安装 "Cline" 插件
2. 打开 Cline 面板，点击「MCP Servers > Configure MCP Servers」
3. 在打开的 `.json` 文件中添加配置（配置内容与 Cursor 相同）
4. Cline 会自动启用 MCP 服务，也可以手动点击「开关」按钮启用

### Trae 中配置 MCP

1. 打开最新版 Trae 编辑器
2. 点击右上角「AI 侧栏 -> 设置」图标
3. 选择「MCP」选项，点击「+ 添加 MCP Servers」按钮
4. 在 MCP 市场中选择 "手动配置"
5. 在打开的 `mcp.json` 文件中添加配置

### Claude Code 中配置 MCP

1. 打开本地项目，在根目录新建一个 `.mcp.json` 文件
2. 在文件中添加配置（配置内容与 Cursor 相同）

## 使用项目内文档的配置（私有访问）

如果需要访问私有项目文档，使用以下配置：

```json
{
  "mcpServers": {
    "API 文档": {
      "command": "npx",
      "args": [
        "-y",
        "apifox-mcp-server@latest",
        "--project-id=你的项目ID"
      ],
      "env": {
        "APIFOX_ACCESS_TOKEN": "你的访问令牌"
      }
    }
  }
}
```

## 验证配置

配置完成后，可以通过向 AI 提问来验证连接是否正常工作：

```
请通过 MCP 获取 API 文档，并告诉我项目中有几个接口
```

如果 AI 能够返回你 Apifox 项目中的 API 信息，说明连接成功。

**注意**：在 Cursor 中请设置为 Agent 模式。

## 配置多个 API 文档

如果需要使用多个 API 文档，在配置文件中添加多个 MCP Server 即可：

```json
{
  "mcpServers": {
    "DeepSeek API - API 文档": {
      "command": "npx",
      "args": [
        "-y",
        "apifox-mcp-server@latest",
        "--site-id=5435161"
      ]
    },
    "宠物商店 - API 文档": {
      "command": "npx",
      "args": [
        "-y",
        "apifox-mcp-server@latest",
        "--site-id=4997831"
      ]
    }
  }
}
```

## 私有化部署配置

使用私有化部署版本的用户，请在配置中添加 `--apifox-api-base-url` 参数：

```json
{
  "mcpServers": {
    "API 文档": {
      "command": "npx",
      "args": [
        "-y",
        "apifox-mcp-server@latest",
        "--site-id=123456",
        "--apifox-api-base-url=https://你的私有化部署服务器地址"
      ]
    }
  }
}
```

**注意**：请确保网络可以正常访问 `www.npmjs.com`。

## 常见问题

### Q1: Windows 系统配置问题
如果在 Windows 操作系统上配置无法正常工作，请使用以下配置：

```json
{
  "mcpServers": {
    "API 文档": {
      "command": "cmd",
      "args": [
        "/c",
        "npx",
        "-y",
        "apifox-mcp-server@latest",
        "--site-id=123456"
      ]
    }
  }
}
```

### Q2: Node.js 版本问题
如果遇到错误提示 Node.js 版本过低，请确保安装了 18 或更高版本的 Node.js：

```bash
node -v
```

### Q3: API 文档更新后如何同步？
API 文档数据会缓存在本地，如果接口文档有更新，需要告诉 AI 刷新接口文档数据：

```
请重新读取 API 文档数据，在 Pet DTO 里添加 API 文档新增的几个字段
```

### Q4: MCP 按钮不显示？
如果在线文档页面未显示 MCP 按钮，请检查：
- 发布的在线文档是否启用了密码保护、IP 白名单等访问限制
- 在这些受限访问的情况下，按钮将无法显示

### Q5: 如何优化 AI 生成的代码？
在向 AI 提问时，明确 API 名称或接口功能：

❌ **不推荐**："帮我写个请求代码"（描述不明确）

✅ **推荐**："基于 API 文档的 `/orders` 接口，生成 TypeScript API 请求封装"（提供具体信息）

## 最佳实践

### 1. 清晰的 API 命名
使用清晰、描述性的 API 命名，有助于 AI 更准确地理解接口功能。

### 2. 完善的接口文档
确保 API 文档包含：
- 接口描述
- 请求参数说明
- 响应数据格式
- 错误码说明

### 3. 及时同步文档
当 API 文档有更新时，及时通知团队成员刷新 MCP 缓存。

### 4. 结合版本管理
将 MCP 配置文件纳入版本控制，便于团队协作。

### 5. 使用描述性 Server 名称
在配置多个 API 文档时，使用描述性的名称便于区分：

```json
{
  "mcpServers": {
    "用户服务 API": { ... },
    "订单服务 API": { ... },
    "支付服务 API": { ... }
  }
}
```

## 相关资源

- [Apifox 官网](https://apifox.com/)
- [Apifox API Hub](https://apifox.com/apihub) - 所有开放 API 项目已默认支持 MCP
- [Apifox 帮助文档 - MCP 配置](https://docs.apifox.com/6327890m0)
- [GitHub - apifox-mcp-server](https://github.com/apifox/apifox-mcp-server)

## 总结

Apifox MCP 实现了 API 文档与 AI 编程的深度集成：

1. **一分钟快速配置** - 开启 MCP 服务，复制配置到 IDE 即可
2. **文档即代码** - AI 直接读取 API 定义，生成规范代码
3. **自动同步** - 文档更新后，AI 可以获取最新数据
4. **多平台支持** - 支持 Cursor、VSCode、Trae 等主流 IDE
5. **灵活配置** - 支持公开文档和私有项目两种模式

通过 Apifox MCP，AI 成为你的智能助手，告别重复劳动，专注核心业务开发。
