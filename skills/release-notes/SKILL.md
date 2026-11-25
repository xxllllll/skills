---
name: release-notes
description: '撰写、润色、审校 GitHub Release 说明与版本更新公告时使用，涵盖用户视角聚类、升级提示、双语与发版前体检'
---

# Release Notes 发布说明规范

写发布说明先查目标仓库已有约定（`AGENTS.md`、`CHANGELOG.md`、最近 2–3 条 Release），再跑 `scripts/collect-changes` 采集区间改动。**绝不凭 commit 标题脑补**——含义模糊时必须核对 commit 正文或 `git diff --stat`。

## 选档位

根据**用户可见变更**的数量与场景跨度选结构，不为了凑格式硬写亮点：

| 档位 | 适用条件 | 必备结构 |
| --- | --- | --- |
| 精简版 | 用户可见变更 ≤ 4 条且主题单一（补丁/小版本） | 一句话总览 → `> ⚠️ 升级提示`（按需）→ 精炼明细 → 安装与升级（按需） |
| 标准版 | 用户可见变更 ≥ 5 条，或跨 ≥ 2 个用户场景 | 一句话总览 → `> ⚠️ 升级提示`（按需）→ 2–6 个 `### <emoji> 场景亮点` → 精炼明细 → 安装与升级（按需） |

## 筛选与翻译

| 进正文（面向使用者/部署者） | 默认过滤（留给 git log） |
| --- | --- |
| 新功能、交互与界面可感知改进 | `chore`、`ci`、`test`、`docs`、`style`、`refactor`（无行为变化） |
| Bug 修复、兼容性修复、安全漏洞修复 | 内部工程 `scope`：`release`、`scripts`、`tests`、`adr`、`agents`、`skills` |
| 明显性能/内存/启动耗时改善 | 常规依赖版本推进、测试夹具清理、门禁与工作流调整 |
| 破坏性变更、配置/数据迁移、强制更新门槛 | 版本号 commit、合并记录、记账/归档提交 |

- 若安全修复或底层重构带来明确的用户可感知收益或升级动作，人工从过滤列表提回正文。
- **翻译成人话**：写成 `- 场景/模块：用户能感知的变化`；删除裸 commit SHA、`type(scope):` 前缀、内部表名/类名和 `ADR-NNNN` 编号。
- **场景亮点只按用户场景聚**（可把同场景的 `feat` + `fix` + `perf` 揉成 2–3 句连贯短段），不机械照抄目录名或 commit type。
- 零碎小修过多时，在修复清单末尾收束为 `- 另有 N 项稳定性与细节修复`。

## 升级提示与可选开关

- **破坏性变更 / 升级避坑**：有不兼容改动、数据/配置迁移、最低版本水位或升级操作要求时，在首段总览后**必须**紧跟 `> ⚠️ **升级提示**：...`，写清影响谁、怎么处理。
- **语言**：默认**纯中文**；对外开源且面向国际用户的仓库开启中英双语（完整中文在前 + `---` + 完整英文在后，共用文末安装与致谢）。
- **署名与致谢**：个人或私有仓库默认**省略**；多人协作开源仓库在亮点段末轻署 `— @user1 · @user2`，文末附 `## 🙏 致谢 / Thanks`。
- **安装与产物**：客户端、CLI、插件或容器项目在文末附 `## 安装与升级`（常用命令、平台避坑如 macOS quarantine、文档或校验和入口）。

## 发布落地防坑

- 优先写临时文件再发布：`gh release create <tag> --notes-file notes.md`（或 `gh release edit`），不要靠 `--generate-notes` 裸出一条 Compare 链接。
- 若仓库由 annotated tag 承载 Release Notes（`git tag -a`），**必须**带 `--cleanup=verbatim` 或 `-F`，否则 Git 默认会把 `#` / `##` 标题当注释剥掉。

## 自查

```sh
scripts/collect-changes                     # 采集上一版 tag 到 HEAD 的变更素材
scripts/check-release release-notes.md      # 发版前体检草稿（只报不改）
scripts/check-release --repo . --limit=5    # 只读体检仓库历史 GitHub Releases
```

细则全文见 `references/rules.md`，脱敏 bad→good 对照见 `references/examples.md`。
