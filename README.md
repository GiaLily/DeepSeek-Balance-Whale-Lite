# DSH 小鲸鱼记账挂件 Lite（dsh-whale-widget-lite）

![DSH 小鲸鱼记账挂件](assets/DSH2.png)

> 这是 [MeteorNOX/DeepSeek-Balance-Whale-Widget](https://github.com/MeteorNOX/DeepSeek-Balance-Whale-Widget) 的个人 fork 版，面向 DSH（DeepSeek Harness）桌面端。
>
> **本 fork 的主要目的：在第三方订阅套餐（Command Code、OpenCode Go）里，监控 DeepSeek 最新模型（DeepSeek V4.1 Flash / V4 Pro）的用量与花费**。除 DeepSeek 最新模型外，其它模型不保证完整支持。
>
> 开发方式以 AI 辅助为主：需求由人提出，设计、编码、验证基本由 DSH 中的 AI 完成，人工只做验收。
>
> 上游 v0.4.2 的完整功能说明、截图与变更记录见 **[README.upstream.md](./README.upstream.md)**（fork 基点版本）。

## 相对上游 v0.4.2 的核心区别

| 改动 | 说明 |
|---|---|
| **新增 Command Code（GOAT 套餐）支持** | 上游没有这个厂商。本 fork 新增模板：`/alpha/billing/credits` 取美元余额（`monthlyCredits` + `purchasedCredits` + `freeCredits` 求和）与 5h / 周窗口 `used/cap`；`/alpha/billing/subscriptions` 取套餐档位（GOAT / Go / Pro / Max / Team）。轮末气泡显示本轮美元金额 + 5h 已用 / 剩余 |
| Command Code 按模型计价 | 仅 `deepseek-v4.1-flash` / `deepseek-v4.1-flash-fast` / `deepseek-v4-pro` 在轮末气泡显示金额（按官方谷峰价，美元）；其余 CmdC 模型不显示金额，也不计入面板的官方账单统计 |
| OpenCode Go 计价 | 原版已有该厂商的额度模板；本 fork 增加 `deepseek-v4.1-flash` / `deepseek-v4-pro` 的本轮美元金额（与 CmdC 同价目），其余模型不显示金额 |
| 轮末气泡按厂商裁剪 | 官方 DeepSeek 与 OpenCode Go 不再显示「今日已用」，只显示本轮消耗、输入 / 输出、缓存命中率；CmdC 只显示本轮金额 + 5h 已用 / 剩余 |
| 厂商白名单 | 界面只暴露 DeepSeek 官方、Command Code、OpenCode Go 三家；上游其余模板保留在代码表里但不下发（`LITE_PROVIDERS`，只过滤下发不删表，旧模型配置不会丢） |
| 面板账单口径 | 「近7天实际扣费」只统计官方渠道，按余额差观测（准确优先）单列展示；CmdC / OpenCode Go 的订阅额度不计入 |
| **气泡金额取整（v891）** | 每轮消耗气泡的金额固定 2 位小数；**不足 1 分按 1 分显示**（`$0.001` → `$0.01`），不再出现 4 位小数，也不会把「花了钱」显示成 `0.00` |
| **轮末气泡含子智能体（v893）** | 子智能体跑出来的金额与 token 会**并入**父会话那一轮：气泡里的「本轮消耗 / 输入 / 输出 / 缓存命中」都是主会话 + 所有子智能体之和，并在末尾多一行 `（含子智能体）`；那一轮没有子智能体就完全不显示这行 |

## 安装

### 桌面端（Electron 客户端）

桌面端读的是 `desktop` profile，而 `dsh plugin` 命令行**按设计拒绝**操作它（会报 `profile "desktop" is managed exclusively by the Electron application`）。正确做法是在桌面端的会话里让 agent 用宿主内置的 `plugin_manager` 安装，`target` 填下面任一规格：

| 装法 | `target` | 说明 |
|---|---|---|
| 从 GitHub 装（推荐） | `github:GiaLily/DeepSeek-Balance-Whale-Lite` | 无需本地克隆；装的是 `main` 当前状态，不跟随已发布版本 |
| 从本地源码装 | `link:D:/Coding/dsh-whale-widget-lite` | 开发用；改前端刷新即生效，改宿主需重启 |

装完**重启 DSH 桌面客户端**（改宿主 `lib/index.js` 必须重启才生效；只改 `assets/whale-widget.js` 刷新页面即可）。

### Web 端（`dsh web`）

```powershell
# 从 GitHub 安装
dsh plugin --profile web add github:GiaLily/DeepSeek-Balance-Whale-Lite

# 本地开发用 link 模式（路径换成你自己的克隆位置）
dsh plugin --profile web add "link:D:/path/to/dsh-whale-widget-lite"
```

装完重启 `dsh web`，浏览器按 `Ctrl+F5`。

> 本包**尚未发布到 npm**（`dsh-whale-widget-lite` 在 registry 上目前是 404），所以暂时没有「`dsh plugin add dsh-whale-widget-lite`」这条最短命令。

资源包（zip）/ 拷贝安装等其它方式见 [README.upstream.md](./README.upstream.md) 的「安装」一节，把包名换成 `dsh-whale-widget-lite` 即可。

> ⚠️ 不要与上游 `dsh-whale-widget` 同时启用：两者共用同一份状态文件与 `/dsh-whale/*` 路由，会互相抢。

## 接入 Command Code（第三方 provider 插件）

本插件的 CmdC 用量监控，是在社区维护的第三方 provider 插件 **`@mars-sea/dsh-commandcode-provider`** 下接入的：

- GitHub：<https://github.com/Mars-Sea/dsh-commandcode-provider>
- npm：<https://www.npmjs.com/package/@mars-sea/dsh-commandcode-provider>

桌面端同样用宿主内置的 `plugin_manager` 安装（`target` 填 `@mars-sea/dsh-commandcode-provider@latest`）；Web 端：

```powershell
dsh plugin --profile web add @mars-sea/dsh-commandcode-provider@latest
```

装好后在 **设置 → Models → Command Code** 卡片里填 API key（或网页登录获取），再选模型即可。该插件注册的 provider id 是 `commandcode`，所以会话事件里 `source.provider` 就是 `commandcode`——本插件正是按这个标识识别 CmdC，并通过 DSH 官方凭据读取 `COMMANDCODE_API_KEY`，显示美元余额、5h / 周窗口与本轮花费。

> 如果你在 DSH 里自己新建「自定义提供商」来接入 CmdC，provider 名字不再是 `commandcode`，本插件就识别不到——本轮不会显示美元金额，5h 窗口也不会出现。本插件只识别 `commandcode`、`command-code` 或名字里含 `cmdcode` 的 provider。CmdC 上只有 `deepseek-v4.1-flash` / `deepseek-v4.1-flash-fast` / `deepseek-v4-pro` 会显示金额。

## 支持的 DSH 版本

- 本插件在 **DSH 2.0.19-next（dsh 引擎 `0.2.1-rc.2`）** 上开发并验证。
- Command Code provider 插件（`@mars-sea/dsh-commandcode-provider`）声明支持 `dsh 0.2.0-rc.2 || 0.2.1-rc.2`；更早的引擎不保证。
- 上架插件市场：`package.json` 已声明 `dsh.bundle.patch`；市场做兼容性判定时读 `engines.dsh`，本仓库该字段与上述验证范围保持一致。

## 版本

当前版本：`0.4.2-lite.18`

- 上游基线：`v0.4.2`（commit `5247dd6`）
- 同步上游：仓库保留 `upstream` remote 指向原仓库；上游发版后 rebase 到最新 tag，冲突集中在 `package.json` / `README` / `PROVENANCE.md` / `lib/index.js` / `assets/whale-widget.js` 五处。
- `0.4.2-lite.18`：点击鲸鱼不再「卡在压扁状态」——气泡渲染与取数让出点击帧，回弹动画立即起跑，气泡晚一帧出现（v894）。
- `0.4.2-lite.17`：轮末消耗气泡**并入子智能体开销**（金额与 token 都加总），该轮有子智能体时末尾多一行 `（含子智能体）`（v893）。
- `0.4.2-lite.16`：**撤销**「整体缩放」（v890 的 `uiScale`）—— 该功能多余，菜单回到只有「大小」一个缩放维度（v892）。
- `0.4.2-lite.15`：气泡金额固定两位小数 + 不足 1 分按 1 分显示（v891）。

## 目录结构

与上游一致：

- `lib/index.js` —— 宿主逻辑（余额/额度采集、会话事件记账、HTTP 路由）
- `assets/whale-widget.js` —— 前端挂件（气泡、面板、提醒、音效，无构建步骤）
- `cordis.patch.yml` —— DSH bundle 补丁
- `PROVENANCE.md` —— 与上游的差异记录
- `README.upstream.md` —— 上游原版 README（fork 基点版本）

## 许可

MIT，与上游一致；版权信息见 [`LICENSE`](LICENSE)。

## 致谢

- 上游作者 [MeteorNOX](https://github.com/MeteorNOX) 与 `DeepSeek-Balance-Whale-Widget` 的所有贡献者
- 小鲸鱼形象与音效素材的原作者
