---
author: Ryou
title: arch 配置niri
date: 2026-02-13T14:45:28+08:00
lastmod: 2026-02-13T19:10+08:00
description: 
draft: true
mermaid: true
image: https://w.wallhaven.cc/full/rq/wallhaven-rqg6m7.jpg
位置: 上海市 - 上海市
坐标: 
  - 121.4768
  - 31.2243
温度: 19.7℃ 
风速: 4.2 m/s
天气: 主要多云
categories:
  - 
tags:
  - 状态: 已安装，待配置 (2026-02-13)
---
- 我感觉我不想用 hyprland了
> [!warning] 状态更新
> **niri 25.11 已安装，用户使用 hyprland。正在测试和配置阶段。**
>
> ---
>
> ### 卸载记录
> - **配置文件位置**: `~/.config/niri/` (已删除)
> - **Wayland 会话**: `/usr/share/wayland-sessions/niri.desktop` (需手动 sudo rm 删除)
> - **用户服务**: niri 无独立 systemd 服务

---

## 快速开始 (基于当前 Hyprland 配置迁移)

### 1. 安装 niri

```bash
# 使用 AUR 助手安装
yay -S niri

# 或使用 paru
paru -S niri
```

### 2. 创建配置文件

```bash
mkdir -p ~/.config/niri
niri --config ~/.config/niri/config.kdl  # 首次运行生成默认配置
```

### 3. 迁移配置 (基于你当前的 Hyprland 配置)

> 以下配置基于你当前的 Hyprland 设置迁移

