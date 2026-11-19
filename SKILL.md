---
name: souldesk-widget
description: 根据需求创建、修改、打包或本地验证 SoulDesk 桌面组件。适用于在组件广场开发正式组件，或在宿主平台验证组件接口及原型。
---

# SoulDesk 组件开发

## 确定工作目录

- 组件创建、维护及广场收录默认从同时包含 `sdk.lock.json`、`scripts/widget.py` 和 `widgets/` 的组件广场仓库进行。找到项目中已关联的广场目录，并从该目录执行构建、检查和 Git 操作。
- 宿主平台负责 SDK、能力网关与安装验证。任务明确要求宿主接口、原型或夹具时，从同时包含 `sdk/README.md` 和 `src-tauri/` 的宿主仓库工作。独立 Skill 仓库维护本工作流。
- 同时涉及多个仓库时，按实际任务决定变更范围。核对目标仓库的 `AGENTS.md`、`README.md` 和 Git 状态。

## 创建和维护组件

1. 从需求提取显示内容、操作、数据来源和持久化。用现有组件确认目录和交互做法，已有组件保持 ID、配置和实例数据兼容。原型可参考宿主的协议夹具。使用第三方代码或资产时核对固定来源和许可。
2. 阅读宿主 SDK 契约及所需能力，按目标宿主版本核对支持范围。广场以 `sdk.lock.json` 固定的 SDK 构建；新能力需确认宿主实现及最低兼容版本。只声明实际使用的权限，选择尽量少的依赖。
3. 新包使用稳定 ID、数字版本、`hostApiMajor: 1`、`loadCheck: 1` 和 `dist/` 下的入口。使用相对资源、外部脚本和 SDK 消息桥。完成初始化再调用 `host.ready()`；隐藏试加载用隔离数据，默认页面无需外部服务即可就绪。写入等待宿主确认，失败保留草稿。已导入的同 ID 包内容有变化时升版。

## 打包和验证

- 广场源码位于 `widgets/<目录>/`，资源在 `src/`。从广场根目录运行 `python scripts/widget.py <目录>`，按广场 README 检查 ZIP、SHA-256 及发布说明。
- 宿主原型的平铺 HTML/CSS/JS 源码可从宿主根目录运行 `node scripts/build-example.mjs <源码目录>`，从 `artifacts/` 取包。宿主专用测试入口见宿主 README。
- 本地导入优先使用隔离宿主。在管理窗口「导入本地 ZIP」后从组件库添加，验证一条主要操作、重开恢复和适用的实例隔离。区分构建、隐藏试加载、可见实例和已安装宿主实测。
- 上架时走广场现有的 Release 及目录签名流程，仅对已验收且许可允许分发的 ZIP 填写最低宿主版本及 SHA-256。打包、导入请求的交付内容包括源码、ZIP 和实际验证结论。

流程参考 [WidgetJS 的组件开发 Skill](https://github.com/widget-js/skills/tree/master/widget) 和 [Kepo 的插件开发 Skill](https://github.com/Enivel/kepo-plugin-dev-skill/tree/main/skills/kepo-plugin-dev)。
