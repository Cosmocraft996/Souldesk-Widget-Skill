# SoulDesk 组件开发 Skill

从需求生成、修改并验证 SoulDesk 组件。Skill 由本仓库维护，支持宿主原型与组件广场两条开发入口。

| 仓库 | 职责 |
| --- | --- |
| [SoulDesk](https://github.com/Cosmocraft996/SoulDesk) | Windows 宿主、组件 SDK、运行接口和隔离验证 |
| [SoulDesk 组件广场](https://github.com/Cosmocraft996/SoulDesk-Widgets) | 正式组件源码、ZIP 构建、发布和签名目录 |
| 本仓库 | [组件开发 Skill](SKILL.md) |

## 在 Codex 使用

以 SoulDesk 宿主为 Codex 项目的主目录，并关联广场和 Skill 两个仓库。将本仓库接入宿主的 `.agents/skills/souldesk-widget/`，从 SoulDesk 项目聊天中调用 `$souldesk-widget`。

宿主若以 Git 子模块接入此 Skill，克隆宿主后，在宿主根目录运行：

```powershell
git submodule update --init -- .agents/skills/souldesk-widget
```

> $souldesk-widget 在组件广场创建一个本地倒计时，生成 ZIP，并在 SoulDesk 的隔离实例里验证导入。

变更工作流后验证两个仓库的入口，再由宿主更新固定的 Skill 提交。
