---
author: Ryou
title: HOOK技术
date: 2026-02-13T15:29:40+08:00
lastmod: 2026-02-13T15:31:25+08:00
description: 
draft: true
mermaid: true
image: https://w.wallhaven.cc/full/og/wallhaven-ogy6zm.jpg
位置: 上海市 - 上海市
坐标: 
  - 121.4768
  - 31.2243
温度: 19.7℃ 
风速: 4 m/s
天气: 主要多云
categories:
  - 
tags:
  - 
---


>[!summary] 前情提要
>- 无处不在的 HOOK 技术 有点头痛。
我来为您详细梳理不同领域的 HOOK 技术。首先进行联网检索获取最新权威信息。

```mermaid
flowchart TD
    A["HOOK 技术"] --> B["逆向安全 HOOK"]
    A --> C["运维 DevOps HOOK"]
    A --> D["前端渲染 HOOK"]
    A --> E["后端开发 HOOK"]
    
    B --> B1["IAT Hook"]
    B --> B2["EAT Hook"]
    B --> B3["Inline Hook"]
    B --> B4["VEH/SEH Hook"]
    
    C --> C1["Webhooks"]
    C --> C2["Git Hooks"]
    C --> C3["CI/CD Hooks"]
    
    D --> D1["React Hooks"]
    D --> D2["Vue Composables"]
    D --> D3["生命周期钩子"]
    
    E --> E1["中间件 Hook"]
    E --> E2["框架扩展点"]
    E --> E3["事件驱动 Hook"]
```

---

### 1. 核心概念：什么是 HOOK？

**HOOK（钩子）** 是一种**扩展点机制**，允许在特定事件发生或特定条件满足时，插入自定义逻辑。

```mermaid
flowchart LR
    A["原始流程"] --> B{"事件/条件触发?"}
    B -->|否| C["继续执行"]
    B -->|是| D["HOOK 拦截"]
    D --> E["执行自定义逻辑"]
    E --> F["返回继续执行"]
    
    style D fill:#ff6b6b,color:#fff
    style E fill:#feca57
```

**核心思想**：**劫持** → **干预** → **放行/替代**

---

### 2. 逆向安全领域的 HOOK 技术

```mermaid
flowchart TB
    subgraph Process["进程空间"]
        subgraph Kernel["内核态"]
            K1["系统调用"]
        end
        
        subgraph User["用户态"]
            U1["PEB/TEB"]
            U2["导入表 IAT"]
            U3["导出表 EAT"]
            U4["代码段"]
        end
    end
    
    K1 -.->|"Syscall"| U4
    
    U2 -->|"IAT Hook"| U4
    U3 -->|"EAT Hook"| U4
    U4 -->|"Inline Hook"| U4
    
    style Process fill:#2d3436,color:#fff
```

#### 2.1 IAT Hook（导入地址表钩子）

**原理**：修改 PE 文件的导入地址表（IAT），替换函数指针。

```mermaid
sequenceDiagram
    participant App as 应用程序
    participant IAT as 导入地址表
    participant Orig as 原始函数
    participant Hook as HOOK函数
    
    App->>IAT: 调用 GetMessage
    IAT->>Hook: 跳转执行 HOOK 函数
    Hook->>App: 处理/修改后返回
    Hook->>Orig: 可选调用原函数
```

**代码示例（伪代码）**：
```c
// 保存原始函数指针
OrigGetMessage = (GetMessage_t)GetProcAddress(hUser32, "GetMessageW");

// 替换为 HOOK 函数
*pGetMessageW = (FARPROC)MyGetMessageW;
```

**特点**：
- ✅ 易于实现
- ✅ 稳定可靠
- ❌ 只能 Hook 导入表中的函数

---

#### 2.2 EAT Hook（导出地址表钩子）

**原理**：修改 DLL 的导出地址表，改变函数入口。

```mermaid
flowchart TD
    A["调用 DLL 函数"] --> B{"首次调用?"}
    B -->|是| C["获取 EAT"]
    B -->|否| F["直接调用"]
    C --> D["修改导出地址"]
    D --> E["指向恶意代码"]
    E --> F
    
    style D fill:#ff6b6b,color:#fff
```

**应用场景**：
- 恶意软件替换系统 DLL 导出函数
- 软件破解/补丁

---

#### 2.3 Inline Hook（内联钩子）

**原理**：直接修改目标函数的机器码，跳转到自定义函数。

