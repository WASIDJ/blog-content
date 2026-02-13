---
author: Ryou
title: arch启动慢了--优化
date: 2026-02-12T23:16:27+08:00
lastmod: 2026-02-13T00:09:00+08:00
description: Arch Linux 启动慢问题诊断报告
draft: true
mermaid: true
image: https://w.wallhaven.cc/full/qr/wallhaven-qrjmgl.jpg
位置: 上海市 - 上海市
坐标:
  - 121.4768
  - 31.2243
温度: 6℃
风速: 6.9 m/s
天气: 晴朗
categories:
  - llm协助
tags:
  - arch-linux
  - 启动优化
  - hyprland
  - sddm
---


>[!summary] 问题描述

1. **开机时，hyprland 要进入两次** - SDDM 登录界面需要启动两次才能成功进入桌面
2. **进入 hyprland 后一段时间后被卡掉** - 会话运行约 1 分钟后崩溃并回到登录界面

---

## 📊 系统启动分析

### 启动时间总览

```
Startup finished in 7.183s (firmware) + 1.918s (loader) + 4.843s (kernel) + 9.870s (userspace) = 23.816s 
graphical.target reached after 9.869s in userspace.
```

**总启动时间：约 24 秒**

### 启动耗时服务排名 (Top 10)

| 服务 | 耗时 |
|------|------|
| docker.service | 5.839s |
| systemd-binfmt.service | 962ms |
| NetworkManager.service | 726ms |
| dev-sda2.device | 615ms |
| containerd.service | 288ms |
| systemd-journal-flush.service | 228ms |
| systemd-tmpfiles-setup-dev-early.service | 225ms |
| user@1000.service | 198ms |
| systemd-remount-fs.service | 185ms |
| libvirtd.service | 160ms |

### 关键启动链

```
graphical.target @9.869s
└─multi-user.target @9.869s
  └─daed.service @9.869s
    └─docker.service @4.029s +5.839s
      └─containerd.service @3.709s +288ms
```

---

## 🚨 发现的主要问题

### 问题 1: SDDM Wayland 显示服务器启动失败

**症状**：SDDM 首次尝试启动 Wayland 显示服务器失败，然后回退到 X11

**日志证据**：
```
Feb 12 23:15:02 sddm[941]: Auth: sddm-helper exited with 4
Feb 12 23:15:02 sddm[941]: Greeter stopped. SDDM::Auth::HELPER_DISPLAYSERVER_ERROR
Feb 12 23:15:02 sddm[941]: Failed to launch the display server, falling back to DisplayServer=x11-user
```

**解释**：
- SDDM 配置了 `DisplayServer=wayland`
- 但 Wayland greeter 启动失败（exit code 4）
- 系统被迫回退到 X11 模式
- **这是用户需要进入两次的原因之一**

**配置文件**：
```conf
# /etc/sddm.conf.d/*.conf
[General]
DisplayServer=wayland
CompositorCommand=weston --shell=kiosk
```

---

### 问题 2: Hyprland 会话崩溃

**症状**：进入 Hyprland 后约 1 分钟，会话被终止

**日志证据**：
```
Feb 12 23:15:11 sddm[941]: Starting Wayland user session: "/usr/share/sddm/scripts/wayland-session" "/usr/bin/start-hyprland"
Feb 12 23:15:40 sddm[941]: Session started false           ← 启动失败标记
Feb 12 23:16:40 sddm[941]: Auth: sddm-helper exited with 2  ← 约1分钟后崩溃
```

**时间线**：
- 23:15:11 - Hyprland 会话启动
- 23:15:40 - SDDM 标记会话启动失败
- 23:16:40 - 会话完全退出，SDDM 重新启动

**相关服务崩溃**：
- `xdg-desktop-portal-gtk.service` - Error reading events from display: Broken pipe
- `plasma-xdg-desktop-portal-kde.service` - exit code 255/EXCEPTION

---

### 问题 3: 内核模块加载失败

```
systemd-modules-load[492]: Failed to find module 'ntsync'
systemd-modules-load[492]: Failed to find module 'acpi_call'
```

**影响**：Wine/游戏性能优化相关的模块未加载，但不影响系统启动

