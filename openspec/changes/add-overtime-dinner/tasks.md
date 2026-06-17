# Tasks: 加班晚餐慰藉推荐

## 1. 数据：给 dishes 数组加 comfort 字段
- [ ] 1.1 `wallpaper.html` 中 `dishes` 给 酱骨 / 寿喜锅 / 跷脚牛肉 / 瓦罐汤 加 `comfort: true`
- [ ] 1.2 `widget-compact.html` 同步修改

## 2. 状态：引入 userTouchedSelector
- [ ] 2.1 `wallpaper.html` 在 `<script>` 顶部声明 `let userTouchedSelector = false`
- [ ] 2.2 在所有控件的事件处理（步进器点击、滑块拖动）中置 `userTouchedSelector = true`
- [ ] 2.3 在 `autoDetect()` 末尾重置 `userTouchedSelector = false`
- [ ] 2.4 `widget-compact.html` 同样改造（事件源是 `#weekday` 文本和 `#meal-seg` 按钮）

## 3. 逻辑：pick() 加加班分支
- [ ] 3.1 在 `pick()` 内、`available` 计算之后，按 design.md 的方案 A 加一段时段判断分支
- [ ] 3.2 校验回退路径（comfort 为空时不要把 `pool` 设为 `[]`）

## 4. 文档
- [ ] 4.1 README "特殊规则" 小节加一行："加班时段 (工作日 21 点后晚餐)：优先推荐 comfort 菜品"
- [ ] 4.2 `menu.md` 不修改（不是数据源）

## 5. 手动验证
- [ ] 5.1 把系统时间临时改到周三 21:30，按 ⌥⌘R 刷新挂件，连点 5 次「换一家」，所有结果应都在 {酱骨, 寿喜锅, 跷脚牛肉, 瓦罐汤} 集合内
- [ ] 5.2 同样时间下点一下「中餐」按钮，再点「换一家」，应能命中麦当劳之外的全菜池
- [ ] 5.3 把系统时间改到周二 19:00，刷新，多次「换一家」应能出现 comfort 之外的菜（如喜家德、轻食）

## 6. 归档前自检
- [ ] 6.1 `openspec validate add-overtime-dinner --strict` 通过
- [ ] 6.2 主规约 `openspec/specs/desktop-widget/spec.md` 已通过 archive 流程合并新增的 R2.5
