---
author: Ryou
title: opencode 如何方便的配置MCP
date: 2026-02-14T20:10:15+08:00
lastmod: 2026-02-14T20:30:00+08:00
description: 详细介绍如何在 OpenCode 中配置和使用 MCP（Model Context Protocol）扩展 AI 能力
draft: true
mermaid: true
image: https://w.wallhaven.cc/full/ml/wallhaven-mlzgy1.jpg
位置: 上海市 - 上海市
坐标: 
  - 121.4768
  - 31.2243
温度: 14.2℃ 
风速: 5.9 m/s
天气: 晴朗
categories:
  - AI工具
tags:
  - OpenCode
  - MCP
  - AI编程
  - 配置教程
---

> [!summary] 前情提要
> OpenCode 是一款开源的 AI 编程助手，支持通过 MCP（Model Context Protocol）协议连接外部工具和服务，让 AI 能够调用数据库、搜索引擎、监控平台等任意工具，极大地扩展了 AI 的能力边界。

## 什么是 MCP

**MCP（Model Context Protocol，模型上下文协议）** 是一种标准化协议，用于为 AI 模型提供外部上下文和工具访问能力。通过 MCP，OpenCode 可以连接到：

- **本地脚本和工具**：如文件系统操作、代码生成工具
- **远程知识库**：如技术文档搜索、API 文档
- **第三方服务**：如 Sentry 监控、GitHub、数据库等

### MCP 的核心概念

| 概念 | 说明 |
|------|------|
| **MCP 服务器** | 提供工具的外部进程或远程服务 |
| **MCP 工具** | 服务器暴露的具体功能（如搜索、查询、操作） |
| **MCP 客户端** | OpenCode 内置的连接器，自动管理连接 |

### MCP 的工作原理

```
用户提问 → OpenCode → AI 决定调用 MCP 工具 → MCP 服务器执行 → 返回结果
```

## 配置文件位置

OpenCode 的主要配置文件为 `opencode.json`（或 `opencode.jsonc`）：

**macOS / Linux：**
```bash
~/.config/opencode/opencode.json
```

**Windows：**
```bash
%APPDATA%\opencode\opencode.json
```

## 配置 MCP 服务器

### 1. 本地 MCP 服务器配置

本地 MCP 服务器运行在你的机器上，通过 stdio（标准输入输出）通信。

#### 基本配置格式

```json
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "my-local-mcp": {
      "type": "local",
      "command": ["npx", "-y", "@modelcontextprotocol/server-everything"],
      "enabled": true,
      "environment": {
        "MY_ENV_VAR": "value"
      }
    }
  }
}
```

#### 配置选项说明

| 选项 | 类型 | 必填 | 描述 |
|------|------|------|------|
| `type` | String | ✓ | 必须是 `"local"` |
| `command` | Array | ✓ | 命令数组，如 `["npx", "-y", "xxx"]` 或 `["bun", "x", "xxx"]` |
| `environment` | Object | 否 | 环境变量键值对 |
| `enabled` | Boolean | 否 | 是否启用，默认 `true` |
| `timeout` | Number | 否 | 连接超时（毫秒），默认 30000ms |

#### 常用本地 MCP 示例

**shadcn 组件工具：**
```json
{
  "mcp": {
    "shadcn": {
      "type": "local",
      "command": ["npx", "-y", "shadcn@latest", "mcp"],
      "enabled": true
    }
  }
}
```

**测试服务器（@modelcontextprotocol/server-everything）：**
```json
{
  "mcp": {
    "everything": {
      "type": "local",
      "command": ["npx", "-y", "@modelcontextprotocol/server-everything"]
    }
  }
}
```

**sequential-thinking（顺序思考）：**
```json
{
  "mcp": {
    "sequential-thinking": {
      "type": "local",
      "command": ["npx", "-y", "@modelcontextprotocol/server-sequential-thinking"],
      "enabled": true
    }
  }
}
```

**memory-bank（记忆库）：**
```json
{
  "mcp": {
    "memory-bank": {
      "type": "local",
      "command": ["npx", "-y", "@allpepper/memory-bank-mcp"],
      "environment": {
        "MEMORY_BANK_ROOT": "./memory-bank"
      },
      "enabled": true
    }
  }
}
```

### 2. 远程 MCP 服务器配置

远程 MCP 服务器通过 HTTP/HTTPS 协议连接，适用于知识库查询、API 调用等场景。

#### 基本配置格式

```json
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "my-remote-mcp": {
      "type": "remote",
      "url": "https://mcp.example.com/mcp",
      "enabled": true,
      "headers": {
        "Authorization": "Bearer {env:MY_API_KEY}"
      }
    }
  }
}
```

#### 配置选项说明

