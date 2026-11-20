---
name: souldesk-widget
description: 设计、创建、维护、打包和隔离验证 SoulDesk 桌面组件。适用于组件广场开发、既有组件升级，以及验证宿主 SDK 的组件原型和协议夹具。
---

# SoulDesk 组件开发

## 找到任务所属仓库

- 正式组件默认在组件广场开发，以同一根目录下的 `widgets/`、`sdk.lock.json` 和 `scripts/widget.py` 确认仓库。组件目录为 `widgets/<目录>/`，包含 `manifest.json`、`src/`、`README.md` 和 `CHANGELOG.md`。
- 宿主仓库以 `sdk/README.md` 和 `src-tauri/` 确认，维护 SDK、权限、运行时和桌面隔离验收。验证宿主接口的原型可从 `tests/fixtures/` 中选同类夹具参考。
- 独立 Skill 仓库维护本文件，宿主通过 `.agents/skills/souldesk-widget/` 接入。按工作区实际路径定位各仓库，从所属仓库根目录执行命令。先核对其 Git 状态、`AGENTS.md` 和 `README.md`。

## 组件设计

1. 先梳理使用场景、要展示的信息、主要操作、数据来源、实例存储及目标尺寸。对搜索、筛选、编辑等通用功能，查阅当前主流同类产品的可核实做法，结合 SoulDesk 宿主能力和组件尺寸选择交互。
2. 新组件默认参考[「清晰均衡」组件 UI 设计](references/ui-design.md)。字号、间距、圆角、控件与状态样式均可按场景调整，各组件保留自己的配色。
3. 需要设计界面时，先提供 3–4 套可比较的样式，用图片或独立 HTML 预览展示。使用相同的主要功能与示例内容，说明各方案在布局、信息密度和交互上的取舍。待用户选定或确认组合方案后，再编写组件源码。已有明确确认的方案可直接实施，现有组件的局部修复沿用原界面。

## 实现或更新组件

1. 确认目标宿主版本。简单组件可参考广场的 `widgets/local-clock/` 或 `widgets/daily-note/`。维护迁入组件时先确认源码归属。若资源由 `source/widgetjs-port/` 生成，按该目录的 README 修改源文件并重建，再更新对应的 `widgets/<目录>/src/`。
2. 以宿主 `sdk/README.md` 和目标版本实现核对接口、权限及组件身份限制。广场以 `sdk.lock.json` 构建，构建器注入并校验 `host.js`。组件通过 SDK 消息桥调用宿主，只声明实际需要的能力。若需求超出已有宿主能力，将宿主扩展和升级要求纳入任务范围确认。
3. 新包使用稳定 ID、数字版本、`schemaVersion: 1`、`hostApiMajor: 1`、`loadCheck: 1` 和 `dist/` 下的 HTML 入口。使用相对资源与外部脚本，按 SDK 约定配置尺寸和默认值。先初始化本地界面，再 `await host.ready()`。在 `host.preview` 为真时跳过真实网络和有副作用的操作，让隐藏试加载可独立完成。
4. 更新既有包时保留 ID，保证旧实例配置和存储可读。包内容变化时提高版本并更新 `CHANGELOG.md`。保存成功以宿主响应为准，失败保留用户输入。复用第三方代码或资产时核对固定来源与分发许可。

## 组件通知

组件需要弹出提醒时，查阅宿主 `sdk/README.md` 的「组件通知（`notifications.send`）」及目标版本实现。启用该能力的包在清单声明 `notifications.send`，业务事件发生后通过 `host.request('notifications.send', payload)` 提交，在参数中明确选择 `channel`。隐藏试加载（`host.preview`）跳过发送。

- `channel: 'windows'` 适合系统文字通知，`channel: 'island'` 适合顶部胶囊、按钮、实时倒计时和来电交互。两路在宿主设置中独立控制，默认都开启。被用户关闭的渠道返回 `suppressed`。同一事件确需发两路时，每路使用独立的请求 UUID。
- `kind` 支持 `info`、`success`、`warning`、`error`、`reminder`、`advance-countdown`、`countdown`、`call`。常规通知提供 `title` 与可选 `body`，两种倒计时另外提供 Unix 毫秒 `targetAt`，模拟来电只提供宿主固定 `preset`。
- 来电还需在清单声明 `notifications.call`，并由用户在宿主设置显式开启。双按钮提醒由宿主处理「打开组件」和「关闭」。
- 每次实际业务事件生成 UUID `requestId`，同一事件重试保留原 ID 和参数。`accepted` 表示宿主或系统受理，`suppressed` 表示用户设置抑制。倒计时目标仅控制已触发通知的显示，组件关闭后仍需计时的任务使用已有宿主后台协议。
- 用支持该接口的隔离宿主分别验证实际组件通知、所选渠道关闭时的抑制及重复请求，再填写广场最低宿主版本。

## 打包和隔离验证

- **正式组件**从广场根目录运行 `python scripts/widget.py <目录>`，在 `artifacts/widgets/` 查看 ZIP、SHA-256 和发布说明。离线构建可传 `--sdk <host.js路径>`，脚本会与锁定摘要核对。
- **宿主原型**从宿主根目录运行 `node scripts/build-example.mjs <源码目录>`。此入口适用于平铺的 HTML/CSS/JS 和清单，使用当前宿主 SDK，产出位于宿主的 `artifacts/`。
- **本地验证**从宿主根目录运行 `pnpm dev`，使用默认隔离数据。通过管理窗口「导入本地 ZIP」，再从组件库添加可见实例。验证主要操作、重开恢复，以及适用的多实例隔离或带数据升版。安装版验证按宿主 README 选择对应入口。
- **上架**按广场 README 核验许可、实际验收的 ZIP 与最低宿主版本，再走 Release 和签名目录流程。提交、推送与发布均按用户明确授权执行。

交付时给出组件源码位置及生成的 ZIP 和 SHA-256。报告实际验证的宿主版本和范围，区分打包、隐藏试加载、可见实例交互和安装版实测。