```kdl
// ~/.config/niri/config.kdl

// ========== 环境变量 (来自 hyprland/env.conf) ==========
environment {
    XDG_CURRENT_DESKTOP "niri"
    XDG_SESSION_DESKTOP "niri"
    XDG_SESSION_TYPE "wayland"
    QT_QPA_PLATFORM "wayland;xcb"
    QT_QPA_PLATFORMTHEME "qt5ct"
    GBM_BACKEND "nvidia-drm"
    __GLX_VENDOR_LIBRARY_NAME "nvidia"
    WLR_NO_HARDWARE_CURSORS "1"
    LIBVA_DRIVER_NAME "nvidia"
    ELECTRON_OZONE_PLATFORM_HINT "auto"
    CLUTTER_BACKEND "wayland"
    XDG_BACKEND "wayland"
    GDK_BACKEND "wayland,x11"
    SDL_VIDEODRIVER "wayland"
    _JAVA_AWT_WM_NONREPARENTING "1"
}

// ========== 输入配置 (来自 hyprland/input.conf) ==========
input {
    keyboard {
        xkb {
            layout "us"
            variant ""
            options "compose:ralt"
        }
        repeat-rate 40
        repeat-delay 400
    }
    touchpad {
        tap
        natural-scroll
        dwt
    }
    mouse {
        accel-profile "flat"
    }
}

// ========== 显示器配置 (来自 hyprland/monitors.conf) ==========
// 运行 niri-msg outputs 查看显示器名称
// 输出示例: DP-1, HDMI-A-1, eDP-1
// 
// 主显示器配置 (1920x1080@60Hz):
// output "DP-1" {
//     mode 1920x1080@60
//     position x=0 y=0
//     scale 1.0
//     primary true
// }

// 副显示器配置 (右侧):
// output "HDMI-A-1" {
//     mode 1920x1080@60
//     position x=1920 y=0
//     scale 1.0
// }

// 笔记本内置显示器:
// output "eDP-1" {
//     scale 2.0
// }

// ========== 游标配置 ==========
cursor {
    xcursor-theme "Bibata-Modern-Classic"
    xcursor-size 24
    hide-when-typing true
}

// ========== 布局配置 (来自 hyprland/general.conf) ==========
layout {
    gaps 10
    gaps-inner 5
    gaps-outer 5
    
    // 边框宽度
    border-size 2
    
    // 焦点边框颜色
    focus-ring {
        width 2
        active-color 61 174 233 255  // 蓝色 (#3daee9)
        inactive-color 77 77 77 255  // 灰色
    }
}

// ========== 窗口规则 (来自 hyprland/rules.conf) ==========
// niri 使用 window-rule {} 块

// Firefox 始终最大化
// window-rule {
//     app-id "firefox"
//     initial-state "maximized"
// }

// ========== 键盘绑定 (来自 hyprland/keybinds.conf) ==========
// niri 使用 binds {} 块，格式: "Modifiers+Key" { action }

// 基础操作
binds {
    // 退出: Super + Shift + Q
    "Super+Shift+Q" { quit skip-confirmation=true }
    
    // 关闭窗口: Super + Q
    "Super+Q" { close }
    
    // 最大化: Super + M
    "Super+M" { toggle-fullscreen }
    
    // 切换浮动: Super + Space
    "Super+Space" { toggle-floating }
    
    // 窗口居中: Super + J
    "Super+J" { center }
    
    // 焦点移动
    "Super+Left" { focus left }
    "Super+Right" { focus right }
    "Super+Up" { focus up }
    "Super+Down" { focus down }
    
    // 窗口移动
    "Super+Shift+Left" { move left }
    "Super+Shift+Right" { move right }
    "Super+Shift+Up" { move up }
    "Super+Shift+Down" { move down }
    
    // 工作区切换
    "Super+1" { workspace 1 }
    "Super+2" { workspace 2 }
    "Super+3" { workspace 3 }
    "Super+4" { workspace 4 }
    "Super+5" { workspace 5 }
    "Super+6" { workspace 6 }
    "Super+7" { workspace 7 }
    "Super+8" { workspace 8 }
    "Super+9" { workspace 9 }
    "Super+0" { workspace 10 }
    
    // 移动到工作区
    "Super+Shift+1" { move-to-workspace 1 }
    "Super+Shift+2" { move-to-workspace 2 }
    "Super+Shift+3" { move-to-workspace 3 }
    "Super+Shift+4" { move-to-workspace 4 }
    "Super+Shift+5" { move-to-workspace 5 }
    "Super+Shift+6" { move-to-workspace 6 }
    "Super+Shift+7" { move-to-workspace 7 }
    "Super+Shift+8" { move-to-workspace 8 }
    "Super+Shift+9" { move-to-workspace 9 }
    "Super+Shift+0" { move-to-workspace 10 }
    
    // 显示器间移动
    "Super+Ctrl+Left" { focus-monitor left }
    "Super+Ctrl+Right" { focus-monitor right }
    "Super+Ctrl+Shift+Left" { move-to-monitor left }
    "Super+Ctrl+Shift+Right" { move-to-monitor right }
    
    // 调整窗口大小
    "Super+Alt+Left" { resize-window left -10% }
    "Super+Alt+Right" { resize-window right +10% }
    "Super+Alt+Up" { resize-window up -10% }
    "Super+Alt+Down" { resize-window down +10% }
    
    // 截图 (使用 grim)
    "Print" { spawn "grim -g \"$(slurp)\" - | wl-copy" }
    "Super+Print" { spawn "grim - | wl-copy" }
    "Shift+Print" { spawn "grim -g \"$(slurp)\" ~/Pictures/Screenshots/$(date +%Y-%m-%d_%H-%M-%S).png" }
}

// ========== 应用启动 (来自 hyprland/execs.conf) ==========

// 启动应用
binds {
    // 终端: Super + Return
    "Super+Return" { spawn "alacritty" }
    
    // 应用启动器: Super + D
    "Super+D" { spawn "wofi --show drun" }
    
    // 文件管理器: Super + E
    "Super+E" { spawn "dolphin" }
    
    // 浏览器: Super + W
    "Super+W" { spawn "firefox" }
    
    // 编辑器: Super + C
    "Super+C" { spawn "code" }
    
    // 设置: Super + I
    "Super+I" { spawn "systemsettings" }
    
    // 音量控制: Super + V
    "Super+V" { spawn "pavucontrol" }
}

// ========== 启动时运行的服务 (来自 hyprland/execs.conf) ==========
spawn-at-startup "waybar"
spawn-at-startup "wofi --show dock"
spawn-at-startup "hypridle"
spawn-at-startup "dbus-update-activation-environment --all"
spawn-at-startup "easyeffects --hide-window --service-mode"
spawn-at-startup "wl-paste --type text --watch bash -c 'cliphist store'"
spawn-at-startup "wl-paste --type image --watch bash -c 'cliphist store'"

// ========== 锁屏 (来自 hyprland/hyprlock.conf) ==========
// 使用 hyprlock 或 swaylock
binds {
    "Super+L" { spawn "swaylock -f" }
}

// ========== 截图配置 ==========
screenshot-path "~/Pictures/Screenshots/Screenshot from %Y-%m-%d %H-%M-%S.png"

// ========== 通知配置 ==========
config-notification {
    disable-failed
}

// ========== 热点角 (关闭) ==========
gestures {
    hot-corners {
        off
    }
}

// ========== 待机配置 (来自 hyprland/hypridle.conf) ==========
switch-events {
    lid-close { spawn "swaylock -f" }
}
```

