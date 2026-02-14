# Rice Niri - 工作计划

## TL;DR

> **目标**: 在 Hyprland 会话中配置和美化 niri，准备切换到 niri 作为主力窗口管理器
> 
> **当前状态**: 
> - ✅ Niri 25.11 已安装
> - ✅ 默认配置已生成 (~/.config/niri/config.kdl)
> - ✅ Niri 正在 winit 窗口模式下运行 (PID 68728)
> 
> **交付物**:
> - 美化的 niri 配置（配色、布局、动画）
> - 集成的 waybar 配置
> - 自定义键盘绑定
> - 启动脚本和服务
> 
> **预计工作量**: 中等 (Medium)
> **并行执行**: 部分可并行
> **关键路径**: 修改配置 → 测试 → 调整

---

## 上下文

### 用户当前环境
- **OS**: Arch Linux
- **当前 WM**: Hyprland
- **目标 WM**: Niri
- **配置风格**: 基于当前 Hyprland 设置迁移

### 当前 Hyprland 配置特点
- 使用 waybar 作为状态栏
- 使用 quickshell (qs) 作为自定义 shell
- 使用 hypridle + hyprlock 作为锁屏
- 使用 cliphist 管理剪贴板
- 使用 easyeffects 处理音频
- 自定义键盘绑定和窗口规则

### Niri 与 Hyprland 的关键差异
| 特性 | Hyprland | Niri |
|------|----------|------|
| 布局 | 动态平铺 | 滚动平铺 (scrollable) |
| 配置格式 | `.conf` | `.kdl` |
| 工作区 | 固定数量 | 动态无限 |
| 窗口排列 | 树状 | 列状 (columns) |

---

## 工作目标

### 核心目标
创建一个美观、功能完整的 niri 配置，包含：
1. 现代化的视觉风格（配色、边框、动画）
2. 完整的键盘绑定（基于当前 Hyprland 习惯）
3. 集成现有工具（waybar、cliphist、easyeffects 等）
4. 优化的布局和交互体验

### 具体交付物
- [ ] `~/.config/niri/config.kdl` - 主配置文件
- [ ] `~/.config/niri/binds.kdl` - 键盘绑定（可选分离）
- [ ] `~/.config/waybar/config-niri` - waybar 配置（如需要调整）
- [ ] `~/.config/niri/scripts/` - 辅助脚本

### Definition of Done
- [ ] 配置在 winit 窗口模式下可正常运行
- [ ] 所有键盘绑定工作正常
- [ ] waybar 正确显示
- [ ] 应用程序可以正常启动和切换
- [ ] 配置已备份到 git/dotfiles

### Must Have
- 与 Hyprland 相似的配色方案
- 完整的窗口管理绑定
- 应用启动器绑定
- waybar 集成
- 截图功能

### Must NOT Have (Guardrails)
- 不要删除现有的 Hyprland 配置
- 不要修改系统级配置（需要 sudo 的部分留给用户）
- 不要安装新软件包（除非明确请求）

---

## 执行策略

### 阶段划分

```
阶段 1: 基础配置调整 (必做)
├── 修改配色方案
├── 调整布局参数
└── 配置显示器

阶段 2: 键盘绑定优化
├── 添加应用启动绑定
├── 调整窗口管理绑定
└── 添加媒体键绑定

阶段 3: 启动服务配置
├── 配置 spawn-at-startup
├── 集成现有工具
└── 测试启动流程

阶段 4: 美化与微调
├── 动画配置
├── 阴影和视觉效果
└── 最终测试
```

### 依赖关系
| 任务 | 依赖 | 阻塞 |
|------|------|------|
| 配色调整 | 无 | 布局调整 |
| 键盘绑定 | 无 | 无 |
| 启动服务 | 无 | 最终测试 |
| 最终测试 | 所有配置 | 无 |

---

## TODOs

### 任务 1: 基础配置调整