```mermaid
flowchart TD
    subgraph Before["原始代码"]
        B1["push ebp"]
        B2["mov ebp, esp"]
        B3["sub esp, 40h"]
    end
    
    subgraph After["修改后"]
        A1["push ebp"]
        A2["jmp HOOK_Func"]
        A3["(原始指令...)"]
    end
    
    B1 --> A1
    B2 --> A2
    B3 --> A3
```

**实现步骤**：
1. 保存原始指令（前 5-7 字节）
2. 写入跳转指令（JMP XXXXXXXX）
3. 执行自定义逻辑
4. 可选：恢复原始指令并调用原函数

**特点**：
- ✅ 可 Hook 任意函数（包括内部函数）
- ✅ 灵活性最高
- ❌ 实现复杂，需要处理指令长度
- ❌ 可能被检测

---

#### 2.4 VEH/SEH Hook（异常处理钩子）

**原理**：利用 Vectored Exception Handler / Structured Exception Handler 拦截异常。

```mermaid
flowchart TB
    A["异常发生"] --> B["VEH 链表"]
    B --> C{"处理?"}
    C -->|是| D["执行处理函数"]
    C -->|否| E["SEH 链"]
    E --> F["程序崩溃"]
    
    style B fill:#feca57
    style D fill:#ff6b6b,color:#fff
```

---

#### 2.5 应用场景

| 场景 | 说明 |
|------|------|
| 🛡️ **安全软件** | 杀毒软件 HOOK 系统调用进行监控 |
| 🎮 **游戏辅助** | 读取/修改游戏内存 |
| 🔧 **调试工具** | 函数拦截用于调试 |
| 🔒 **软件保护** | 检测调试器/修改器 |

