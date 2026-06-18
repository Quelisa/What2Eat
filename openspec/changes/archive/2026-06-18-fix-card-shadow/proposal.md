## Why

在桌面挂件（紧凑版 `widget-compact.html` 和完整版 `wallpaper.html`）下方，会出现一段**冷灰色的小条**。用户视觉确认其特征：

- 一小段，**不是横贯整宽**
- 出现在**卡片圆角之外**
- 颜色**偏冷灰**
- 顶部**不存在**对应物

根本原因是卡片元素的 `box-shadow` 设计：

| 文件 | 当前 box-shadow |
|---|---|
| `widget-compact.html` | `0 12px 40px rgba(0, 0, 0, 0.16)` |
| `wallpaper.html` | `0 16px 56px rgba(0, 0, 0, 0.18)` |

正向 12–16px 的 `y-offset` 把阴影压到卡片下方，圆角 22–28px 又把阴影两侧"啃掉"——结果在卡片底部正中留下了一段最浓的中性灰投影。同时网页通过 `reportSize()` 上报的尺寸用的是 `getBoundingClientRect()`（**不包含阴影**）+ 24px 边距，恰好让阴影最浓的那一段落在了挂件窗口的底部透明区里，肉眼看就是"挂件下方有一道灰条"。

这个问题违反了项目宪章中"零摩擦 / 不打扰"的视觉直觉——挂件应该看起来浮在桌面上、轻盈干净，而不是带着一道明显的暗色拖痕。

## What Changes

- 把两个 HTML 文件中卡片的 `box-shadow` 改为**沿卡片形状（含圆角）柔和投影**的方案，避免出现可见的"暗色拖痕"
- 在 `desktop-widget` 主规约中新增一条 Requirement：**卡片阴影需视觉柔和、不得在挂件底部留下可辨识的硬色段**——把这个视觉契约写进规约，防止后续样式重构时回退

## Capabilities

### New Capabilities

无新增能力。

### Modified Capabilities

- `desktop-widget`：新增「卡片视觉外观」相关 Requirement，明确阴影约束

## Impact

- `widget-compact.html`：`.widget` 选择器的 `box-shadow` 替换
- `wallpaper.html`：`.glass-card` 选择器的 `box-shadow` 替换
- `openspec/specs/desktop-widget/spec.md`：通过本 change 的 archive 流程合入新增的 Requirement
- 不影响：菜品池、双层级模式、拖拽定位、自适应尺寸、双设计切换、状态持久化、快捷键