| 选项 | 类型 | 必填 | 描述 |
|------|------|------|------|
| `type` | String | ✓ | 必须是 `"remote"` |
| `url` | String | ✓ | 远程 MCP 服务器 URL |
| `enabled` | Boolean | 否 | 是否启用，默认 `true` |
| `headers` | Object | 否 | 自定义请求头，支持 `{env:VAR}` 环境变量语法 |
| `oauth` | Object/false | 否 | OAuth 配置或设置为 `false` 禁用 OAuth |
| `timeout` | Number | 否 | 连接超时（毫秒），默认 30000ms |

#### 常用远程 MCP 示例

**Context7（文档搜索）：**
```json
{
  "mcp": {
    "context7": {
      "type": "remote",
      "url": "https://mcp.context7.com/mcp",
      "enabled": true,
      "headers": {
        "CONTEXT7_API_KEY": "{env:CONTEXT7_API_KEY}"
      }
    }
  }
}
```

**Grep by Vercel（GitHub 代码搜索）：**
```json
{
  "mcp": {
    "gh_grep": {
      "type": "remote",
      "url": "https://mcp.grep.app"
    }
  }
}
```

**Sentry（错误监控）：**
```json
{
  "mcp": {
    "sentry": {
      "type": "remote",
      "url": "https://mcp.sentry.dev/mcp",
      "oauth": {}
    }
  }
}
```

### 3. 混合配置多个 MCP

你可以同时配置多个本地和远程 MCP 服务器：

```json
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "shadcn": {
      "type": "local",
      "command": ["npx", "-y", "shadcn@latest", "mcp"],
      "enabled": true
    },
    "context7": {
      "type": "remote",
      "url": "https://mcp.context7.com/mcp",
      "enabled": true,
      "headers": {
        "CONTEXT7_API_KEY": "{env:CONTEXT7_API_KEY}"
      }
    },
    "gh_grep": {
      "type": "remote",
      "url": "https://mcp.grep.app"
    }
  }
}
```

## OAuth 认证配置

OpenCode 自动处理远程 MCP 服务器的 OAuth 认证。

### 自动认证（推荐）

大多数支持 OAuth 的 MCP 服务器无需特殊配置：

```json
{
  "mcp": {
    "my-oauth-server": {
      "type": "remote",
      "url": "https://mcp.example.com/mcp"
    }
  }
}
```

当服务器需要认证时，OpenCode 会自动提示你进行授权。

### 预注册客户端

如果你有客户端凭证，可以预先配置：

```json
{
  "mcp": {
    "my-oauth-server": {
      "type": "remote",
      "url": "https://mcp.example.com/mcp",
      "oauth": {
        "clientId": "{env:MY_MCP_CLIENT_ID}",
        "clientSecret": "{env:MY_MCP_CLIENT_SECRET}",
        "scope": "tools:read tools:execute"
      }
    }
  }
}
```

### 禁用 OAuth

如果服务器使用 API Key 而非 OAuth，禁用自动 OAuth：

```json
{
  "mcp": {
    "my-api-key-server": {
      "type": "remote",
      "url": "https://mcp.example.com/mcp",
      "oauth": false,
      "headers": {
        "Authorization": "Bearer {env:MY_API_KEY}"
      }
    }
  }
}
```

## MCP 管理命令

### 查看 MCP 状态

```bash
# 列出所有 MCP 服务器及其状态
opencode mcp list

# 查看详细认证状态
opencode mcp auth list
```

### 手动认证

```bash
# 触发特定服务器的 OAuth 认证
opencode mcp auth <server-name>

# 示例
opencode mcp auth sentry
```

### 退出登录

```bash
# 清除特定服务器的认证信息
opencode mcp logout <server-name>

# 示例
opencode mcp logout sentry
```

### 调试连接

```bash
# 调试特定服务器的连接和 OAuth 流程
opencode mcp debug <server-name>

# 示例
opencode mcp debug context7
```

### 快速验证配置

在 OpenCode 对话中输入：

```
/mcps
```

这将显示当前已加载的所有 MCP 服务及其状态。

## MCP 连接状态

MCP 连接有 5 种状态：

| 状态 | 说明 |
|------|------|
| `connected` | 已连接，工具可用 |
| `disabled` | 配置中 `enabled: false`，未连接 |
| `failed` | 连接失败，查看错误信息 |
| `needs_auth` | 需要 OAuth 认证 |
| `needs_client_registration` | 需要预注册客户端 ID |

## 使用 MCP 工具

配置完成后，MCP 工具会自动作为 OpenCode 的内置工具可用。你可以通过以下方式使用：

### 方式 1：在提示词中指定

```
use the shadcn tool to find Button component usage
```

```
use context7 to search for React hooks best practices
```

