---
author: Ryou
title: redis数据库
date: 2026-02-13T15:59:47+08:00
lastmod: 
description: 
draft: true
mermaid: true
image: https://w.wallhaven.cc/full/og/wallhaven-ogyeol.jpg
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

```mermaid
flowchart TD
    A["Redis 数据库"] --> B["内存存储"]
    A --> C["NoSQL 数据库"]
    A --> D["数据结构服务器"]
    A --> E["键值存储"]
```

---

### 1. Redis 核心概念

#### 1.1 什么是 Redis？

**Redis（Remote Dictionary Server）** 是一个开源的**内存数据结构存储系统**，可用作数据库、缓存、消息队列。

```mermaid
flowchart LR
    subgraph Storage["存储层"]
        M["内存 (Memory)"]
        D["磁盘 (Disk)"]
    end
    
    subgraph Redis["Redis 引擎"]
        R1["数据持久化"]
        R2["高可用"]
        R3["集群"]
    end
    
    M --> R1
    D --> R1
    R1 --> R2
    R2 --> R3
    
    style M fill:#ff6b6b,color:#fff
    style Redis fill:#00add8,color:#fff
```

**核心特性**：
- 📦 **内存存储** → 极高性能
- 💾 **持久化** → 数据不丢失
- 🔄 **主从复制** → 高可用
- 🌀 **集群** → 水平扩展