---

### 问题 4: Udev 规则配置问题

```
systemd-udevd[536]: /usr/lib/udev/rules.d/75-davincipanel.rules:2 Failed to resolve group 'resolve'
systemd-udevd[536]: /etc/udev/rules.d/99-ec.rules:1 Failed to resolve group 'ec'
```

---

### 问题 5: DBus 通知服务冲突

```
dbus-broker-launch[1138]: Ignoring duplicate name 'org.freedesktop.Notifications' in service file 
  - /usr/share/dbus-1/services/org.kde.plasma.Notifications.service
  - /usr/share/dbus-1/services/org.knopwob.dunst.service
```

**解释**：系统中同时安装了 KDE Plasma 通知服务和 Dunst 通知服务，导致冲突

---

### 问题 6: Wireplumber 蓝牙 MIDI 错误

```
wireplumber[1599]: spa.bluez5.midi: org.bluez.GattManager1.RegisterApplication() failed: 
  GDBus.Error:org.bluez.Error.AlreadyExists: Already Exists
```

---

### 问题 7: gkr-pam 密钥环警告

```
sddm-helper[1552]: gkr-pam: unable to locate daemon control file
```

---

## 💻 系统信息

### 硬件配置

**显卡**：
```
00:02.0 Intel Corporation Alder Lake-P GT2 [Iris Xe Graphics] (i915 驱动)
01:00.0 NVIDIA Corporation GA104M [Geforce RTX 3070 Ti Laptop GPU] (nvidia 驱动)
```

**内存**：
```
Total: 31Gi
Used:  9.1Gi
Free:  11Gi
Available: 21Gi
```

### 软件配置

**内核参数**：
```
initrd=\initramfs-linux-lts.img root=UUID=xxx rw rootflags=subvol=@ quiet splash loglevel=3 nvidia_drm.modeset=1
```

**NVIDIA 驱动**：
```
版本: 590.48.01
内核模块: nvidia.ko.zst (DKMS)
```

**mkinitcpio 配置**：
```conf
MODULES=(btrfs nvidia nvidia_modeset nvidia_uvm nvidia_drm)
HOOKS=(base plymouth udev autodetect microcode modconf kms keyboard keymap consolefont block filesystems fsck)
```

### 运行中服务数量：33 个

---

## 🔧 优化建议

### 高优先级

1. **修复 SDDM Wayland 显示服务器问题**
   - 临时方案：切换到 X11 模式启动 SDDM
   - 检查 weston 是否安装：`pacman -Q weston`
   - 或者尝试使用 labwc 替代 weston 作为 kiosk  compositor

2. **调查 Hyprland 崩溃原因**
   - 查看 Hyprland 详细日志：`~/.hyprland/hyprland.log`
   - 检查最近更新的包：`/var/log/pacman.log`
   - 考虑临时禁用 portal 服务进行排查

### 中优先级

3. **优化启动速度**
   - Docker 服务耗时 5.8s，如不需要可禁用：`systemctl disable docker`
   - 启用 systemd 服务并行启动
   - 检查 initramfs 是否包含不必要的模块

4. **清理模块加载配置**
   - 删除或注释掉 `/etc/modules-load.d/` 中不存在的模块配置
   - `ntsync` 和 `acpi_call` 需要先安装相关包才能使用

### 低优先级

5. **修复通知服务冲突**
   - 选择使用 KDE Plasma 或 Dunst 其中之一
   - 删除不需要的 dbus service 文件

6. **修复 udev 规则**
   - 创建缺失的用户组或修正规则文件

7. **安全警告**
   - 修复 `/boot/loader/random-seed` 权限问题

---

## 📋 诊断命令汇总

```bash
# 查看启动时间
systemd-analyze
systemd-analyze blame
systemd-analyze critical-chain

# 查看 SDDM 日志
journalctl -u sddm --since "1 day ago"

# 查看 Hyprland 日志（在用户目录）
cat ~/.hyprland/hyprland.log 2>/dev/null || echo "Log not found"

# 查看最近系统错误
journalctl -p 3 --since "1 day ago"

# 查看显卡驱动状态
lspci -k | grep -EA3 'VGA|3D|Display'

# 查看最近 pacman 更新
tail -50 /var/log/pacman.log
```