```
use the gh_grep tool to find JWT implementation examples in Node.js
```

### 方式 2：在 AGENTS.md 中配置规则

在项目根目录创建 `AGENTS.md` 文件：

```markdown
When you need to search docs, use `context7` tools.

If you are unsure how to do something, use `gh_grep` to search code examples from GitHub.
```

### 方式 3：全局禁用/启用

在 `opencode.json` 中配置：

```json
{
  "tools": {
    "shadcn": false,
    "context7": true
  }
}
```

### 方式 4：Agent 级别配置

为特定 Agent 启用特定的 MCP：

```json
{
  "mcp": {
    "my-mcp": {
      "type": "local",
      "command": ["bun", "x", "my-mcp-command"],
      "enabled": true
    }
  },
  "tools": {
    "my-mcp*": false
  },
  "agent": {
    "my-agent": {
      "tools": {
        "my-mcp*": true
      }
    }
  }
}
```

## 注意事项与最佳实践

### ⚠️ 重要提醒

1. **上下文消耗**：MCP 服务器会增加上下文消耗，工具越多 token 消耗越快
2. **谨慎选择**：某些 MCP（如 GitHub）会添加大量 token，容易超出上下文限制
3. **按需启用**：建议只启用必要的 MCP 服务器

### 最佳实践

1. **为 MCP 起描述性名称**：便于在对话中引用
   ```json
   {
     "mcp": {
       "shadcn-ui": { ... },
       "docs-search": { ... }
     }
   }
   ```

2. **使用环境变量**：敏感信息（API Key、Token）使用环境变量
   ```json
   {
     "headers": {
       "Authorization": "Bearer {env:MY_API_KEY}"
     }
   }
   ```

3. **合理设置超时**：根据网络情况调整 `timeout`
   ```json
   {
     "timeout": 60000
   }
   ```

4. **分项目配置**：为不同项目配置不同的 MCP 组合

## 故障排查

### 问题 1：`/mcps` 命令无输出

**解决方案：**
```bash
# 验证配置文件格式
python3 -m json.tool ~/.config/opencode/opencode.json

# 检查文件权限
ls -la ~/.config/opencode/

# 查看日志
tail -f ~/.config/opencode/logs/latest.log
```

### 问题 2：本地 MCP 启动失败

**解决方案：**
```bash
# 确认命令可用
which npx
node --version  # 需要 v16+

# 手动测试命令
npx -y @modelcontextprotocol/server-everything
```

### 问题 3：远程 MCP 认证失败

**解决方案：**
```bash
# 使用 curl 测试 API Key
curl -H "Authorization: Bearer YOUR_API_KEY" \
     https://mcp.context7.com/mcp

# 手动触发认证
opencode mcp auth <server-name>

# 调试连接
opencode mcp debug <server-name>
```

### 问题 4：MCP 加载但不生效

1. 确认 `"enabled": true`
2. 查看日志是否有错误
3. 尝试明确询问：`use <mcp-name> tool to ...`

## 常见 MCP 服务器推荐

| 名称 | 类型 | 用途 |
|------|------|------|
| **shadcn** | 本地 | shadcn/ui 组件库工具 |
| **context7** | 远程 | 技术文档搜索 |
| **gh_grep** | 远程 | GitHub 代码搜索 |
| **sentry** | 远程 | 错误监控平台 |
| **sequential-thinking** | 本地 | 顺序思考辅助 |
| **memory-bank** | 本地 | 跨会话记忆 |
| **@modelcontextprotocol/server-filesystem** | 本地 | 文件系统操作 |
| **@modelcontextprotocol/server-github** | 远程 | GitHub API 操作 |
| **@modelcontextprotocol/server-postgres** | 本地 | PostgreSQL 数据库 |

## 总结

OpenCode 的 MCP 支持让你能够：

1. **轻松连接外部工具**：本地脚本、远程 API、第三方服务
2. **灵活的认证方式**：OAuth、API Key、环境变量
3. **智能的工具管理**：自动发现、按需启用、Agent 级别配置
4. **丰富的生态**：大量现成的 MCP 服务器可用

通过合理配置 MCP，你可以让 OpenCode 的 AI 助手具备几乎无限的能力，从代码搜索到数据库操作，从文档查询到错误监控，真正实现"一个 AI 助手，连接整个世界"。

## 相关资源

- [OpenCode 官方文档 - MCP](https://opencode.ai/docs/mcp-servers/)
- [OpenCode GitHub](https://github.com/anomalyco/opencode)
- [MCP 协议规范](https://modelcontextprotocol.io/docs)
- [OpenCode 中文教程](https://learnopencode.com/)
- [Awesome MCP Servers](https://github.com/punkpeye/awesome-mcp-servers)
