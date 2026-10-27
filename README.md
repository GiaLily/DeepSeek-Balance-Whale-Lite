# DSH 小鲸鱼记账挂件 Lite（dsh-whale-widget-lite）

![DSH 小鲸鱼记账挂件](assets/DSH2.png)

> 这是 [MeteorNOX/DeepSeek-Balance-Whale-Widget](https://github.com/MeteorNOX/DeepSeek-Balance-Whale-Widget) 的个人 fork 版，面向 DSH（DeepSeek Harness）桌面端。
> 上游完整的功能说明、截图、安装方式与变更记录见 **[README.upstream.md](./README.upstream.md)**（fork 基点 v0.4.2 的原版 README）。

## 这个 fork 改了什么

| 改动 | 说明 |
|---|---|
| 只保留三家厂商 | 下拉里只暴露 DeepSeek 官方、Command Code、OpenCode Go；上游其余 30+ 家模板仍在代码表里，但不下发、不显示（`LITE_PROVIDERS` 白名单，只过滤下发不删表，旧模型配置不会丢） |
| 轮末气泡按厂商显示 | Command Code 只显示本轮金额 + 5h 已用/剩余；DeepSeek 官方与本 fork 的 OpenCode Go 只显示本轮信息，不再显示「今日已用」 |
| CmdC 按美元计价 | 仅 `deepseek-v4.1-flash` / `deepseek-v4.1-flash-fast` / `deepseek-v4-pro` 在轮末气泡显示美元金额（谷峰价）；其余 CmdC 模型不显示金额，但仍进入账本 |
| OpenCode Go 同口径 | 仅 `deepseek-v4.1-flash` / `deepseek-v4-pro` 显示美元金额；其余模型（Kimi / GLM / Qwen / MiniMax …）不显示 |
| 模型名友好化 | 气泡与面板显示成 `CmdC：DeepSeek V4.1 Flash` 这类可读名称，代替原始 `provider/model` 串 |
| 面板费用块 | 整合为「DeepSeek V4 近7日费用」，只统计官方渠道、按余额差口径（准确优先）单列展示 |
| 历史记录修复 | 修复「小鲸鱼记账」历史记录永久卡「加载中…」的问题（路由异常 + 前端占位兜底） |

改动细节以 [`PROVENANCE.md`](PROVENANCE.md) 为准（每版一段，含根因与验证方式）。

## 安装

桌面端（Electron 客户端，推荐）：

```powershell
# 从本地源码安装（link 模式，改前端代码刷新即生效）
dsh plugin --profile desktop add "link:D:/Coding/dsh-whale-widget-lite"
```

或使用 `plugin_manager` 的 `install_bundle`，target 填 `link:D:/Coding/dsh-whale-widget-lite`。

装完**重启 DSH 桌面客户端**（改宿主 `lib/index.js` 必须重启才生效；只改 `assets/whale-widget.js` 刷新页面即可）。

其它安装方式（资源包 / GitHub / npm）见 [README.upstream.md](./README.upstream.md) 的「安装」一节，把包名换成 `dsh-whale-widget-lite` 即可。

> ⚠️ 不要与上游 `dsh-whale-widget` 同时启用：两者共用同一份状态文件与 `/dsh-whale/*` 路由，会互相抢。

## 版本

当前版本：`0.4.2-lite.11`

- 上游基线：`v0.4.2`（commit `5247dd6`）
- 同步上游：仓库保留 `upstream` remote 指向原仓库；上游发版后 rebase 到最新 tag，冲突集中在 `package.json` / `README` / `PROVENANCE.md` / `lib/index.js` / `assets/whale-widget.js` 五处。

## 目录结构

与上游一致：

- `lib/index.js` —— 宿主逻辑（余额/额度采集、会话事件记账、HTTP 路由）
- `assets/whale-widget.js` —— 前端挂件（气泡、面板、提醒、音效，无构建步骤）
- `cordis.patch.yml` —— DSH bundle 补丁
- `PROVENANCE.md` —— 与上游的差异记录（本 fork 的改动台账）
- `README.upstream.md` —— 上游原版 README（fork 基点版本）

## 许可

MIT，与上游一致；版权信息见 [`LICENSE`](LICENSE)。

## 致谢

- 上游作者 [MeteorNOX](https://github.com/MeteorNOX) 与 `DeepSeek-Balance-Whale-Widget` 的所有贡献者
- 小鲸鱼形象与音效素材的原作者
