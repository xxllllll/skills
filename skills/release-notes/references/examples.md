# Release Notes 真实场景对照（脱敏示例）

以下示例提炼自真实仓库发布实践，项目与私有业务名已泛化处理，重点展示「不同体量如何选档位」「如何过滤工程噪音」「如何把平铺列表收成倒金字塔」以及「公开双语版标准样式」。

---

## 1. 仅剩一行 Compare 链接 → 精简版用户公告（多端小步热修）

### 背景提交（区间共 4 条）

```text
chore(release): v0.2.161+167
docs(upstream): 记录 2026-11-25 上游巡检结论
fix(playrepair): 收窄热修熔断条件，增加已验证回滚
feat(playresolver): 强化播放取流协议容错能力
```

### 坏例（裸 `--generate-notes`）

```markdown
**Full Changelog**: https://github.com/owner/app/compare/v0.2.160+166...v0.2.161+167
```

问题：使用者点开 Release 页完全不知道为什么要升级；跨端下载几十 MB 安装包前无从判断与自己是否相关。

### 好例（精简版，过滤巡检文档与版本提交）

```markdown
本次更新增强了播放取流协议的容错能力，并收窄规则热修的熔断条件，异常时可自动回滚到已验证版本。

## 🚀 新功能
- 播放解析：强化取流协议容错，应对上游响应字段变动更稳健

## 🐛 修复
- 规则热修：收窄熔断触发条件，并在热修失效时自动回滚至最近可用版本

## 安装与升级
- 请根据设备从下方 Assets 下载对应平台的 `.apk`、`.ipa`、`.dmg` 或 `.zip` 安装包，校验值见 `SHA256SUMS.txt`。
```

判据：用户可见变更只有 2 条且同属播放链路，直接用**精简版**，不硬凑 `###` 亮点段落。

---

## 2. 机械 `git log` 转储 + `git tag` 吃掉标题 → 运维视角精简公告

### 坏例（`cut.py` 拼 log + 未传 `--cleanup=verbatim`）

```markdown
- 新增宿主机 Compose 受控升级脚本 (`e6d69988`)

- 锚定 v2.2.0 合流基线排除已发布历史提交 (`f62e9127`)

- 清理孤儿组件、死请求封装、豁免配置 (`fccbef1a`)
- 收口手动整理模型，清退废表、死列、冗余依赖 (`eb35d13c`)

- chore(main): 清理生产主线 (`4842fccc`)
- docs(adr): 记录 main 单主干发版决策（ADR-0013） (`c5a34c5d`)
- ci(release): 收敛 main 单主干工作流至标签驱动发布 (`8d4bfd01`)
- test(console): 清理测试夹具残留的 0.5.0 静态版本号 (`8917dd78`)
- ci(validate): 清理失效诊断脚本，修复门禁路径分类器 (`8f991e63`)
- docs(adr): 记录历史包袱清退决策，校准模块合同（ADR-0014） (`eb0c724c`)

基准版本：`v2.2.0`
```

问题：
1. `git tag -a -m` 默认剥掉了所有 `#` / `##` 标题行，只剩零散无标题列表；
2. 10 条里 8 条是 `ci`/`test`/`docs(adr)`/`chore` 和纯内部清理，真正与部署者有关的只有 1 条新脚本和 1 条数据库/模型收敛；
3. 带着裸 SHA、`chore(main):`、`ADR-0014` 等开发账本信息。

### 好例（滤除 8 条内部噪音，核对 diff 后翻译给部署者）

```markdown
本次更新新增宿主机 Docker Compose 受控升级脚本，并清理了早期版本遗留的废弃数据表与冗余配置。

> ⚠️ **升级提示**：本次移除了已停用的旧整理模型相关数据表与字段；如自定义外部脚本曾直接读取数据库旧表，请先检查兼容性再升级。

## 🚀 新功能
- 运维升级：新增宿主机 Docker Compose 受控升级脚本，支持按步骤校验镜像并平滑更新

## ⚡ 性能与优化
- 存储与前端：清理不再使用的旧数据表、冗余依赖与废弃界面组件，降低镜像与运行时维护负担

## 安装与升级
- 容器部署请拉取新版本镜像并重启服务，或使用仓库自带的 Compose 受控升级脚本更新。
```

---

## 3. 40+ 条平铺列表缺层次 → 标准版倒金字塔（总览 + 提示 + 亮点 + 明细）

### 坏例（有用户语言意识，但几十条无分层平铺）