---

## 🔴 崩溃报告分析

在 `~/.cache/hyprland/` 目录下发现崩溃报告：`hyprlandCrashReport1649.txt`

### 崩溃详情

| 项目 | 内容 |
|------|------|
| **信号** | SIGSEGV (段错误，signal 11) |
| **Hyprland 版本** | v0.53.1 (ab1d80f3) |
| **崩溃日期** | 2026-01-19 |
| **内核版本** | 6.12.65-1-lts |

### 堆栈跟踪 (Backtrace)

```
#4 | libhyprutils.so.10(_ZN9Hyprutils9Animation21CBaseAnimatedVariable14onAnimationEndEv+0x98)
    → Hyprutils::Animation::CBaseAnimatedVariable::onAnimationEnd()

#5 | Hyprland(+0x4d3467) [0x59dbd6a3c467]
    → CHyprAnimationManager::CHyprAnimationManager()

#6 | Hyprland(_ZN21CHyprAnimationManager4tickEv+0xa60)
    → CHyprAnimationManager::tick()
```

### 崩溃分析

**根本原因**：动画系统在 `onAnimationEnd()` 回调中发生段错误

- 崩溃发生在 **Hyprutils 动画库**的 `CBaseAnimatedVariable::onAnimationEnd()` 函数
- 堆栈显示这是动画管理器 tick 循环的一部分
- **与触摸屏/触摸板事件相关**：日志末尾显示大量 libinput 触摸事件（`event18` - 可能是触摸板）

### 可能触发原因

1. **动画配置冲突**：Hyprland 配置中的动画设置与触摸手势冲突
2. **触摸板驱动问题**：libinput 在多点触控时触发边界情况
3. **内存损坏**：动画回调访问已释放的内存

### 临时解决方案

1. **禁用或简化动画**：
   ```conf
   # ~/.config/hypr/hyprland.conf
   animations {
       enabled = false
   }
   ```

2. **禁用触摸板手势**（如果不需要）：
   ```conf
   gestures {
       workspace_swipe = false
   }
   ```

---

## 📌 下一步行动

### 立即执行

1. **修复 SDDM Wayland Greeter 问题**（导致需要进入两次）：
   ```bash
   # 创建配置将 SDDM 切换到 X11 模式
   sudo tee /etc/sddm.conf.d/display.conf << 'EOF'
   [General]
   DisplayServer=x11
   EOF
   ```

2. **临时禁用 Hyprland 动画**以避免崩溃：
   - 编辑 `~/.config/hypr/hyprland.conf`
   - 在 `animations` 部分设置 `enabled = false`
   - 重启测试

3. **检查触摸板配置**：
   - 查看 `~/.config/hypr/hyprland.conf` 中的 `input` 部分
   - 尝试添加 `disable_while_typing = true`

### 深入调查

4. **更新 Hyprland** 到最新版本（当前 v0.53.1，可能有修复）：
   ```bash
   sudo pacman -S hyprland
   ```

5. **检查是否有可用的 coredump**：
   ```bash
   coredumpctl list
   coredumpctl info <PID>
   ```

6. **查看实时日志以捕获下一次崩溃**：
   ```bash
   # 终端 1：监视系统日志
   journalctl -f | grep -i hypr
   
   # 终端 2：监视 Hyprland 日志
   tail -f ~/.cache/hyprland/*.txt
   ```

7. **如果问题持续，报告 bug**：
   - 访问：https://github.com/hyprwm/Hyprland/issues
   - 提供崩溃报告文件内容

### 启动速度优化

8. **禁用不必要的服务**（可选）：
   ```bash
   # 如果不需要 Docker
   sudo systemctl disable docker
   sudo systemctl disable containerd
   
   # 如果不需要虚拟机
   sudo systemctl disable libvirtd
   ```
### 系统级加速：让服务并行启动
```bash
sudo nvim /etc/systemd/system.conf
ParallelStart=yes 
DefaultTimeoutStartSec=5s
```

---

*诊断时间: 2026-02-13*
*系统: Arch Linux (kernel 6.12.70-1-lts)*