**What to do**:
1. 修改 focus-ring 配色为 `#3daee9` (蓝色，匹配 KDE/Plasma 风格)
2. 调整 gaps 为 8 (更紧凑)
3. 启用 border 替代 focus-ring，或保留 focus-ring 但调整宽度为 2
4. 添加环境变量配置块
5. 配置光标主题 (Bibata-Modern-Classic)

**Must NOT do**:
- 不要删除任何现有的窗口规则
- 不要修改我们不理解的配置

**Recommended Agent Profile**:
- **Category**: `quick`
- **Skills**: `ui-ux-pro-max` (用于配色建议)
- **Justification**: 这是一个直接的配置文件编辑任务，不需要复杂逻辑

**Parallelization**:
- **Can Run In Parallel**: YES
- **Parallel Group**: Wave 1
- **Blocks**: 任务 2

**Acceptance Criteria**:
- [ ] `focus-ring active-color` 设置为 `"#3daee9"`
- [ ] `focus-ring width` 设置为 `2`
- [ ] `gaps` 调整为 `8`
- [ ] `cursor` 块配置 Bibata 主题
- [ ] 运行 `niri validate` 通过

**Agent-Executed QA**:
```
Scenario: 验证配置语法
  Tool: Bash
  Steps:
    1. niri validate
  Expected Result: 返回 "Configuration is valid" 或无错误
```

---

### 任务 2: 键盘绑定优化

**What to do**:
1. 添加常用应用启动绑定：
   - `Mod+Return` → alacritty
   - `Mod+D` → wofi --show drun
   - `Mod+E` → dolphin
   - `Mod+W` → firefox
   - `Mod+C` → code
2. 调整窗口管理绑定以匹配 Hyprland 习惯：
   - `Mod+Q` → close-window
   - `Mod+M` → maximize-column
   - `Mod+V` → toggle-window-floating
3. 添加截图绑定：
   - `Print` → grim + slurp
   - `Mod+Print` → grim (全屏)

**Must NOT do**:
- 不要删除默认绑定，只添加新的
- 不要修改我们不理解的绑定

**Recommended Agent Profile**:
- **Category**: `quick`
- **Skills**: 无特殊技能需求
- **Justification**: 纯文本编辑任务

**Parallelization**:
- **Can Run In Parallel**: YES (与任务 1 同时)
- **Parallel Group**: Wave 1
- **Blocks**: 任务 3

**Acceptance Criteria**:
- [ ] 所有新绑定添加到 `binds {}` 块
- [ ] 绑定语法正确（KDL 格式）
- [ ] 无重复的键位冲突
- [ ] `niri validate` 通过

**Agent-Executed QA**:
```
Scenario: 验证绑定语法
  Tool: Bash
  Steps:
    1. niri validate
  Expected Result: 无绑定相关错误
```

---

### 任务 3: 启动服务配置

**What to do**:
1. 修改现有的 `spawn-at-startup` 行：
   - 保留 waybar
   - 添加 easyeffects --hide-window --service-mode
   - 添加剪贴板管理 (wl-paste + cliphist)
   - 添加 hypridle
2. 添加环境变量配置块（在文件顶部）
3. 配置 screenshot-path

**Must NOT do**:
- 不要添加需要用户交互的程序到自动启动
- 不要覆盖现有的 waybar 配置

**Recommended Agent Profile**:
- **Category**: `quick`
- **Skills**: 无
- **Justification**: 简单的配置编辑

**Parallelization**:
- **Can Run In Parallel**: NO (依赖任务 1 和 2)
- **Blocked By**: 任务 1, 任务 2

**Acceptance Criteria**:
- [ ] waybar 仍配置为自动启动
- [ ] easyeffects 添加为自动启动
- [ ] 剪贴板管理配置正确
- [ ] 环境变量块添加正确
- [ ] `niri validate` 通过

**Agent-Executed QA**:
```
Scenario: 验证启动配置
  Tool: Bash
  Steps:
    1. grep -A5 "spawn-at-startup" ~/.config/niri/config.kdl
  Expected Result: 显示所有配置的启动项
```

---

### 任务 4: 动画配置

