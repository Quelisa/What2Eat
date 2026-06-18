# desktop-widget — 桌面挂件能力

## Purpose

让用户在 macOS 桌面上以零摩擦的方式获取每日午餐 / 晚餐推荐。挂件需要在"不打扰"和"可操作"之间取得平衡，提供拖拽、自适应尺寸、双设计切换等便利能力。
## Requirements
### Requirement: 启动即出推荐

挂件 SHALL 在加载时根据系统时间自动判定餐次并立即给出一条菜品推荐 + 文案。

#### Scenario: 默认推荐在加载后 1 秒内出现

- **WHEN** Hammerspoon 启动并加载挂件
- **THEN** 挂件在 1 秒内自动显示一条菜品推荐和对应文案
- **AND** 餐次根据系统时间自动判定：10:00–14:59 设为中餐，其他时段设为晚餐

### Requirement: 换一家按钮

挂件 SHALL 提供一个主按钮，点击后重新抽取一道菜并触发对应的随机文案。

#### Scenario: 点击换一家立即出新结果

- **WHEN** 用户点击「吃啥」/「换一家」按钮
- **THEN** 挂件立即显示一道新菜品和该菜品对应的一条文案
- **AND** 文案从该菜品的多条文案中随机抽取
- **AND** 新菜品允许与上一次相同（避免菜品池小时无法刷新）

### Requirement: 手动切换星期与餐次

挂件 SHALL 允许用户手动切换星期和餐次，候选集 MUST 相应过滤。

#### Scenario: 切换后下一次推荐基于新维度

- **WHEN** 用户把星期切到 X、餐次切到 Y 后点「换一家」
- **THEN** 推荐仅从「在 X 星期 Y 餐次可吃」的菜品集合中产生

### Requirement: 基础菜品池

菜品和文案 MUST 直接硬编码在 HTML 的 `dishes` 数组中，不引入外部数据源。

#### Scenario: 菜品最低数量与文案

- **WHEN** 检查任一 HTML 文件的 `dishes` 数组
- **THEN** 至少包含：烧鸭、瓦罐汤、喜家德、老乡鸡、麦当劳、自选菜、酱骨、跷脚牛肉、寿喜锅、云南生烫米线、轻食 共 11 个菜品
- **AND** 每个菜品至少配 2 条文案

### Requirement: 麦当劳限定

麦当劳作为「周五加班犒劳」专属菜品，MUST 仅在周五晚餐出现。

#### Scenario: 非周五或非晚餐不出现麦当劳

- **WHEN** 当前组合为「非周五」或「中餐」
- **THEN** 麦当劳不出现在 `pick()` 的候选集中

### Requirement: 老乡鸡限定

老乡鸡 MUST 仅在晚餐时段出现。

#### Scenario: 中餐时段不出现老乡鸡

- **WHEN** 当前餐次为中餐
- **THEN** 老乡鸡不出现在候选集中

### Requirement: 候选为空兜底

任何过滤组合 MUST NOT 让用户看到空白或异常。

#### Scenario: 候选集为空时显示提示

- **WHEN** 当前 (星期, 餐次) 组合下没有任何可推荐菜品
- **THEN** 挂件显示提示文本（例如「今天换个口味吧」）
- **AND** 不抛出异常、不停在空白

### Requirement: 默认贴桌面层

挂件 SHALL 启动时进入"贴桌面"状态，不打扰任何前台应用。

#### Scenario: 启动后被任何应用窗口盖住

- **WHEN** Hammerspoon 启动加载 `init.lua`
- **THEN** 挂件窗口层级设为 `desktop + 1`，被任何应用窗口盖住
- **AND** 鼠标点击会穿透到桌面（这是 macOS `kCGDesktopWindowLevel` 的固有行为）

### Requirement: 激活模式可点击

挂件 SHALL 提供 ⌥⌘E 快捷键进入激活模式，浮到最前接收所有事件。

#### Scenario: 激活后所有控件响应点击

- **WHEN** 用户按 ⌥⌘E
- **THEN** 挂件层级升到 `floating`，浮在普通应用之上
- **AND** 所有按钮（换一家、星期 / 餐次切换、拖拽条）都可响应鼠标

#### Scenario: 失焦自动收起

- **WHEN** 挂件处于激活模式且失去主窗口焦点
- **THEN** 挂件自动回到贴桌面模式

### Requirement: 拖拽条移动挂件

挂件最顶部 22–36 像素的拖拽条 SHALL 响应鼠标拖动；其他区域 MUST NOT 触发拖拽。

#### Scenario: 拖拽条按下并移动跟随鼠标

- **WHEN** 用户在挂件最顶部拖拽条按下鼠标左键并拖动
- **THEN** 挂件跟随鼠标移动
- **AND** 在拖拽条之外的区域按下不触发拖拽

#### Scenario: 松手吸到角落并持久化

- **WHEN** 用户拖动挂件后松开鼠标
- **THEN** 挂件根据中心点位置吸到该屏幕的「左上 / 右上 / 左下 / 右下」之一
- **AND** 新位置写入 `~/.hammerspoon/widget-state.lua`

### Requirement: 键盘定位

挂件 SHALL 通过快捷键直接切屏幕和角落。

#### Scenario: 按角落数字键直达对应角