* 参考来源：[Understanding Hooking Techniques - Mandiant](https://www.mandiant.com/resources/blog)

---

### 3. 运维 DevOps 领域的 HOOK

```mermaid
flowchart LR
    subgraph Event["事件源"]
        E1["Git Push"]
        E2["PR 创建"]
        E3["镜像构建"]
    end
    
    subgraph Hook["Webhook"]
        H1["HTTP POST"]
        H2["JSON Payload"]
    end
    
    subgraph Action["执行动作"]
        A1["CI/CD"]
        A2["通知"]
        A3["部署"]
    end
    
    E1 --> H1
    E2 --> H1
    E3 --> H1
    H1 --> A1
    H1 --> A2
    H1 --> A3
```

#### 3.1 Webhooks（Web 钩子）

**定义**：通过 HTTP 回调实现的自动化消息推送机制。

```mermaid
sequenceDiagram
    participant User as 用户
    participant Git as 代码仓库
    participant Webhook as Webhook 服务器
    participant CI as CI/CD 系统
    
    User->>Git: 推送代码
    Git->>Webhook: POST /webhook (JSON)
    Webhook->>CI: 触发构建
    CI->>Webhook: 返回结果
    Webhook->>Git: 更新状态
    
    Note over Git,CI: 自动触发后续流程
```

**常见应用**：
- GitHub/GitLab Webhooks → 触发 CI/CD
- 支付回调（支付宝/微信）
- 自动化测试触发

**配置示例**（GitHub）：
```yaml
# .github/workflows/example.yml
on:
  push:
    branches: [main]
  pull_request:
    branches: [main]
```

* 参考来源：[About Webhooks - GitHub Docs](https://docs.github.com/en/webhooks)

---

#### 3.2 Git Hooks

**定义**：Git 在执行特定操作时执行的脚本。

```mermaid
flowchart TB
    subgraph Local["本地 Hooks"]
        L1["pre-commit"]
        L2["commit-msg"]
        L3["post-commit"]
    end
    
    subgraph Server["服务端 Hooks"]
        S1["pre-receive"]
        S2["update"]
        S3["post-receive"]
    end
    
    L1 -->|"代码检查"| L2
    L2 -->|"验证提交信息"| L3
    L3 -->|"通知"| S3
    
    style L1 fill:#00add8,color:#fff
    style S3 fill:#00add8,color:#fff
```

**常用 Git Hooks**：

| Hook | 阶段 | 用途 |
|------|------|------|
| `pre-commit` | 提交前 | 代码格式检查、单元测试 |
| `commit-msg` | 提交信息 | 规范提交信息格式 |
| `pre-push` | 推送前 | 运行完整测试 |
| `post-receive` | 接收后 | 自动部署 |

**使用示例**：
```bash
# .git/hooks/pre-commit
#!/bin/sh
npm run lint
npm test
```

* 参考来源：[Git Hooks - Pro Git Book](https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks)

---

#### 3.3 CI/CD Pipeline Hooks

```mermaid
flowchart LR
    A["代码提交"] --> B["触发 Pipeline"]
    B --> C["Build"]
    C --> D{"测试通过?"}
    D -->|是| E["Deploy Staging"]
    D -->|否| F["通知失败"]
    E --> G{"审批?"}
    G -->|是| H["Deploy Prod"]
    G -->|否| I["等待"]
    
    style B fill:#00add8,color:#fff
    style H fill:#00add8,color:#fff
```

---

### 4. 前端渲染中的 HOOK

#### 4.1 React Hooks（React 核心机制）

**定义**：Hook 是让你在函数组件中"钩入"状态和副作用的函数。

```mermaid
flowchart TD
    subgraph Component["函数组件"]
        C1["渲染"]
        C2["副作用"]
        C3["状态"]
    end
    
    subgraph Hooks["React Hooks"]
        H1["useState"]
        H2["useEffect"]
        H3["useContext"]
        H4["useReducer"]
    end
    
    C1 -->|读取| H3
    C2 -->|管理| H2
    C3 -->|管理| H1
    
    style Hooks fill:#61dafb,color:#000
```

**核心 Hooks**：

```jsx
import { useState, useEffect, useContext } from 'react';

function Counter() {
    // 状态 Hook
    const [count, setCount] = useState(0);
    
    // 副作用 Hook
    useEffect(() => {
        document.title = `点击 ${count} 次`;
        // 清理函数
        return () => {
            console.log('清理副作用');
        };
    }, [count]);  // 依赖数组
    
    return <button onClick={() => setCount(count + 1)}>{count}</button>;
}
```

**Hook 规则**：
1. ⚠️ **只在顶层调用** - 不能在循环/条件中调用
2. ⚠️ **只在 React 函数中调用** - 函数组件或自定义 Hook

* 参考来源：[React Hooks Official Docs](https://react.dev/reference/react)

---

#### 4.2 Vue Composition API（Vue 3）

```mermaid
flowchart TD
    subgraph Options["Options API"]
        O1["data()"]
        O2["methods"]
        O3["computed"]
        O4["watch"]
    end
    
    subgraph Composition["Composition API"]
        C1["ref/reactive"]
        C2["函数"]
        C3["computed"]
        C4["watchEffect"]
    end
    
    O1 -.->|重构| C1
    O2 -.->|重构| C2
    O3 -.->|重构| C3
    O4 -.->|重构| C4
```

**Vue Hook 形式**：
```javascript
import { ref, onMounted, onUnmounted } from 'vue';

export function useMouse() {
    const x = ref(0);
    const y = ref(0);
    
    function update(event) {
        x.value = event.pageX;
        y.value = event.pageY;
    }
    
    onMounted(() => window.addEventListener('mousemove', update));
    onUnmounted(() => window.removeEventListener('mousemove', update));
    
    return { x, y };
}
```

---

#### 4.3 前端生命周期钩子

```mermaid
flowchart TB
    subgraph Lifecycle["组件生命周期"]
        LB1["beforeCreate"]
        LB2["created"]
        LB3["beforeMount"]
        LB4["mounted"]
        LB5["beforeUpdate"]
        LB6["updated"]
        LB7["beforeUnmount"]
        LB8["unmounted"]
    end
    
    LB1 --> LB2
    LB2 --> LB3
    LB3 --> LB4
    LB4 --> LB5
    LB5 --> LB6
    LB6 --> LB7
    LB7 --> LB8
    
    style LB4 fill:#42b883,color:#fff
    style LB8 fill:#42b883,color:#fff
```

---

### 5. 后端开发中的 HOOK

#### 5.1 中间件模式（Middleware Hook）

```mermaid
flowchart LR
    subgraph Request["HTTP 请求"]
        R1["Method"]
        R2["Path"]
        R3["Headers"]
        R4["Body"]
    end
    
    subgraph Middleware["中间件链"]
        M1["Logger"]
        M2["Auth"]
        M3["Validation"]
        M4["Router"]
    end
    
    subgraph Handler["业务处理"]
        H1["Controller"]
        H2["Service"]
        H3["Model"]
    end
    
    R1 --> M1
    M1 --> M2
    M2 --> M3
    M3 --> M4
    M4 --> H1
    H1 --> H2
    H2 --> H3
    
    style Middleware fill:#ff9f43,color:#fff
```

**Go Gin 框架示例**：
```go
func main() {
    r := gin.Default()
    
    // 全局中间件
    r.Use(Logger())
    r.Use(Recovery())
    
    // 路由组中间件
    api := r.Group("/api")
    api.Use(AuthRequired()) {
        api.GET("/users", getUsers)
    }
    
    r.Run(":8080")
}

// 自定义中间件
func Logger() gin.HandlerFunc {
    return func(c *gin.Context) {
        start := time.Now()
        path := c.Request.URL.Path
        
        c.Next()  // 处理请求
        
        latency := time.Since(start)
        status := c.Writer.Status()
        fmt.Printf("%s %s %d %v\n", c.Request.Method, path, status, latency)
    }
}
```

---

#### 5.2 框架扩展点 HOOK

```mermaid
flowchart TD
    subgraph Framework["框架核心"]
        F1["启动"]
        F2["请求入口"]
        F3["响应出口"]
        F4["关闭"]
    end
    
    subgraph Hooks["扩展点"]
        H1["onStart"]
        H2["onRequest"]
        H3["onResponse"]
        H4["onShutdown"]
    end
    
    F1 --> H1
    F2 --> H2
    F3 --> H3
    F4 --> H4
```

**Node.js Express 示例**：
```javascript
const express = require('express');
const app = express();

// 请求开始时执行
app.use((req, res, next) => {
    console.log('请求开始:', req.method, req.url);
    next();
});

// 路由处理器
app.get('/api', (req, res) => {
    res.json({ message: 'Hello' });
});

// 响应结束时的 Hook
app.use((req, res, next) => {
    res.on('finish', () => {
        console.log('响应完成:', res.statusCode);
    });
    next();
});
```

---

#### 5.3 事件驱动 HOOK

```mermaid
flowchart TB
    subgraph EventSystem["事件系统"]
        E1["EventEmitter"]
        E2["发布/订阅"]
        E3["异步处理"]
    end
    
    subgraph Events["常见事件"]
        Ev1["connection"]
        Ev2["message"]
        Ev3["error"]
        Ev4["close"]
    end
    
    E1 --> Ev1
    E1 --> Ev2
    E1 --> Ev3
    E1 --> Ev4
    
    style EventSystem fill:#e056fd,color:#fff
```

**Node.js 事件驱动示例**：
```javascript
const EventEmitter = require('events');
const emitter = new EventEmitter();

// 注册 HOOK（监听器）
emitter.on('user:login', (user) => {
    console.log('用户登录:', user.name);
    // 发送欢迎邮件
    sendWelcomeEmail(user.email);
});

emitter.on('user:login', (user) => {
    console.log('记录登录日志');
    // 写入日志
    logger.info(`User ${user.id} logged in`);
});

// 触发事件
emitter.emit('user:login', { id: 1, name: 'Alice', email: 'alice@example.com' });
```

---

### 6. 总结：HOOK 的统一本质

```mermaid
flowchart TD
    A["HOOK 本质"] --> B["事件监听"]
    A --> C["函数拦截"]
    A --> D["扩展点注册"]
    
    B --> B1["前端: onClick"]
    B --> B2["后端: EventEmitter"]
    B --> C3["运维: Webhook"]
    
    C --> C1["安全: Inline Hook"]
    C --> C2["安全: IAT Hook"]
    
    D --> D1["中间件"]
    D --> D2["生命周期钩子"]
    D --> D3["Git Hooks"]
    
    style A fill:#ff6b6b,color:#fff
    style B1 fill:#61dafb,color:#000
    style C1 fill:#ff9f43,color:#fff
    style D1 fill:#42b883,color:#fff
```

| 领域 | HOOK 形式 | 作用 |
|------|-----------|------|
| 🔒 **安全/逆向** | 代码注入、函数劫持 | 监控、修改行为 |
| 🛠️ **运维/DevOps** | Webhook、Git Hook | 自动化触发 |
| 🎨 **前端** | React Hooks、生命周期 | 状态/副作用管理 |
| ⚙️ **后端** | 中间件、事件钩子 | 请求预处理/扩展 |

---

### 参考链接

1. [Understanding Hooking Techniques - Mandiant](https://www.mandiant.com/resources/blog) — 安全逆向技术
2. [React Hooks Documentation](https://react.dev/reference/react) — React 官方文档
3. [Git Hooks - Pro Git Book](https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks) — Git 官方文档
4. [Webhooks - GitHub Docs](https://docs.github.com/en/webhooks) — Webhook 配置
5. [Inline Hooking - OpenSecurityTraining](https://opensecuritytraining.info/) — 内联Hook技术
6. [Go Gin Middleware](https://gin-gonic.com/docs/examples/using-middleware/) — Go 中间件示例

---

> 💡 **一句话总结**：HOOK 就是**"在特定时机插入自定义逻辑的机制"**，不同领域的叫法不同，但本质相通。