* 参考来源：[Redis Official Website](https://redis.io/)

---

#### 1.2 Redis 数据结构

```mermaid
flowchart TD
    subgraph DataTypes["Redis 数据类型"]
        S1["String<br>字符串"]
        S2["Hash<br>哈希"]
        S3["List<br>列表"]
        S4["Set<br>集合"]
        S5["Sorted Set<br>有序集合"]
        S6["Bitmap<br>位图"]
        S7["HyperLogLog<br>基数统计"]
        S8["Geospatial<br>地理空间"]
        S9["Stream<br>流"]
    end
    
    S1 --> S2
    S2 --> S3
    S3 --> S4
    S4 --> S5
    S5 --> S6
    
    style DataTypes fill:#dc143c,color:#fff
```

| 类型 | 说明 | 典型场景 |
|------|------|----------|
| **String** | 字符串/数字/JSON | 缓存、计数器、Session |
| **Hash** | 哈希表对象 | 存储对象 |
| **List** | 双向链表 | 消息队列、最新列表 |
| **Set** | 无序集合 | 标签、好友关系 |
| **Sorted Set** | 有序集合 | 排行榜、权重队列 |

* 参考来源：[Redis Data Types](https://redis.io/docs/data-types/)

---

### 2. 键命名规范深度解析

#### 2.1 为什么需要命名规范？

用户问：`entity:identifier:sub_entity:field` 这种格式看不懂

让我用**现实生活的例子**来解释：

```mermaid
flowchart TD
    subgraph Traditional["传统数据库 (SQL)"]
        T1["表: users"]
        T2["字段: id, name, email"]
        T3["行: 1, Alice, alice@email"]
    end
    
    subgraph Redis["Redis (NoSQL)"]
        R1["键: user:1:name"]
        R2["值: Alice"]
        R3["键: user:1:email"]
        R4["值: alice@email"]
    end
    
    T1 --> R1
    T2 --> R2
    T3 --> R4
    
    style Redis fill:#dc143c,color:#fff
```

**Redis 是键值存储**，没有"表"的概念，**所有数据都是键值对**：
- 键（Key）= 数据的唯一标识
- 值（Value）= 存储的数据

---

#### 2.2 命名格式解释：`entity:identifier:sub_entity:field`

这是 Redis 社区推荐的**命名空间规范**，用冒号 `:` 分隔层次。

```mermaid
flowchart TB
    subgraph KeyFormat["键格式"]
        F1["entity"] 
        F2["identifier"]
        F3["sub_entity"]
        F4["field"]
    end
    
    subgraph Explanation["含义"]
        E1["实体类型<br>(是什么)"]
        E2["实体ID<br>(哪个)"]
        E3["子实体<br>(关联)"]
        E4["具体字段<br>(属性)"]
    end
    
    F1 --> E1
    F2 --> E2
    F3 --> E3
    F4 --> E4
```

---

#### 2.3 具体例子

##### 示例 1：用户数据

```mermaid
flowchart TD
    subgraph User1["用户 Alice (ID=1)"]
        K1["user:1:name"]
        V1["Alice"]
        
        K2["user:1:email"]
        V2["alice@example.com"]
        
        K3["user:1:age"]
        V3["25"]
        
        K4["user:1:city"]
        V4["Shanghai"]
    end
    
    subgraph User2["用户 Bob (ID=2)"]
        K5["user:2:name"]
        V5["Bob"]
    end
    
    K1 --> V1
    K2 --> V2
    K3 --> V3
    K4 --> V4
    K5 --> V5
    
    style User1 fill:#e3f2fd
    style User2 fill:#e3f2fd
```

**键值对示例**：

```
┌─────────────────────┬──────────────────┐
│ 键 (Key)            │ 值 (Value)       │
├─────────────────────┼──────────────────┤
│ user:1:name         │ Alice            │
│ user:1:email        │ alice@example.com│
│ user:1:age          │ 25               │
│ user:1:city         │ Shanghai         │
│ user:2:name         │ Bob              │
└─────────────────────┴──────────────────┘
```

**解读**：
- `user` = **entity**（实体类型：用户）
- `1` = **identifier**（实体ID：1号用户）
- `name` = **field**（字段：名字）

---

##### 示例 2：商品数据

```
┌─────────────────────────────────┬──────────────────┐
│ 键 (Key)                        │ 值 (Value)       │
├─────────────────────────────────┼──────────────────┤
│ product:100:title               │ iPhone 15        │
│ product:100:price               │ 999              │
│ product:100:stock               │ 50               │
│ product:100:category            │ phone            │
├─────────────────────────────────┼──────────────────┤
│ product:101:title               │ MacBook Pro      │
│ product:101:price               │ 1999             │
│ product:101:stock               │ 20               │
└─────────────────────────────────┴──────────────────┘
```

---

##### 示例 3：订单 + 订单项（复杂场景）

```mermaid
flowchart TB
    subgraph OrderData["订单数据"]
        O1["order:1001:user_id"]
        O1V["1"]
        O2["order:1001:total"]
        O2V["2999"]
        O3["order:1001:status"]
        O3V["paid"]
    end
    
    subgraph OrderItems["订单项（子实体）"]
        I1["order:1001:item:1:product_id"]
        I1V["100"]
        I2["order:1001:item:1:quantity"]
        I2V["2"]
        I3["order:1001:item:2:product_id"]
        I3V["101"]
        I4["order:1001:item:2:quantity"]
        I4V["1"]
    end
    
    O1 --> O1V
    O2 --> O2V
    I1 --> I1V
```

**完整结构**：
```
# 订单主信息
order:1001:user_id      → 1
order:1001:total       → 2999
order:1001:status      → paid
order:1001:created_at → 2024-01-15 10:30:00

# 订单项 1
order:1001:item:1:product_id  → 100
order:1001:item:1:quantity     → 2
order:1001:item:1:price        → 1999

# 订单项 2  
order:1001:item:2:product_id  → 101
order:1001:item:2:quantity     → 1
order:1001:item:2:price        → 999
```

---

#### 2.4 为什么用冒号 `:` 分隔？

```mermaid
flowchart LR
    A["键: user:1:name"] --> B["Redis 支持<br>通配符匹配"]
    B --> C["KEYS user:1:*"]
    B --> D["SCAN 0 MATCH user:1:*"]
    
    C --> E["找到用户1的所有字段"]
    D --> E
    
    style B fill:#ffd700
```

**优势**：

| 优势 | 说明 |
|------|------|
| 🔍 **便于搜索** | `KEYS user:1:*` 匹配所有用户1的键 |
| 📊 **便于管理** | 按前缀分类，易于查看 |
| 🏗️ **层次清晰** | 表达实体关系 |
| 🔧 **工具支持** | Redis Desktop Manager 等工具支持树形展示 |

---

#### 2.5 实际项目中的命名约定

```mermaid
flowchart TD
    subgraph Prefix["常见前缀"]
        P1["user:"]     -->|"用户"| U1["用户数据"]
        P2["order:"]    -->|"订单"| U2["订单数据"]
        P3["product:"]  -->|"商品"| U3["商品数据"]
        P4["session:"]  -->|"会话"| U4["Session"]
        P5["cache:"]    -->|"缓存"| U5["缓存数据"]
        P6["rate:"]     -->|"限流"| U6["限流器"]
        P7["lock:"]     -->|"锁"| U7["分布式锁"]
    end
    
    U1 --> E1["user:1001:profile"]
    U2 --> E2["order:20240115:total"]
    U3 --> E3["product:sku:iphone15"]
    U4 --> E4["session:abc123:data"]
    U5 --> E5["cache:page:/home"]
    U6 --> E6["rate:ip:192.168.1.1"]
    U7 --> E7["lock:order:1001"]
```

**常见实践**：

```bash
# 用户模块
user:1001:name
user:1001:email  
user:1001:profile
user:1001:friends          # Set - 好友列表

# 缓存模块
cache:user:1001:profile     # 用户资料缓存
cache:product:100:detail    # 商品详情缓存

# 会话模块
session:abc123:token
session:abc123:data
session:abc123:expire

# 计数器
counter:product:100:views   # 商品浏览量
counter:user:1001:login     # 登录次数

# 排行榜
rank:score:global           # Sorted Set - 全局排行榜
rank:score:daily:20240115   # Sorted Set - 每日排行榜
```

---

### 3. Redis 的实际使用方式

#### 3.1 命令行示例

```bash
# 存储用户数据
SET user:1:name "Alice"
SET user:1:email "alice@example.com"
SET user:1:age 25

# 批量获取
MGET user:1:name user:1:email user:1:age

# 使用 Hash 存储对象（更推荐）
HSET user:1 name "Alice"
HSET user:1 email "alice@example.com"
HSET user:1 age 25

# 获取整个用户对象
HGETALL user:1

# 模糊匹配
KEYS user:1:*
```

#### 3.2 Hash vs String 存储对比

```mermaid
flowchart LR
    subgraph String["String 存储"]
        S1["user:1:name"]
        S2["user:1:email"]
        S3["user:1:age"]
    end
    
    subgraph Hash["Hash 存储"]
        H1["user:1"]
        H1 --> H2["name: Alice"]
        H1 --> H3["email: ..."]
        H1 --> H4["age: 25"]
    end
    
    S1 -->|"拆成多个键"| H1
    
    style Hash fill:#00add8,color:#fff
```

**Hash 更适合存储对象**：

```bash
# String 方式（不推荐用于对象）
SET user:1:name "Alice"
SET user:1:email "alice@example.com"
# 3个键，分散存储

# Hash 方式（推荐）
HSET user:1 name "Alice" email "alice@example.com" age 25
# 1个键，内部字段管理
```

---

### 4. Redis vs 传统数据库

```mermaid
flowchart TD
    subgraph SQL["传统数据库 (MySQL/PostgreSQL)"]
        S1["表: users"]
        S2["字段: id, name, email, age"]
        S3["行: 1, Alice, alice@, 25"]
    end
    
    subgraph Redis["Redis"]
        R1["键: user:1:name"]
        R1V["值: Alice"]
        R2["键: user:1:email"]
        R2V["值:<br>alice\@example.com"]
        R3["键: user:1:age"]
        R3V["值: 25"]
    end
    
    S1 -->|"映射"| R1
    S2 -->|"扁平化"| R2
    S3 -->|"每行独立"| R3
    
    style Redis fill:#dc143c,color:#fff
```

| 特性       | 传统数据库  | Redis  |
| -------- | ------ | ------ |
| **数据结构** | 固定表结构  | 多种数据结构 |
| **存储位置** | 磁盘     | 内存（主）  |
| **查询方式** | SQL 语句 | 键命令    |
| **事务**   | ACID   | 有限支持   |
| **扩展性**  | 垂直扩展   | 水平扩展   |

---

### 5. Redis 典型应用场景

```mermaid
flowchart TD
    A["Redis 应用场景"] --> B["缓存"]
    A --> C["会话存储"]
    A --> D["消息队列"]
    A --> E["实时排行榜"]
    A --> F["分布式锁"]
    A --> G["限流器"]
    
    B --> B1["页面缓存、API缓存"]
    C --> C1["Session、Token"]
    D --> C2["List、Stream"]
    E --> C3["Sorted Set"]
    F --> C4["SETNX 命令"]
    G --> C5["计数器+过期时间"]
    
    style A fill:#dc143c,color:#fff
    style B fill:#ffd700
    style C fill:#ffd700
    style D fill:#ffd700
    style E fill:#ffd700
    style F fill:#ffd700
    style G fill:#ffd700
```

---

### 6. 总结

#### 键命名公式

```
键 = 业务前缀:实体类型:实体ID[:子实体][:字段]
```

#### 一句话解释

> **Redis 的 `entity:identifier:sub_entity:field` 格式，本质上是把传统数据库的「表名:主键:字段」拆散，用冒号连接成唯一的键名。**

这样设计是为了：
1. ✅ **唯一性** - 每个数据都有唯一键
2. ✅ **可读性** - 键名自描述
3. ✅ **可查询** - 支持前缀匹配
4. ✅ **层次感** - 表达数据关系

---

### 参考链接

1. [Redis Official Website](https://redis.io/) — 官方网站
2. [Redis Data Types](https://redis.io/docs/data-types/) — 数据类型文档
3. [Redis KEYS Command](https://redis.io/commands/keys/) — 键命令
4. [Redis Hashes](https://redis.io/docs/data-types/hashes/) — Hash 存储
5. [Redis Persistence](https://redis.io/docs/management/persistence/) — 持久化机制

---

