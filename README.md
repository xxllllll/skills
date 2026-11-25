# skills

个人通用 Agent Skills 仓库，兼容 Antigravity、Claude Code、Codex、Cursor、Pi 等遵循 [Agent Skills](https://agentskills.io/) 标准的客户端，并支持通过 `magpie` 统一分发与自动更新。

## 包含技能

| 技能 | 路径 | 用途 | 附带脚本 |
| --- | --- | --- | --- |
| `commit-message` | [skills/commit-message](skills/commit-message/SKILL.md) | Git 提交信息规范、拆分策略与 trailer 约定 | `commit-msg` hook、`check-log` 历史体检 |
| `adr` | [skills/adr](skills/adr/SKILL.md) | 架构决策记录（ADR）准入、起草、修订互链与大重构拆步 | `check-adr` 只读体检与自动取号 |
| `release-notes` | [skills/release-notes](skills/release-notes/SKILL.md) | GitHub Release 与版本更新公告写作、场景聚类、升级提示与双语约定 | `collect-changes` 素材采集、`check-release` 质检与体检 |

## 挂到 magpie

在 magpie App 的 **Library** 页面添加本仓库（例如 `xxllllll/skills`），按需勾选 `skills/commit-message`、`skills/adr` 与 `skills/release-notes`，随后同步到各客户端：

```sh
magpie library skill agents release-notes agy,antigravity,claude,codex,cursor,pi
magpie library sync
```

若此前按旧的单技能根目录仓库 `xxllllll/commit-message` 安装过 `commit-message`：

1. 在 magpie App 的 **Library** 页面移除旧的 `commit-message`；
2. 重新从本仓库添加 `skills/commit-message` 与 `skills/adr`；
3. 同步后重跑 `~/.config/magpie/library/skills/commit-message/scripts/install.sh` 刷新 `~/.githooks` 软链。

后续可通过 `magpie library skill update` 或开启 `magpie library skill auto-update on` 自动拉取更新。

## 编写约定（踩坑记录）

为确保 `magpie` 与各客户端索引解析稳定，新增技能时遵守两条 frontmatter 约定：

1. `description` 中不使用半角「冒号 + 空格」（`: `），需要时加引号并改用全角冒号或调整措辞，避免 YAML 解析截断。
2. 核心触发词放在 `description` 的第一句且控制在 80 字符以内，避免技能索引只截取首句或 80 字符时丢词。