**What to do**:
1. 调整 `animations {}` 块
2. 启用窗口打开/关闭动画
3. 调整动画速度（可选：slowdown 因子）

**Must NOT do**:
- 不要禁用所有动画（用户可能喜欢动画）

**Recommended Agent Profile**:
- **Category**: `quick`
- **Skills**: 无

**Parallelization**:
- **Can Run In Parallel**: YES (与任务 1-2)
- **Parallel Group**: Wave 1

**Acceptance Criteria**:
- [ ] 动画配置语法正确
- [ ] 可选的 slowdown 设置

---

### 任务 5: 测试与验证

**What to do**:
1. 运行配置验证：`niri validate`
2. 如果 niri 已在运行，发送重载命令：`niri msg reload`
3. 在 winit 窗口中测试：
   - 打开终端
   - 测试键盘绑定
   - 检查 waybar 显示

**Must NOT do**:
- 不要在测试中修改配置文件

**Recommended Agent Profile**:
- **Category**: `quick`
- **Skills**: 无

**Parallelization**:
- **Can Run In Parallel**: NO (最终验证)
- **Blocked By**: 所有配置任务

**Acceptance Criteria**:
- [ ] `niri validate` 通过
- [ ] 配置成功重载
- [ ] 基本功能测试通过

**Agent-Executed QA**:
```
Scenario: 配置验证
  Tool: Bash
  Steps:
    1. niri validate
    2. niri msg reload 2>&1 || echo "Niri not running or reload failed"
  Expected Result: 
    - 验证通过
    - 重载成功或无错误
```

---

## 详细配置参考

### 推荐的颜色方案
```
focus-ring:
- active-color: "#3daee9" (蓝色，匹配 KDE Breeze)
- inactive-color: "#4d4d4d" (深灰)
- width: 2

border (可选):
- active-color: "#ffc87f" (橙色)
- inactive-color: "#505050"
```

### 需要添加的环境变量
```kdl
environment {
    QT_QPA_PLATFORM "wayland;xcb"
    QT_QPA_PLATFORMTHEME "qt5ct"
    XDG_CURRENT_DESKTOP "niri"
    XDG_SESSION_DESKTOP "niri"
    XDG_SESSION_TYPE "wayland"
    ELECTRON_OZONE_PLATFORM_HINT "auto"
    _JAVA_AWT_WM_NONREPARENTING "1"
}
```

### 光标配置
```kdl
cursor {
    xcursor-theme "Bibata-Modern-Classic"
    xcursor-size 24
    hide-when-typing true
}
```

### 启动服务
```kdl
spawn-at-startup "waybar"
spawn-at-startup "easyeffects" "--hide-window" "--service-mode"
spawn-at-startup "wl-paste" "--type" "text" "--watch" "cliphist" "store"
spawn-at-startup "wl-paste" "--type" "image" "--watch" "cliphist" "store"
```

### 推荐的键盘绑定
```kdl
// 应用启动
Mod+Return { spawn "alacritty"; }
Mod+D { spawn "wofi" "--show" "drun"; }
Mod+E { spawn "dolphin"; }
Mod+W { spawn "firefox"; }
Mod+C { spawn "code"; }

// 窗口管理
Mod+Q { close-window; }
Mod+M { maximize-column; }
Mod+V { toggle-window-floating; }
Mod+Space { toggle-column-tabbed-display; }
```

---

## 最终检查清单

- [ ] 所有配置语法正确 (`niri validate`)
- [ ] 配色方案已应用
- [ ] 键盘绑定已添加
- [ ] 启动服务已配置
- [ ] 光标主题已设置
- [ ] 环境变量已添加
- [ ] 配置已备份

---

## 后续步骤

完成此计划后：
1. 在 winit 窗口中充分测试 niri
2. 满意后，从 TTY 或登录管理器启动原生 niri
3. 可选：创建 systemd 用户服务
4. 更新 `arch 配置niri.md` 文档

---

**计划创建时间**: 2026-02-13
**状态**: 待执行
**执行命令**: `/start-work`
