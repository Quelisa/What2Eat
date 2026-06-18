## ADDED Requirements

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
