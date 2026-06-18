# Tasks: 修复卡片阴影底部"灰条"

## 1. 紧凑挂件版样式
- [x] 1.1 在 `widget-compact.html` 中找到 `.widget` 选择器（约第 20–32 行）
- [x] 1.2 删除 `box-shadow: 0 12px 40px rgba(0, 0, 0, 0.16);`
- [x] 1.3 新增 `filter: drop-shadow(0 6px 18px rgba(0, 0, 0, 0.14));`

## 2. 完整版样式
- [x] 2.1 在 `wallpaper.html` 中找到 `.glass-card` 选择器（约第 26–35 行）
- [x] 2.2 删除 `box-shadow: 0 16px 56px rgba(0, 0, 0, 0.18);`
- [x] 2.3 新增 `filter: drop-shadow(0 8px 22px rgba(0, 0, 0, 0.16));`

## 3. 手动视觉验证
- [x] 3.1 按 `⌥⌘R` 刷新挂件，观察卡片底部不再出现可辨识的灰色窄条
- [x] 3.2 按 `⌥⌘T` 切到完整版，重复观察
- [x] 3.3 拖动挂件到屏幕中央（远离任何屏幕边缘），确认阴影沿圆角对称分布
- [x] 3.4 按 `⌥⌘E` 在贴桌面 / 激活模式间切换，确认阴影外观一致

## 4. 校验与归档准备
- [x] 4.1 运行 `openspec validate fix-card-shadow --strict`，期望输出 `Change ... is valid`
- [x] 4.2 运行 `openspec validate desktop-widget --type spec --strict`（合并前预演），期望输出 `is valid`
- [x] 4.3 提交 git diff（仅 `widget-compact.html` 和 `wallpaper.html` 各 1 行变更）

## 5. 归档
- [x] 5.1 视觉验证全部通过后，运行 `/opsx:archive fix-card-shadow`
- [x] 5.2 确认 `openspec/specs/desktop-widget/spec.md` 已合并新增 Requirement「卡片阴影柔和契约」
- [x] 5.3 确认 change 移到 `openspec/changes/archive/<date>-fix-card-shadow/`
