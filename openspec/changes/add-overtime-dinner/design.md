# Design: 加班晚餐慰藉推荐

## 设计取舍

### 方案 A（采纳）：在 `pick()` 内做时段判断

伪代码：
```javascript
function pick() {
  const now = new Date();
  const isWeekday   = now.getDay() >= 1 && now.getDay() <= 5;
  const isLateNight = now.getHours() >= 21;
  const userOverrode = userTouchedSelector;  // 新增标志

  const baseAvailable = dishes.filter(/* 已有 R2.2/R2.3 规则 */);

  let pool = baseAvailable;
  if (isWeekday && isLateNight && !userOverrode && mealOptions[mealIndex] === '晚餐') {
    const comfort = baseAvailable.filter(d => d.comfort);
    if (comfort.length > 0) pool = comfort;   // 加班 comfort 子集
  }

  const dish = pool[Math.floor(Math.random() * pool.length)];
  // ... 渲染
}
```

**优点**：
- 改动局限在一个函数内，不引入额外模块
- 退化路径清晰（场景 R2.5.2 的回退就是 if 分支不进入）

**缺点**：
- `userTouchedSelector` 是新增的全局状态，需要在切换控件的事件里 `userTouchedSelector = true`

### 方案 B（拒绝）：把"时段 → 菜池"做成查表函数

会引入一个 "时段类型" 的中间抽象，但项目宪章明确"拒绝过度抽象"，目前只有两条特殊规则 + 一条加班规则，方案 A 的 if 链还能撑住。等到第 5 条特殊规则时再考虑重构。

### 方案 C（拒绝）：让用户配置 comfort 标记

需要一个设置面板。和"零配置"宗旨冲突，砍掉。

## `userTouchedSelector` 的生命周期

- 初始 `false`
- 任何 `weekday-stepper` / `meal-stepper`（完整版）或 `weekday` 文本 / `meal-seg` 按钮（紧凑版）的 click/change 事件触发时置 `true`
- `autoDetect()` 重新执行（页面加载）时重置回 `false`

## 测试场景对照

| 场景 | 时间 | 用户操作 | 期望 |
|---|---|---|---|
| 普通晚餐 | 周二 19:00 | 不动 | 全候选池 |
| 加班晚餐 | 周三 21:30 | 不动 | 仅 comfort 菜品 |
| 加班但用户选了周一中餐 | 周三 21:30 | 切到 中餐 | 走 R1.3，不应用 comfort |
| 周五加班晚餐 | 周五 22:00 | 不动 | comfort 子集（含寿喜锅、酱骨…，**不含**麦当劳因为麦当劳 `comfort: false`） |

## 风险

- 如果加班时段同时命中 R2.2 麦当劳条件，麦当劳虽然过 R2.2 但不过 comfort 过滤，这是合预期的（麦当劳不算 comfort 食物）
- 时间判断用本地 `Date.getHours()`，跨时区或服务器时间错乱的风险——本项目本地运行无此风险
