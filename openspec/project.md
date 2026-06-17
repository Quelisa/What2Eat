# What2Eat 项目宪章

## 项目宗旨

帮用户摆脱"今天吃啥"的决策疲劳。每天工作日的中午和晚上，自动给出一个不带情感负担的随机推荐 + 暖心文案。

## 设计原则

1. **零摩擦**：决策出现在桌面上，不需要打开任何 App，不需要登录、不需要联网。
2. **不打扰**：默认贴在桌面层，被任何应用窗口盖住。需要换菜时才唤起。
3. **可玩性 > 实用性**：菜品文案为主角，多条文案随机出现，避免推荐乏味化。
4. **本地优先**：菜品和文案直接写在 HTML 里，不引入任何依赖、不调外部 API。

## 技术栈

- **前端**：纯静态 HTML + CSS + JavaScript（无构建、无框架、无 npm）
- **桌面集成**：[Hammerspoon](https://www.hammerspoon.org/) (`hs.webview` + Lua 配置)
- **跨进程通信**：`webkit.messageHandlers` 桥（JS → Lua）实现拖拽和自适应尺寸
- **规约驱动**：[OpenSpec](https://github.com/Fission-AI/OpenSpec) 管理需求演进

## 关键约束

- **macOS only**：`hs.webview` 与桌面层级 (`kCGDesktopWindowLevel`) 都是 macOS 概念，不跨平台。
- **不能既贴桌面又可点击**：这是 macOS 系统级约束，所有桌面挂件方案（Übersicht / Plash / 我们的 Hammerspoon 方案）都无法绕过。我们的设计是**双层级 + 一键切换**。
- **HTML 文件即真理**：菜品池、挑选规则、UI 全部写在 `wallpaper.html` / `widget-compact.html` 里。`menu.md` 仅作人类可读的菜名清单参考，不是数据源。
- **不引入构建工具**：保持 `open file.html` 双击即用、无任何 npm/build 步骤的简洁。

## 仓库布局

```
What2Eat/
├── wallpaper.html              完整版挂件 UI（480×620）
├── widget-compact.html         紧凑挂件 UI（360×200，默认）
├── menu.md                     菜单池（人类可读快速参考）
├── README.md                   用户向使用说明
└── openspec/                   规约驱动开发资产
    ├── project.md              本文件
    ├── specs/<capability>/     已沉淀的主规约（按能力组织）
    └── changes/<change>/       进行中的变更
```

`~/.hammerspoon/init.lua` 是这个项目运行时的关键依赖（虽然不在仓库里），由 README 中描述的部署流程生成。

## 决策风格

- **小步快跑**：一个 change 只解决一个能讲清楚的小事，不堆需求。
- **可逆默认**：所有快捷键、屏幕、设计选择都持久化到 `~/.hammerspoon/widget-state.lua`，用户随时改。
- **拒绝过度抽象**：菜品 + 文案直接硬编码在 JS 里。需要数据库化时再说。
