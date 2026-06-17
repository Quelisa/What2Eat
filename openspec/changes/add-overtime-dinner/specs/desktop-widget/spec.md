## ADDED Requirements

### Requirement: 加班晚餐慰藉推荐

工作日 21:00 之后的晚餐时段被识别为"加班晚餐"。在该时段内，未手动改过控件的用户 SHALL 优先看到被标记为 `comfort: true` 的慰藉系菜品；若 comfort 子集为空，挂件 MUST 回退到正常候选集，避免空白。

#### Scenario: 进入加班时段，候选集优先收窄到 comfort

- **WHEN** 系统时间为周一至周五的 21:00–23:59，且当前餐次为「晚餐」，且用户尚未点击过任何星期 / 餐次切换控件
- **THEN** `pick()` 的候选集首先仅包含 `dish.comfort === true` 的菜品
- **AND** 推荐结果必然是 {酱骨, 寿喜锅, 跷脚牛肉, 瓦罐汤} 的子集

#### Scenario: comfort 子集为空时回退

- **WHEN** 加班时段下，按 R2.2 / R2.3 等已有规则过滤后，comfort 子集为空
- **THEN** `pick()` 回退到完整的、按已有规则过滤后的候选集
- **AND** 不会显示空白或抛错

#### Scenario: 用户手动改控件后不再应用 comfort 偏好

- **WHEN** 用户处于加班时段，但点击了星期或餐次切换控件
- **THEN** 后续 `pick()` 调用按 R1.3 标准逻辑执行
- **AND** 不再应用 comfort 偏好

#### Scenario: 页面刷新重置用户覆盖

- **WHEN** 用户按 ⌥⌘R 刷新挂件，或 `autoDetect()` 重新执行
- **THEN** `userTouchedSelector` 被重置为 false
- **AND** 加班 comfort 偏好恢复生效

#### Scenario: comfort 标记菜品

- **WHEN** 检视任一 HTML 文件中的 `dishes` 数组
- **THEN** 至少 酱骨、寿喜锅、跷脚牛肉、瓦罐汤 这 4 个菜品带有 `comfort: true`
- **AND** 其余菜品默认不带该字段
