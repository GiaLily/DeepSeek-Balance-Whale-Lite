# 素材来源与许可范围（PROVENANCE）

本仓库的**代码**按 MIT 许可（见 [`LICENSE`](LICENSE)）。`assets/` 目录下的**美术素材**（图片 / 动图 / 音效）**不在 MIT 覆盖范围内**，按下面的说明「原样提供」。

## 一、许可范围

| 范围 | 许可 |
|---|---|
| `lib/`、`cordis.patch.yml`、`package.json`、文档与维护脚本 | **MIT**（见 `LICENSE`） |
| `icon.png`（插件列表图标） | **不适用 MIT**：与 `assets/` 下的美术素材同属"原样提供"，仅用于标识本插件；不授予再许可，也不声明为原创作品。 |
| `assets/**`（图片 / 动图 / 音效） | **不适用 MIT**：由维护者提供或使用 AI 工具生成，按 **as-is** 随插件分发，仅用于运行本插件；不授予再许可，也不声明为原创作品。 |

这样划分的原因：代码可以明确授权，而美术素材的来源与权利状态往往无法百分之百举证 —— 与其给一个站不住的授权，不如如实标注范围，并承诺收到权利主张就处理（见第四节）。

## 二、逐项来源

| 文件 | 来源 / 说明 |
|---|---|
| `DSniang1.png`（鲸鱼本体，气泡由代码绘制） | **AI 工具生成**的图像，经人工挑选与裁切。生成工具与原始出处**已不可考**（文件内元数据已被剥离，见第三节）。 |
| `icon.png`（插件列表图标，256×256 PNG 含透明） | 由现有素材裁切 / 缩放而来（源头同 `DSniang1.png` 系列，AI 工具生成、出处不可考）。已剥离全部内嵌元数据，块序列仅 `IHDR / IDAT / IEND`。 |
| `DSniang02.png` | 同上（备用整图，兼容旧版手动安装路径）。 |
| `DSH2.png`（README 顶部展示图） | 由维护者自行设计与排版（使用在线设计工具），2026-09 替换为清理版（已剥离全部内嵌元数据）。 |
| `rua.gif`、`bubble-petpet.gif` | 内置动图素材。 |
| `bubble-money1.gif` | 内置泡泡图（余额预警默认内容的配图）。 |
| `Ya1.mp3` / `Ya2.mp3`、`D1.mp3` / `D2.mp3` | 内置音效（按压 / 松开）。 |
| `minecraft-exp-orb.wav`、`task-end-a.wav` | 内置任务结束音（Minecraft·经验球 / 预设 A）。 |

## 三、元数据清理

图片素材曾内嵌过设计工具的 XMP 归属信息（工具名、模板名、账号 id，甚至第三方品牌名）。自 **0.3.1** 起，随包 PNG **一律剥离** `eXIf` / `iTXt` / `tEXt` / `zTXt` 元数据块，只保留渲染必需的 `IHDR` / `sRGB` / `gAMA` / `pHYs` / `IDAT` / `IEND`。

- 复核：解析每个 PNG 的块序列，确认不再有文本 / EXIF 块；
- 剥离：逐块重写，不触碰像素数据（IHDR/IDAT/IEND 原样保留），因此画面完全不变。

## 四、权利主张 / Takedown

如果你认为 `assets/` 中有素材侵犯了你的权利，请在本仓库开一条 issue，说明**文件名**与**依据**，我们会在核实后**立即替换或移除**，不附加其它条件。我们也欢迎直接提供可自由再分发的替代素材。

## 五、本 fork 的改动

本仓库是 [MeteorNOX/DeepSeek-Balance-Whale-Widget](https://github.com/MeteorNOX/DeepSeek-Balance-Whale-Widget)（MIT）的 fork，基于上游 **0.4.2**。上游版权与免责声明完整保留（见 `LICENSE`）；`assets/` 与 `icon.png` 的授权范围与上游**完全一致**（沿用原素材，未新增美术资源，也未改变原有许可说明）。

代码层面的改动：

| 文件 | 改动 |
|---|---|
| `lib/index.js` | 新增 `commandcode` 厂商模板（`kind:'quota'`，`COMMANDCODE_API_KEY`）；`templateOrigins()` 纳入 `quota.extra.url`；`fetchModelBalance()` 支持 `json.sum` 多字段求和；`fetchModelQuota()` 支持窗口 `used` + `cap` 换算百分比、`resetAtMs` 时间戳、`quota.extra` 二次请求取套餐档位；新增 `humanizePlanLabel()` 档位名兜底 |
| `assets/whale-widget.js` | 窗口下拉由静态常量改为按模板 `quota.json.windows` 动态生成（`bubblePlanWinDefs` / `bubblePlanWinOpts` / `bubblePlanWinOf` / `bubblePlanWinLabel`）；既有余额又有额度的厂商额外提供「余额」模块（依据宿主下发的 `hasBalanceApi`）；新增 `apiPlanMoneyOf()` 与 `{plan_money}` / `{plan_spent}` / `{plan_cap}` 变量 |
| `package.json` | 包名 `dsh-whale-widget-pro`、版本 `0.1.0`、描述 / 关键词 / 仓库地址按本 fork 更新 |
| `cordis.patch.yml` | 插件 id 与 name 改为 `dsh-whale-widget-pro` |
| `README.md` | 按 fork 身份重写：新增 Command Code / OpenCode Go 接入说明与窗口金额变量，删除与本仓库无关的上游分支内容 |

兼容性说明：`~/.dsh/.dshw-*.json` 数据文件、`dshwv-*` DOM class、`/dsh-whale/*` 路由**沿用上游命名**，因此角色、音效、账本数据可无缝迁移；代价是本插件**不能与上游 `dsh-whale-widget` 同时启用**（同名路由与同一份状态会互相抢）。

OpenCode Go 的接口按官方公开结构实现，开发时无该套餐 API key，**未做真机联调**；Command Code 的两个接口在开发机实测通过。