- **WHEN** 用户按 ⌥⌘1 / ⌥⌘2 / ⌥⌘3 / ⌥⌘4
- **THEN** 挂件分别贴到当前屏幕的左上 / 右上 / 左下 / 右下角

#### Scenario: 方向键切屏幕

- **WHEN** 用户按 ⌥⌘ → 或 ⌥⌘ ←
- **THEN** 挂件循环切换到下一块或上一块屏幕，并保持当前角落

### Requirement: 内容驱动尺寸

窗口尺寸 MUST 由网页内容决定，永不出现内部滚动条。

#### Scenario: 网页报告尺寸窗口跟随

- **WHEN** 网页内容变化（推荐卡片显隐、文案长短）
- **THEN** 网页通过 `webkit.messageHandlers.hsbridge.postMessage({type:"resize", height, width})` 报告新尺寸
- **AND** Hammerspoon 调整窗口尺寸（高度限 120–1200px，宽度限 200–1200px），并按当前 corner 重新定位

#### Scenario: 永不出现滚动条

- **WHEN** 检查任一 HTML 文件
- **THEN** `body` 和 `html` 设有 `overflow: hidden`
- **AND** 内容超出靠扩大窗口承接，而非让用户在挂件内滚动

### Requirement: 双设计可切换

挂件 SHALL 提供完整版与紧凑挂件版两套 UI，用户用 ⌥⌘T 切换。

#### Scenario: 首次启动默认紧凑挂件版

- **WHEN** 用户首次启动 Hammerspoon（`widget-state.lua` 不存在或缺 `design` 字段）
- **THEN** 挂件加载 `widget-compact.html`（默认 360×200）

#### Scenario: 切换记忆选择

- **WHEN** 用户按 ⌥⌘T
- **THEN** 挂件切换到另一份 HTML（`compact` ↔ `full`）
- **AND** 新选择写入 `widget-state.lua`
- **AND** 窗口尺寸重置为该设计的默认值，再由 resize 消息按内容自适应

### Requirement: 状态持久化

屏幕、角落、设计三项状态 MUST 在 Hammerspoon 重启后保持。

#### Scenario: 状态文件格式

- **WHEN** 写入 `~/.hammerspoon/widget-state.lua`
- **THEN** 文件可被 `dofile` 加载为 Lua 表
- **AND** 字段包含：`screenIndex`（整数）、`corner`（"top-left" / "top-right" / "bottom-left" / "bottom-right"）、`design`（"compact" / "full"）

#### Scenario: 缺字段使用默认值

- **WHEN** 状态文件存在但缺某字段
- **THEN** 该字段用默认值填充：`screenIndex=1`, `corner="bottom-right"`, `design="compact"`
- **AND** 不报错

### Requirement: 快捷键统一修饰键

所有全局快捷键 MUST 使用 ⌥⌘ 修饰键，避免与 macOS 系统手势冲突。

#### Scenario: 现有快捷键覆盖

- **WHEN** 检视 `init.lua` 的快捷键绑定
- **THEN** 包含：⌥⌘E（激活/贴桌面）、⌥⌘T（切换设计）、⌥⌘R（刷新）、⌥⌘W（重载配置）、⌥⌘ ← / →（切屏幕）、⌥⌘ 1 / 2 / 3 / 4（贴角落）

#### Scenario: 新增快捷键沿用约定

- **WHEN** 后续 change 引入新快捷键
- **THEN** 该快捷键使用 ⌥⌘ 修饰键
- **AND** 在 `init.lua` 同名表格和 README 中同步登记

### Requirement: 卡片阴影柔和契约

挂件卡片的阴影 MUST 视觉柔和、沿圆角形状投影，**不得**在挂件底部透明区中留下肉眼可辨识的暗色段。

#### Scenario: 阴影使用 drop-shadow 而非 box-shadow

- **WHEN** 检视 `widget-compact.html` 中 `.widget` 选择器的样式
- **THEN** 该选择器使用 `filter: drop-shadow(...)` 提供阴影
- **AND** 不使用 `box-shadow`
- **WHEN** 检视 `wallpaper.html` 中 `.glass-card` 选择器的样式
- **THEN** 该选择器使用 `filter: drop-shadow(...)` 提供阴影
- **AND** 不使用 `box-shadow`

#### Scenario: 阴影偏移与模糊参数克制

- **WHEN** 检视任一卡片选择器的 `drop-shadow` 参数
- **THEN** y-offset MUST ≤ 10px
- **AND** 模糊半径 MUST ≤ 24px
- **AND** 不透明度（rgba 第四位）MUST ≤ 0.18

#### Scenario: 视觉验收 — 底部无可辨灰条

- **WHEN** 用户在任一设计（紧凑版 / 完整版）下加载挂件
- **THEN** 卡片下方的透明窗口区域 MUST NOT 出现一段长度明显短于卡片宽度的中性灰色色块
- **AND** 阴影 MUST 沿卡片圆角对称地呈现柔和环绕效果

#### Scenario: 后续样式改动仍受约束

- **WHEN** 后续 change 修改卡片或新增卡片样式（如 hover 态、新设计）
- **THEN** 新样式 MUST 通过相同的视觉验收（无可辨灰条、参数在上限内）
- **AND** change 的 tasks.md MUST 包含一条手动视觉验证项

