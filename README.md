# What2Eat 🍱

> 解决「今天吃啥」这个世纪难题——一个能挂在 macOS 桌面上的"今天吃啥"挂件。

工作日中午站在公司楼下犹豫 10 分钟、晚上下班对着外卖 App 滑半小时，全部交给它来决定。

## 文件结构

```
What2Eat/
├── wallpaper.html          完整版（480×620，大卡片风格）
├── widget-compact.html     挂件版（360×200，macOS 原生挂件比例，默认）
├── menu.md                 菜单池
├── test-click.html         点击调试用（可忽略）
└── README.md
```

两套设计可以一键切换，按 `⌥⌘T` 即可。

## 桌面挂件部署（macOS）

通过 [Hammerspoon](https://www.hammerspoon.org/) 把 HTML 渲染成桌面挂件。比 Übersicht 稳，可点击、可拖动、可贴桌面。

### 一次性安装

```bash
# 1. 安装 Hammerspoon
brew install --cask hammerspoon

# 2. 启动 Hammerspoon，授权"系统设置 → 隐私与安全性 → 辅助功能"
open -a Hammerspoon
```

> Hammerspoon 配置文件已经写好在 `~/.hammerspoon/init.lua`，**第一次启动时它会自动加载，挂件出现在屏幕角落**。

### 快捷键速查

| 快捷键 | 作用 |
|---|---|
| **⌥⌘E** | 激活 ⇄ 贴桌面（**贴桌面时点击会穿透**，需先激活才能点按钮） |
| **⌥⌘T** | 切换两套设计（完整版 / 挂件版） |
| **⌥⌘R** | 刷新页面（改了 HTML 后用） |
| **⌥⌘W** | 重载 Hammerspoon 配置（改了 init.lua 后用） |
| **⌥⌘ ← / →** | 在多个屏幕间切换 |
| **⌥⌘ 1 / 2 / 3 / 4** | 贴到屏幕的左上 / 右上 / 左下 / 右下角 |

### 鼠标操作

| 操作 | 效果 |
|---|---|
| 拖**挂件最顶部那条小灰条** | 移动挂件位置；松手时自动吸到最近的角落 + 屏幕，并持久化 |
| 点击挂件外的任意位置 | 自动从激活模式缩回桌面层 |

### 双层级模式说明

挂件有两种状态，平衡 "不打扰 / 可操作" 这对天然矛盾：

- **贴桌面 (默认)**：挂件钉在桌面图标层，**任何应用窗口都会盖住它**，不打扰工作；代价是这个层级 macOS 不接受鼠标事件，所以**点击会穿透到桌面**
- **激活**（按 `⌥⌘E`）：挂件浮到最前，可以点按钮、拖位置；操作完再按一次 `⌥⌘E` 或点击别的窗口失焦，自动缩回桌面层

> 这是 macOS 的硬性约束（`kCGDesktopWindowLevel` 不接收点击）。Übersicht 的同款问题就是这么来的。我们这套方案把"临时进入交互"做成了一个快捷键，比 Übersicht 的 ⌘+点击更顺手。

## 修改菜单

在 `menu.md` 维护菜品池，但目前菜品 + 文案是写死在两个 HTML 里的 `dishes` 数组里的（一菜两条文案）。改完按 `⌥⌘R` 刷新挂件即可看到。

特殊规则（写死在 `pick()` 里）：
- 麦当劳：仅周五晚餐出现
- 老乡鸡：仅晚餐出现

## 状态持久化

`~/.hammerspoon/widget-state.lua` 自动记忆：
- 当前所在屏幕
- 屏幕角落位置
- 当前选用的设计（完整版 / 挂件版）

下次开机 Hammerspoon 启动后，挂件会停在你上次放的位置、用上次选的设计。

## 直接在浏览器里用

不想装 Hammerspoon？直接 `open wallpaper.html` 或 `open widget-compact.html` 双击在浏览器里也能用。

## OpenSpec 工作流

本仓库已接入 [OpenSpec](https://github.com/Fission-AI/OpenSpec)（规约驱动开发），改动建议走：

```
/opsx:propose <想法>   →  人工 review markdown  →  /opsx:apply  →  /opsx:archive
```

详见 `openspec/` 目录。
