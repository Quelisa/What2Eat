## Why

`desktop-widget` 当前的限定规则只覆盖**菜品级别**的过滤（麦当劳=周五晚、老乡鸡=晚餐），都是基于 (星期, 餐次) 二元组的硬开关。

用户反馈：周五加班到 21 点之后还在公司时，希望挂件能优先推**重口味、慰藉感强**的菜（酱骨、寿喜锅、跷脚牛肉、瓦罐汤），而不是和平时一样的全菜池。这是**时段级别**的偏好，不是菜品级别的限制——已有的 `pick()` 函数处理不了。

## What Changes

- 给 `dishes` 数组中的「酱骨、寿喜锅、跷脚牛肉、瓦罐汤」追加 `comfort: true` 字段
- 在 `pick()` 中引入"加班时段"判断：工作日 21:00 之后的晚餐，候选集**优先**只保留 comfort 菜品；comfort 子集为空时回退到完整候选集
- 引入 `userTouchedSelector` 状态：用户主动改过星期 / 餐次后，加班分支不再生效（避免覆盖用户意图）
- README 在「特殊规则」小节补一条说明

## Capabilities

### New Capabilities

无新增能力。

### Modified Capabilities

- `desktop-widget`: 新增「加班晚餐慰藉推荐」需求，作为 R2 菜品池规则的扩展

## Impact

- `wallpaper.html`：`dishes` 数组加 `comfort` 标记；`pick()` 加时段分支；选择控件事件加 `userTouchedSelector` 置位
- `widget-compact.html`：同上
- `README.md`：「修改菜单」章节的特殊规则列表加一条
- 不影响：双层级模式 (R3)、拖拽定位 (R4)、自适应尺寸 (R5)、双设计 (R6)、状态持久化 (R7)、快捷键 (R8)