### 4. 创建 Wayland 会话

```bash
# 需要 root 权限
sudo tee /usr/share/wayland-sessions/niri.desktop > /dev/null << 'EOF'
[Desktop Entry]
Name=niri
Comment=A modern Wayland scrollable tiling compositor
Exec=niri
Type=Application
Keywords=wayland;compositor;tiling;
EOF
```

### 5. 验证配置

```bash
# 检查配置语法
niri validate

# 如果配置正确，重新加载配置
# 在 niri 内运行: Super + Alt + R 或
niri-msg reload
```

---

## 常用命令

| 功能 | 命令 |
|------|------|
| 验证配置 | `niri validate` |
| 重新加载 | `niri-msg reload` |
| 查看输出 | `niri-msg outputs` |
| 查看工作区 | `niri-msg workspaces` |
| 退出 niri | `niri-msg quit` |

---

## 配置文件结构参考

```
~/.config/niri/
├── config.kdl           # 主配置文件
├── binds.kdl           # 键盘绑定 (使用 include)
├── layout.kdl          # 布局配置
└── window-rules.kdl    # 窗口规则
```

### 使用 include 分离配置

```kdl
// 在 config.kdl 中
include "binds.kdl"
include "layout.kdl"
include "window-rules.kdl"
```

---

## 故障排查

```bash
# 查看日志
journalctl -f -u niri

# 或在 tty 中直接运行查看错误
niri
```

---

> **提示**: 此配置基于你当前的 Hyprland 设置迁移。实际使用前请根据你的硬件调整显示器名称和分辨率。

---

## 迁移指南：基于 end-4 dots-hyprland + Quickshell

### 当前环境分析

| 组件 | Hyprland 配置 | Niri 需要 |
|------|--------------|----------|
| **窗口管理器** | Hyprland | Niri (scrollable tiling) |
| **状态栏** | Quickshell (ii) | Quickshell (需适配) |
| **锁屏** | hyprlock | hyprlock/swaylock |
| **剪贴板** | cliphist + QS | cliphist + QS |
| **音频** | easyeffects | easyeffects |
| **输入法** | fcitx5 | fcitx5 |

### 迁移步骤

#### Step 1: 备份当前配置（可选）

```bash
# 备份现有 niri 配置（如有）
cp -r ~/.config/niri ~/.config/niri.backup.$(date +%Y%m%d)
```

#### Step 2: 生成 Niri 默认配置

```bash
# 运行 niri 生成默认配置
niri --config ~/.config/niri/config.kdl
# 立即退出（Ctrl+C）
```

#### Step 3: 创建 Quickshell Niri 配置

你的 QS 配置位于 `~/.config/quickshell/ii/`。Niri 需要单独的配置：

```bash
# 创建 Niri QS 配置目录
mkdir -p ~/.config/quickshell/niri

# 复制 ii 配置作为基础
cp -r ~/.config/quickshell/ii/* ~/.config/quickshell/niri/

# 修改 shell.qml 中的标识
# 将 "ii" 改为 "niri"
sed -i 's/ii/niri/g' ~/.config/quickshell/niri/shell.qml
```

> **注意**: 需要修改 QS 配置中的 workspace 名称和工作区逻辑，因为 Niri 的工作区概念与 Hyprland 不同。

#### Step 4: 关键配置差异

| Hyprland | Niri | 说明 |
|----------|------|------|
| `$qsConfig = ii` | `$qsConfig = niri` | QS 配置名 |
| `Mod+Return` | `Super+Return` | Niri 使用 Super |
| `hyprland.conf` | `config.kdl` | 配置文件格式 |
| `exec-once` | `spawn-at-startup` | 启动项 |
| `windowrule` | `window-rule` | 窗口规则 |
| `workspace` | 动态工作区 | Niri 工作区无限滚动 |

