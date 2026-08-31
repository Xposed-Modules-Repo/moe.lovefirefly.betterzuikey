# BetterZUIKey

[![Android](https://img.shields.io/badge/API-34%2B-green)](https://developer.android.com/about/versions/15) [![Xposed](https://img.shields.io/badge/Xposed-LSPosed-blue)](https://github.com/LSPosed/LSPosed) [![Kotlin](https://img.shields.io/badge/Kotlin-2.1-7f52ff)](https://kotlinlang.org) [![Version](https://img.shields.io/github/v/release/CommandPrompt-Wang/BetterZUIKey?display_name=tag&label=Version)](https://github.com/CommandPrompt-Wang/BetterZUIKey/releases) [![License](https://img.shields.io/badge/License-GPL--3.0-orange)](LICENSE)

[Please scroll down for English / 英语请向下滚动](#eng)

面向联想 ZUXOS 的 [LSPosed](https://github.com/LSPosed/LSPosed) 键盘快捷键覆写模块。

~~别问为什么叫 ZUI 因为 ZUX 的代码也自称是 ZUI~~

ZUXOS 内置大量 Win/Ctrl/Alt 组合键与物理快捷键，但许多无法真正禁用，关闭系统开关后按键仍会被系统吞掉。BetterZUIKey 在系统键盘处理链中插入拦截点，让你对每条快捷键独立选择行为，并支持按前台应用切换配置。

**包名**：`moe.lovefirefly.betterzuikey`

## 适用环境

| 项目 | 要求 |
| --- | --- |
| 系统 | 联想 ZUXOS（已在 Android 16 / ZUXOS 1.5.04 测试） |
| Root | 可选。未挂载 Root 时通过 system_server 权限代理执行命令，会稍有延迟 |
| 框架 | [LSPosed](https://github.com/LSPosed/LSPosed)（libxposed API 101+）；自 v1.1.0-beta1 后已**不支持** XPosed |
| 作用域 | **系统框架**（`system_server`，必须） ~~输入法 `hook` 策略需额外勾选目标 IME~~ （该功能已弃用） |

低版本的 ZUXOS（基于 Android 15） 未经测试，欢迎在 [Issues](https://github.com/CommandPrompt-Wang/BetterZUIKey/issues) 反馈。

## 主要功能

### ⌨️ 快捷键（全局）

- **50+ 条** Win/Ctrl/Alt/Shift 组合键与 AOSP 辅助功能键（Win+Alt+3~6 等）
- 每条支持最多 **五种覆写模式**：保持默认 / 启用 ZUX / 启用 AOSP / 关闭（透传给应用）/ 忽略
- 支持搜索、筛选；修改覆写时自动打开对应系统开关以完全接管事件

特别地：

**Win 长按**：

| 模式 | 说明 |
| --- | --- |
| 保持默认 | 透传，由系统决定（含语音助手等，需要特殊的键盘方有效） |
| 启用 ZUX 实现 | 模块接管：短按 Win → 开始菜单，长按 → 语音助手（国行=小天） |
| 忽略 | 无动作 |
| 执行命令… | 长按运行脚本；长按卡片打开命令编辑器 |

**智能键 507 和 508**：

| 模式 | 短按 | 长按 |
| --- | --- | --- |
| 跟随系统 | 系统决定 | 系统决定 |
| 忽略 | 无动作 | 无动作 |
| 执行命令… | 运行脚本 | 打开系统智能键设置页 |

长按卡片可跳转系统「键盘应用功能」设置页。

v1.3.0 起，附录中的多数 ZUI **物理键**（静音、触控板、分屏、Print Screen 等）**不再**提供模块覆写，交由 ZUI / 系统原生处理；**507 和 508** 仍可在本 Tab 配置。

### 📋 应用模板

- 为指定前台 App 单独定义快捷键覆写，**覆盖**全局「快捷键」Tab 的对应项
- 未在模板中列出的键继续继承全局设置
- 支持多 App 绑定、复制、排序；同一 App 匹配多个模板时，列表中第一个启用的生效

### ⚙️ 设置

**输入法增强**（独立子页）
- 「切换输入法」「切换语言」各绑定一个物理组合键（Ctrl+Shift、Alt+Shift、Ctrl+Space、右 Alt、Win 长按等）
- JSON **IME Profile** 预设（`framework` / `hook` / `keyremap` 三种策略）
- 当 Win 长按被多重占用时，根据是否在输入状态决定 IME 切换 / Win 长按卡片的功能

### ⚠️ 已知问题
- 输入法增强的组合键映射功能条件过宽，可能被意外触发。当前实现是按“按下了指定修饰键”来判定，未严格区分是否仅为目标组合键本身；例如把 `Ctrl+Shift -> Ctrl+Space` 映射后，`Ctrl+Shift+←/→` 这类导航组合键也可能误触发。
- 建议避免将常见修饰键组合映射为另一组常用快捷键，尤其是涉及方向键、功能键或其他系统保留组合的场景。

**虚拟 Fn 键**
- Win 模拟 Fn → F1~F12；FnLock（Win+`）与临时 Fn 模式
- 键盘 Profile 导入/导出；配合键盘检测页录制 scanCode 映射

**Termux 集成**
- 一键授予 `RUN_COMMAND` 等权限，供「执行命令…」运行 shell 脚本
- 脚本模板支持 root / 超时 / 单例运行
- v1.5.0 起内置 [TermuxAm](https://github.com/CommandPrompt-Wang/TermuxAm) 子模块，非 Root 模式下脚本中的 `am` 自动替换为 `termuxam`，解决此前因权限不足（SIGABRT 134）导致命令执行失败的问题

**更新检查**
- 启动时自动检查；长按卡片立即检查
- 四通道可选：自动 / GitHub / GitHub 镜像 / 个人镜像
- v1.4.1-beta3 起支持检测到新版本后弹窗提示下载并安装

**其它**：模块总开关、外观、日志级别、应用内中英文与完整帮助文档

## 安装

- 在 [Releases](https://github.com/CommandPrompt-Wang/BetterZUIKey/releases) 下载最新 APK 并安装
- 打开 LSPosed Manager → 模块 → 启用 **BetterZUIKey**
- 作用域勾选 **系统框架**（必须）
- ~~若使用输入法 hook 策略，按需勾选目标 IME~~（已弃用）
- 软重启 system_server（或重启设备）
- 打开应用，主页状态为 **已激活** 即成功

v1.5.1 起模块通过 libxposed API 101 的 `getScope()` 直接获取激活作用域列表，不再依赖不可靠的 SharedPreferences 写入，作用域检测更准确。若更新后仍提示作用域错误，可尝试再次软重启。

详细说明与快捷键参考见应用内 **帮助** 页面。

## 开源与反馈

- **源码**：[CommandPrompt-Wang/BetterZUIKey](https://github.com/CommandPrompt-Wang/BetterZUIKey)
- **问题反馈**：[Issues](https://github.com/CommandPrompt-Wang/BetterZUIKey/issues)
- **许可证**：GPL-3.0

## 免责声明

本模块直接 Hook 系统键盘输入处理链。使用前请阅读应用内帮助并理解各选项含义。不当配置可能导致部分快捷键行为异常；开发者不承担因使用本模块造成的系统故障、数据丢失或设备异常的责任。

部分代码由 AI 辅助生成，如有问题欢迎提交 Issue 或 Pull Request。

---

<a id="eng"></a>

# BetterZUIKey

An [LSPosed](https://github.com/LSPosed/LSPosed) module for overriding keyboard shortcuts on Lenovo ZUXOS tablets.

Don't ask me why it's called ZUI because the code of ZUX also calls itself ZUI.

ZUXOS ships with many Win/Ctrl/Alt combos and physical shortcut keys, but many cannot be truly disabled — the system may still swallow key events even when toggles are off. BetterZUIKey hooks the keyboard shortcut pipeline so you can choose behavior per shortcut and apply per-app templates.

**Package**: `moe.lovefirefly.betterzuikey`

## Requirements

| Item | Requirement |
| --- | --- |
| OS | Lenovo ZUXOS (tested on Android 16 / ZUXOS 1.5.04) |
| Root | Optional. Without root, custom commands run via a system_
server privilege proxy and may feel slightly delayed |
| Framework | [LSPosed](https://github.com/LSPosed/LSPosed) (libxposed API 101+). **Not compatible** with legacy Xposed since v1.1.0-beta1 |
| Scope | **System Framework** (`system_server`, required). ~~IME `hook` profiles also need the target IME scoped~~ (Abandoned) |

Android 15 and below are untested. Feedback welcome via [Issues](https://github.com/CommandPrompt-Wang/BetterZUIKey/issues).

## Features

### ⌨️ Shortcuts (global)

- **50+** Win/Ctrl/Alt/Shift combos and AOSP accessibility keys (Win+Alt+3~6, etc.)
- Up to **five override modes** per shortcut: Keep default / Use ZUX / Use AOSP / Off (pass through to app) / Block
- Search and filter; changing an override auto-enables the matching system toggle so the module fully owns the event

In particular:

**Win long-press**

| Mode | Description |
| --- | --- |
| Keep default | Pass-through; system decides (voice assistant, etc.; needs specific keyboard firmware) |
| Use ZUX | Module takes over: short Win → Start Menu, long Win → voice assistant (PRC = XiaoTian) |
| Block | No action |
| Run command… | Long press runs script; long-press card opens command editor |

**Smart keys 507 and 508**

| Mode | Short press | Long press |
| --- | --- | --- |
| Follow system | System decides | System decides |
| Block | No action | No action |
| Run command… | Run script | Open system smart-key settings |

Long-press the card to jump to the system **Keyboard App Functions** page.

Since v1.3.0, most ZUI **physical keys** in the appendix (mute, touchpad, split-screen, Print Screen, etc.) are **no longer** overridable by the module and are handled by ZUI / the system natively. **507 and 508** remain configurable on this tab.

### 📋 App templates

- Define per-app shortcut overrides for a foreground app, **superseding** the matching entries on the global Shortcuts tab
- Keys not listed in a template keep inheriting global settings
- Multi-app binding, copy, and reorder supported; if one app matches several templates, the first enabled one in the list wins

### ⚙️ Settings

**IME Enhancement** (separate page)
- Bind a physical combo each for "Switch IME" and "Switch language" (Ctrl+Shift, Alt+Shift, Ctrl+Space, Right Alt, Win long-press, etc.)
- JSON **IME Profile** presets (`framework` / `hook` / `keyremap` strategies)
- When Win long-press has multiple roles, input-state decides whether IME switching or the Win long-press card behavior applies

**Virtual Fn key**
- Win simulates Fn → F1–F12; FnLock (Win+`) and temporary Fn mode
- Keyboard profile import/export; use the keyboard detect page to record scanCode mappings

**Termux integration**
- One-tap grant of `RUN_COMMAND` and related permissions for **Run command…** shell scripts
- Script templates support root / timeout / singleton run
- Since v1.5.0, the [TermuxAm](https://github.com/CommandPrompt-Wang/TermuxAm) submodule is bundled; in non-root mode, script `am` is automatically replaced by `termuxam`, fixing the previous SIGABRT (134) failure caused by insufficient permissions

**Update checker**
- Auto-check on launch; long-press the card to check immediately
- Four channels: Auto / GitHub / GitHub mirror / Personal mirror
- Since v1.4.1-beta3, a pop-up prompts for download and installation when a newer version is detected

**Other**: master switch, appearance, log level, in-app EN/ZH and full help docs

## Install

- Download the latest APK from [Releases](https://github.com/CommandPrompt-Wang/BetterZUIKey/releases) and install
- LSPosed Manager → Modules → enable **BetterZUIKey**
- Scope **System Framework** (required)
- ~~if using IME `hook` profiles, scope the target IME(s) as needed~~ (Abandoned)
- Soft-reboot system_server (or reboot the device)
- Open the app — home status should show **active**

Since v1.5.1, the module fetches the active scope list directly via the libxposed API 101 `getScope()`, no longer relying on the unreliable SharedPreferences write, making scope detection more accurate. If a scope error still appears after updating, try soft-rebooting again.

See in-app **Help** for details and the full shortcut reference.

## Source & feedback

- **Source**: [CommandPrompt-Wang/BetterZUIKey](https://github.com/CommandPrompt-Wang/BetterZUIKey)
- **Issues**: [GitHub Issues](https://github.com/CommandPrompt-Wang/BetterZUIKey/issues)
- **License**: GPL-3.0

## Disclaimer

This module hooks the system keyboard input pipeline directly. Read in-app help and understand each option before changing settings. Misconfiguration may cause unexpected shortcut behavior; the developer is not liable for system failures, data loss, or device issues caused by use of this module.

Portions of the code were AI-assisted. Issues and pull requests are welcome.