```markdown
### 破坏性变化
- **配置**：正式运行只有 Linux 容器，托盘、安装器与 Windows 原生路径全部删除（ADR-0121）。
- **配置**：账本路径就是容器路径，旧 `[media.locations]` 拒绝启动。

### 新增
- **界面**：分区一独立，首页只讲一件事。
- **界面**：详情页磁链与评论共用列表行。
- **界面**：发现页换成网页列表带四个榜期。
- **界面**：手机端详情页把标题提到画面之前，可左右滑动。
- **播放**：外部播放一键直达 MPV/PotPlayer，直链过期自动重签。
- **播放**：悬停预览改放官方预告片并支持调速。
- **馆藏**：缓存封面徽章编译结果，冷启动 7.9 秒降到约 3 秒。
（……此处省略 35 条平铺条目……）
```

问题：单条措辞已经很贴近用户，但打开页面就是一堵 40 行的文字墙，读者无法在 10 秒内抓住这版最核心的 3–4 个变化，破坏性迁移也不够醒目；且夹带了内部编号 `ADR-0121`。

### 好例（标准版漏斗结构）

```markdown
本次更新正式收敛为纯 Linux 容器部署并简化媒体路径配置，全面重构了导航、详情与移动端布局，带来外部播放器直连与官方预告片预览，并将冷启动耗时从 7.9 秒降至约 3 秒。

> ⚠️ **升级提示**：从本版起仅支持 Linux 容器运行，不再提供桌面托盘、安装器及 Windows 原生运行路径；旧配置 `[media.locations]` / `[media.mounts]` 将拒绝启动，升级前请先执行 `python scripts/migrate_posix_paths.py` 迁移为容器路径。

### 🖥️ 界面重构与移动端体验升级
首页回归纯馆藏视图、发现页独立成区并支持多榜期与滚动加载；详情页统一了磁链与评论行并支持一键推送下载，手机端将核心信息前置且支持画面左右滑动。

### ▶️ 播放与预览体验提升
外部播放支持一键直达 MPV / PotPlayer，直链过期后自动重签无须手动刷新；悬停预览改为播放可调速的官方预告片，未入库作品也能直接预览。

### ⚡ 冷启动加速与首屏瘦身
通过封面徽章编译缓存、按页分片加载与小尺寸头像/封面派生图，冷启动从 7.9 秒降至约 3 秒，首页首屏体积大幅压缩，首帧排版偏移（CLS）从 0.168 降至 0.015。

### 🔍 资料采集与账本稳定性
登录接口接管列表与分类抓取，新增第二资料来源兜底与按字段溯源，并分离单站冷却与额度账本；资源对账改为标记「已消失」而非直接删行，避免误删历史记录。

## 🚀 新功能
- 界面与导航：首页聚焦馆藏、发现页支持排序/评分/四榜期；详情页支持一键推送磁链，长列表新增回到顶部
- 移动端：作品详情标题与关键信息前置，画廊支持左右滑动切换
- 播放：支持一键调用 MPV / PotPlayer 播放并自动重签过期直链；悬停支持可调速官方预告片预览
- 关注与筛选：关注页新增「仅看未入库」与「按口味排序」两档筛选
- 采集与复核：接入第二来源补齐空字段并记录字段来源，支持中英文原文同存、按复核名单合并同实体与任务流量预估
- 账本与配置：对账改为软标记「已消失」；支持自定义对外 User-Agent，翻译默认关闭

## ⚡ 性能与优化
- 冷启动：缓存封面徽章编译结果，冷启动由 7.9 秒降至约 3 秒
- 首屏加载：按页面分片加载、站标与头像改发 128px 派生图，入口 gzip 降至 783 KB，CLS 从 0.168 降至 0.015
- 封面索引：仅按目录版本校验，移除 90 秒定时全量重扫

## 🐛 修复
- 界面与路由：修复带点号编号路由拦截、跨页返回丢失列表滚动位置，以及骨架屏无障碍与对比度问题
- 播放与图片：单张封面连接抖动不再中断整批加载，适配新的预告片主机
- 采集与重试：按假名占比识别日文避免误翻中文简介，被拒请求改用指数退避并细分签名失效判据
- 另有 6 项账本连接、缓存与界面细节修复

## 安装与升级
- 通过 Linux 容器运行并使用浏览器访问，部署与升级步骤见 [容器部署文档](docs/OPERATIONS.md#容器部署)。
```

---

## 4. 小补丁硬套标准版（复读机）→ 收缩为精简版

### 坏例

```markdown
本次更新修复了设置页保存卡死的问题。

### 🛠️ 修复设置页保存卡死
修复了点击保存按钮后请求一直转圈无法完成的问题。

## 🐛 修复
- 设置页：修复点击保存按钮后请求一直转圈无法完成的问题
```