#### Step 5: Niri 配置模板（适配你的 Hyprland）

```kdl
// ~/.config/niri/config.kdl

// ========== 环境变量 ==========
environment {
    XDG_CURRENT_DESKTOP "niri"
    XDG_SESSION_DESKTOP "niri"
    XDG_SESSION_TYPE "wayland"
    QT_QPA_PLATFORM "wayland;xcb"
    QT_QPA_PLATFORMTHEME "qt5ct"
    GBMvidia-drm"
_BACKEND "n    __GLX_VENDOR_LIBRARY_NAME "nvidia"
    LIBVA_DRIVER_NAME "nvidia"
    ELECTRON_OZONE_PLATFORM_HINT "auto"
    _JAVA_AWT_WM_NONREPARENTING "1"
}

// ========== 输入配置来自 (你的 Hyprland) ==========
input {
    keyboard {
        xkb {
            layout "us"
            options "compose:ralt"
        }
        repeat-rate 40
        repeat-delay 400
        numlock
    }
    touchpad {
        tap
        natural-scroll
        dwt
    }
    mouse {
        accel-profile "flat"
    }
}

// ========== 游标 (来自你的 Hyprland) ==========
cursor {
    xcursor-theme "Bibata-Modern-Classic"
    xcursor-size 24
    hide-when-typing true
}

// ========== 布局 (紧凑风格，匹配你的 Hyprland) ==========
layout {
    gaps 8
    
    focus-ring {
        width 2
        active-color "#3daee9"
        inactive-color "#4d4d4d"
    }
}

// ========== Quickshell 启动 (关键差异) ==========
// 使用 niri 配置而非 ii
spawn-at-startup "qs" "-c" "niri"

// ========== 其他服务 (来自你的 Hyprland) ==========
spawn-at-startup "gnome-keyring-daemon" "--start" "--components=secrets"
spawn-at-startup "hypridle"
spawn-at-startup "dbus-update-activation-environment" "--all"
spawn-at-startup "easyeffects" "--hide-window" "--service-mode"

// 剪贴板 (不使用 QS 更新以避免冲突)
// 注意：Niri 下 cliphist 更新可能不同
spawn-at-startup "wl-paste" "--type" "text" "--watch" "cliphist" "store"
spawn-at-startup "wl-paste" "--type" "image" "--watch" "cliphist" "store"

// ========== 键盘绑定 (基于你的 Hyprland) ==========
binds {
    // === 应用启动 ===
    "Super+Return" { spawn "alacritty"; }
    "Super+D" { spawn "qs" "-c" "niri" "ipc" "call" "appLauncher" "toggle"; }
    "Super+E" { spawn "dolphin"; }
    "Super+W" { spawn "firefox"; }
    "Super+C" { spawn "code"; }
    "Super+I" { spawn "qs" "-c" "niri" "ipc" "call" "settings" "toggle"; }
    "Super+V" { spawn "pavucontrol"; }
    
    // === 窗口管理 ===
    "Super+Q" { close-window; }
    "Super+M" { maximize-column; }
    "Super+F" { fullscreen-window; }
    "Super+V" { toggle-window-floating; }
    "Super+Space" { toggle-column-tabbed-display; }
    "Super+T" { spawn "alacritty"; }
    "Super+Tab" { focus-workspace-down; }
    "Super+Shift+Tab" { focus-workspace-up; }
    
    // === 工作区 (与 Hyprland 相同) ===
    "Super+1" { focus-workspace 1; }
    "Super+2" { focus-workspace 2; }
    "Super+3" { focus-workspace 3; }
    "Super+4" { focus-workspace 4; }
    "Super+5" { focus-workspace 5; }
    "Super+6" { focus-workspace 6; }
    "Super+7" { focus-workspace 7; }
    "Super+8" { focus-workspace 8; }
    "Super+9" { focus-workspace 9; }
    "Super+0" { focus-workspace 10; }
    
    // === 移动到工作区 ===
    "Super+Shift+1" { move-column-to-workspace 1; }
    "Super+Shift+2" { move-column-to-workspace 2; }
    "Super+Shift+3" { move-column-to-workspace 3; }
    "Super+Shift+4" { move-column-to-workspace 4; }
    "Super+Shift+5" { move-column-to-workspace 5; }
    "Super+Shift+6" { move-column-to-workspace 6; }
    "Super+Shift+7" { move-column-to-workspace 7; }
    "Super+Shift+8" { move-column-to-workspace 8; }
    "Super+Shift+9" { move-column-to-workspace 9; }
    "Super+Shift+0" { move-column-to-workspace 10; }
    
    // === 焦点移动 (Vim 风格) ===
    "Super+H" { focus-column-left; }
    "Super+J" { focus-window-down; }
    "Super+K" { focus-window-up; }
    "Super+L" { focus-column-right; }
    
    // === 窗口移动 ===
    "Super+Ctrl+H" { move-column-left; }
    "Super+Ctrl+J" { move-window-down; }
    "Super+Ctrl+K" { move-window-up; }
    "Super+Ctrl+L" { move-column-right; }
    
    // === 显示器切换 ===
    "Super+Left" { focus-monitor-left; }
    "Super+Right" { focus-monitor-right; }
    "Super+Shift+Left" { move-column-to-monitor-left; }
    "Super+Shift+Right" { move-column-to-monitor-right; }
    
    // === 锁屏 ===
    "Super+L" { spawn "hyprlock"; }
    
    // === 截图 (与 Hyprland 相同) ===
    "Print" { spawn "hyprctl keyword animation \"1\" 0"; }  # 需适配
    "Super+Print" { spawn "grim -g \"$(slurp)\" - | wl-copy"; }
    "Shift+Print" { spawn "grim - | wl-copy"; }
    
    // === 退出 ===
    "Super+Shift+E" { quit skip-confirmation=true; }
}

// ========== 窗口规则 (来自你的 Hyprland) ==========
window-rule {
    match app-id=r#"^firefox$"#
    initial-state "maximized"
}

window-rule {
    match app-id=r#"^code-oss$"#
    initial-state "maximized"
}

// ========== 截图配置 ==========
screenshot-path "~/Pictures/Screenshots/Screenshot from %Y-%m-%d %H-%M-%S.png"

// ========== 通知配置 ==========
config-notification {
    disable-failed
}

// ========== 手势 ==========
gestures {
    hot-corners {
        off
    }
}
```

