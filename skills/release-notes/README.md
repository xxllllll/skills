# release-notes

GitHub Release 说明与版本更新公告写作、采集、体检 skill：面向使用者的倒金字塔结构（总览 → 升级提示 → 场景亮点 → 精炼明细 → 安装指引），默认轻量纯中文并支持双语/致谢按需开关，附带零依赖素材采集脚本 `scripts/collect-changes` 与质检脚本 `scripts/check-release`。

## 设计初衷

`commit-message` 负责保证每一条提交准确、原子、面向开发者与机器；`release-notes` 处在下游，负责把一个版本区间内的多次提交提炼成**面向最终用户与部署者的产品公告**：

- `git log` 是开发账本，不是发布说明——裸 SHA、`type(scope):` 前缀、ADR 编号、测试与 CI 调整都不该直接暴露给使用者；
- 信息按「先总后分、先风险后细节」组织，大版本有主题亮点帮助扫读，小补丁直接精简呈现不注水；
- 不安装全局 Git Hook（不同仓库承载 Release Notes 的渠道不同，强制 `pre-push` 易误伤），改为发版前由人或 Agent 调用只读脚本自查，或按需接入具体项目的发布 CI。

## 用法

在支持的客户端中输入 `/release-notes`，或直接要求起草、润色、审校某个版本区间的 GitHub Release 说明。

### 1. 素材采集：`scripts/collect-changes`

```sh
# 自动定位上一个版本 tag 到 HEAD（兼容 v1.2.3、v1.2.3-rc.1、v0.2.161+167）
scripts/collect-changes

# 指定仓库路径或显式提交区间
scripts/collect-changes /path/to/repo
scripts/collect-changes /path/to/repo --from v2.1.0 --to v2.2.0
scripts/collect-changes /path/to/repo --range v0.2.160+166..v0.2.161+167

# 输出结构化 JSON，或带上完整 commit 正文与文件统计
scripts/collect-changes --json
scripts/collect-changes --full
```

脚本只读，自动将区间内非 merge 提交拆成「用户可见候选（破坏性变更 / 新功能 / 修复 / 性能）」与「已过滤的内部提交（保留摘要供人工复核防漏）」，并给出档位建议（精简版 / 标准版）与贡献者数量提示。

### 2. 草稿校验与历史体检：`scripts/check-release`

```sh
# 发版前校验本地草稿文件（或传入 - 从标准输入读取）
scripts/check-release release-notes.md
cat release-notes.md | scripts/check-release -

# 指定双语模式检查中英文分隔与结构对称性
scripts/check-release release-notes.md --bilingual

# 只读体检仓库线上最近 N 条 GitHub Releases（需要已登录 gh CLI）
scripts/check-release --repo . --limit=5
scripts/check-release --repo owner/name --limit=5

# 机器可读输出 / 严格模式（存在警告也返回非零退出码）
scripts/check-release release-notes.md --json
scripts/check-release release-notes.md --strict
```

### `check-release` 检查什么

| 检查项 | 级别 | 说明 |
| --- | --- | --- |
| 空正文或仅剩 Compare 链接 | 错误 | 正文为空，或只包含 `**Full Changelog**: .../compare/...` |
| 残留原始 commit 前缀 | 错误 | 列表行直接以 `feat(scope):`、`fix:` 等提交格式开头 |
| 残留裸 commit SHA | 错误 | 列表末尾残留 `` (`e6d69988`) `` 或 7–40 位裸哈希 |
| 空章节 | 错误 | 出现 `##` / `###` 标题但下面没有任何正文或子节 |
| 残留占位符 | 错误 | 存在 `TODO`、`TBD`、`XXX`、`<版本号>` 等未替换模板占位 |
| 双语结构缺失（`--bilingual`） | 错误 | 启用双语检查时缺少 `---` 分隔线或英文主章节缺失 |
| 缺少一句话总览 | 警告 | 跳过可选一级标题后直接进入 `##` 分组，缺少首段总览 |
| 泄露内部工程术语 | 警告 | 正文出现 `ADR-00xx`、`chore(release)`、`测试夹具`、`门禁` 等内部词 |
| 破坏性变更缺少置顶提示 | 警告 | 出现「破坏性」「不兼容」「迁移」「BREAKING」等词却无 `> ⚠️` 提示块 |
| 长列表缺少亮点分层 | 警告 | 列表项 ≥ 15 条却没有任何 `###` 场景亮点段，扫读成本高 |
| 极小版本疑似复读注水 | 提示 | 列表项 ≤ 3 条却同时写了 ≥ 2 个 `###` 亮点段，建议改用精简版 |

存在「错误」时退出码为 `1`；无错误时退出码为 `0`（传入 `--strict` 时存在「警告」也返回 `1`）。

## 目录结构

```text
SKILL.md                    判定表、两档模板、筛选边界、可选开关与自查入口
README.md                   使用说明、脚本参数与检查项清单
references/rules.md         采集核验、噪音过滤、翻译规则、两档模板与发布防坑全文
references/examples.md      脱敏 bad→good 对照、双语开源范例与常见症状表
scripts/collect-changes     只读变更素材采集脚本（Python 3 标准库，零外部依赖）
scripts/check-release       只读草稿质检与历史体检脚本（Python 3 标准库，零外部依赖）
```