问题：总共只有 1 条修复，却写了三遍几乎一模一样的句子。

### 好例

```markdown
本次更新修复了修改设置后点击保存一直转圈卡死的问题，替换插件文件并重启即可生效。

## 🐛 修复
- 设置保存：跳过导致配置锁阻塞的后台事件处理，切换配置现可秒级完成落盘

## 安装与升级
- 替换 `Plugin.dll` 后重启服务即可，原有授权与配置保持不变。
```

---

## 5. 公开开源多贡献者双语版（开启双语 + 致谢开关）

当项目面向国际开源社区且有多位贡献者时，开启双语与致谢开关：

```markdown
本次更新修复了桌面客户端权限模式入口消失的问题，新增侧栏主机色点筛选与排队消息附件编辑，并显著降低后台内存占用。

> ⚠️ **升级提示**：若通过国内 npm 镜像自动更新，请等待镜像同步（通常约 1 小时）后再重试；若已回退旧版，可执行 `tool stop` 后重装。从本版起自动更新会先确认源已同步。

### 🌐 侧栏多主机筛选与排队编辑
Web 侧栏顶部新增色点主机筛选栏，远程会话改用左侧细色条标识，列表更清爽；支持直接编辑含图片与文件附件的排队消息。
— @alice · @bob

### ⚡ 响应流畅度与内存优化
优化输入重绘与壁纸模糊渲染范围，用量统计改为按需读取并空闲释放，显著降低后台常驻内存。
— @alice

## 🚀 新功能
- Web 侧栏：新增顶部色点主机筛选栏，远程会话仅在选中行轻显主机名 (#1108)
- 排队消息：支持直接编辑包含图片与文件附件的排队消息 (#1112)

## 🐛 修复
- 桌面端适配：修复新版宿主下外部权限模式切换按钮消失的问题 (#1120)
- 内存占用：按需读取并空闲释放用量统计，减少后台常驻内存 (#1109, #1110)

---

This release restores missing permission mode controls on newer Desktop builds, adds a color-dot host filter bar and queued-message attachment editing, and cuts background memory usage.

> ⚠️ **Upgrade Note**: If you update via an npm mirror, wait for the mirror to sync (~1 hour) before retrying. Starting from this release, auto-updates verify registry sync before prompting.

### 🌐 Host Filter Bar & Queue Editing
Adds a top color-dot host filter bar in the Web sidebar with subtle left rails for remote sessions, and lets you edit queued messages that include image or file attachments.
— @alice · @bob

### ⚡ Responsiveness & Lower Memory Footprint
Eliminates typing re-render fan-out, scopes wallpaper blur near the viewport, and loads usage statistics on demand with idle release.
— @alice

## 🚀 Features
- Web sidebar: top color-dot host filter bar with active-row-only remote host labels (#1108)
- Queued messages: edit queued messages containing image and file attachments (#1112)

## 🐛 Fixes
- Desktop compatibility: restored external permission mode controls on newer Desktop builds (#1120)
- Memory usage: loaded usage stats on demand with idle release (#1109, #1110)

## Install & Upgrade
- Install: `npm install -g @example/cli`, or download an installer from Assets below.
- Upgrade: `tool update` (macOS quarantine fix: `xattr -dr com.apple.quarantine /Applications/Tool.app`).

## 🙏 致谢 / Thanks
@alice (#1108, #1109, #1110, #1120) · @bob (#1112)
```

---

## 常见症状对照表

| 症状 | 规则 |
| --- | --- |
| 正文只有 `**Full Changelog**: .../compare/...` | 禁止裸用 `--generate-notes`，先采集提交再按档位撰写 |
| `git tag -a` 发布的 Release 所有 `#` / `##` 标题蒸发 | 创建 tag 时必须传 `--cleanup=verbatim` 或改用 `gh release --notes-file` |
| 列表里全是 `chore(main):`、`docs(adr):`、`ci:`、`test:` | 按 `type` + 内部 `scope` 双重过滤，只留用户/部署者关心的变化 |
| 每条末尾挂着 `` (`e6d69988`) `` 或夹带 `ADR-0014` | 剔除裸 SHA 与内部文档编号，翻译成用户语言 |
| 40 条清单一口气平铺，没有重点 | ≥ 5 条 / 跨多场景时启用标准版：总览 + 2–6 个 `###` 亮点 + 折叠长尾 |
| 只有 1–2 条修复却写了一堆重复亮点 | ≤ 4 条且单主题时用精简版，禁止复读机 |
| 删了旧配置或改了部署方式却藏在列表中间 | 破坏性变更与迁移步骤必须置顶为 `> ⚠️ **升级提示**` |