#### Step 6: Quickshell 配置适配

Niri 下 QS 需要注意的问题：

1. **工作区逻辑**: Niri 使用动态工作区，QS 的 workspace 模块需要适配
2. **IPC 路径**: Niri 使用不同的 socket 路径
3. **窗口焦点**: Niri 的焦点跟踪方式不同

参考配置: https://github.com/imiric/quickshell-niri

#### Step 7: 测试与验证

```bash
# 1. 验证配置语法
niri validate

# 2. 在 winit 窗口模式测试
niri

# 3. 如需重新加载（窗口模式下无效，需在原生会话）
# niri-msg reload
```

---

## 关键差异总结

### Hyprland vs Niri

| 特性 | Hyprland | Niri |
|------|----------|------|
| **布局** | 动态平铺 | 滚动平铺 (scrollable) |
| **工作区** | 固定数量 (1-10) | 无限滚动 |
| **配置文件** | `.conf` | `.kdl` |
| **窗口移动** | 直接移动 | 列内移动/挤出 |
| **焦点跟随鼠标** | 可配置 | 可配置 |
| **配置文件热重载** | `hyprctl reload` | `niri-msg reload` |

### Quickshell 适配

| 功能 | Hyprland | Niri |
|------|----------|------|
| 启动命令 | `qs -c ii` | `qs -c niri` |
| 工作区模块 | ii/workspace | 需适配 |
| 应用启动器 | ii/appLauncher | 需适配 |
| 概览 | ii/overview | 需适配 |

---

## 资源链接

- **Niri 官方文档**: https://github.com/YaLTeR/niri/wiki
- **Niri 配置示例**: https://github.com/imiric/quickshell-niri
- **双系统 Dotfiles**: https://github.com/Vantesh/dotfiles
- **Niri Dotfiles**: https://github.com/DoubleDotStudios/niri-dotfiles

---

> **迁移建议**: 
> 1. 先在 winit 窗口模式测试完整配置
> 2. 确认 Quickshell (niri 配置) 工作正常后再切换
> 3. 保留 Hyprland 配置以便随时回退