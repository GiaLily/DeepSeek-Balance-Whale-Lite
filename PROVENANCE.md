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
| `lib/index.js` | 新增 `commandcode` 厂商模板（`kind:'quota'`，`COMMANDCODE_API_KEY`）；`templateOrigins()` 纳入 `quota.extra.url`；`fetchModelBalance()` 支持 `json.sum` 多字段求和；`fetchModelQuota()` 支持窗口 `used` + `cap` 换算百分比、`resetAtMs` 时间戳、`quota.extra` 二次请求取套餐档位；新增 `humanizePlanLabel()` 档位名兜底；**新增「当前模型」跟踪**：`handleSessionEvent()` 记录 `provider/model`，`apiMatchModelName()` 复用 `matchIds` 归属到模型条目，`currentModelInfo()` 在重启后用账本最后一条 `events.model` 恢复，`apiModelsPayload()` 增返 `current`；**同名模型的 provider 消歧**：`handleSessionEvent()` 取 `message.source.provider` 存入 `lastSrcProvider`、`appendUsageEvent()` 落盘 `provider` 字段、`apiMatchModelName(modelName, providerHint)` **先按 provider 精确命中自定义模型**再退回名字匹配（否则 Command Code 转发的 `deepseek/deepseek-v4.1-flash` 会被当成官方 DeepSeek）；**新增 `readProfileDefaultModel()`**：内存与账本都没有记录时，读 profile 补丁 `agent-default-model` 的 `provider`/`model` 作为最后一级兜底（5 秒缓存，不引 YAML 依赖） |（v806 补充：Command Code 窗口标签 `5h` → `5小时`，`quota.extra.json.periodEnd` 增加 7 条候选路径）
| `assets/whale-widget.js` | 窗口下拉由静态常量改为按模板 `quota.json.windows` 动态生成（`bubblePlanWinDefs` / `bubblePlanWinOpts` / `bubblePlanWinOf` / `bubblePlanWinLabel`）；既有余额又有额度的厂商额外提供「余额」模块（依据宿主下发的 `hasBalanceApi`）；新增 `apiPlanMoneyOf()` 与 `{plan_money}` / `{plan_spent}` / `{plan_cap}` 变量；**新增「当前模型」气泡**：`apiCurrentModelId()` / `apiCurrentModelName()` / `apiCurrentKnown()`，`bubbleContentTokenMap()` 全局注入 `{model}` `{model_balance}` `{model_today}` `{model_plan*}` `{model_level}`，出厂气泡第一项改为当前模型卡；`bubbleContentText()` 允许 `type:"text"` 模块用 `text` 字段当模板（文本模块也支持 `{占位符}`，取数失败回退原文）；**气泡标题行缩短**：新增 `apiModelShortName()` / `apiLevelText()` 与 `{model_short}`（`Command Code（订阅） · GOAT` → `CC Goat`），出厂卡标题拆成两行（`当前提供商` / `{model_short}`），避免完整名超出气泡宽度；`apiPlanSummary()` 增加 `dropLevel` 参数并新增 `{model_plan_win}`（额度行不带档位，一行放得下）；**气泡外框放大到 1.45 倍**（`.dshwv-pop` 宽度 `calc(100% * var(--dshw-pop-grow,1.45))`、`right:0` 向左扩展，`--dshw-u` 不变 ⇒ 只加容量不放大字号）；气泡内行距 `line-height:1.05`、文本块垂直中心 `--dshw-vy` 默认 36% → 32%（椭圆下缘收窄快，多行卡必须三项一起调才不顶描边）；出厂卡字号下调（`$` size 20→16、今日 4→3、两个额度行与峰块 2→1）；**额度行按窗口拆行**：新增 `apiPlanWinLine()` 与 `{model_plan_win1}` / `{model_plan_win2}`（一行一个窗口，单窗口厂商第 1 个占位符回退整段摘要、第 2 个为空），出厂卡额度行由 1 个模块拆成 2 个；**气泡行数上限 6 → 7**（`ROW_MAX` 于渲染层、`BUBBLE_PV_ROW_MAX` 于编辑器侧），否则第 7 行会被静默丢弃；**v806 排版调整**：出厂卡标题合并为一行 `当前提供商：{model_short}`（size 6）；额度窗口标签改中文（`5小时`，模板 `quota.json.windows[].label`）；行距 `.dshwv-trow` 1u → **2.5u**，多模块行 `.dshwv-trowline` 额外 `margin-top` 5u；新增 `apiPlanPeriodText()` 与 `{model_plan_period}`（套餐整体剩余，宿主侧在 `quota.extra.json.periodEnd` 按候选字段探测并归一成毫秒，下发 `plan.periodEnd`；取不到则前端返回空串、整行自动隐藏）；**模型行同显余额与额度**（`hasBalanceApi && balance != null` 时不再被额度分支覆盖） |

**v807 补充**：新增模块字段 `nowrap`（`blockOf` 写内联 `white-space:nowrap;max-width:none`，`maybeWrap` 直接跳过折行判定）与 `mt`（模块级 `margin-top`，多模块行作用在行容器上）；行距 2.5u、出厂卡块间距 18u；`ROW_MAX` / `BUBBLE_PV_ROW_MAX` 7 → **8**；CSS 兜底中心 `--dshw-vy` 32% → 34%。**失败回退**：宿主新增 `QUOTA_SNAP_FILE`（`~/.dsh/.dshw-quota-snap.json`）、`readQuotaSnap()` / `quotaSnapOf()` / `quotaSnapSet()`（合并写、最多保留 60 个模型），余额与额度成功时各存一份快照，失败时回退并打 `stale:true`（`plan.stale` / `entry.stale`）；`apiPlanNoPlan()` 命中时仍优先 `hide`，不回退旧值；前端新增 `apiPlanStaleText()` 与 `{model_plan_stale}`。

**v808**：气泡外框回到 1.0 倍 —— `.dshwv-pop` 宽度默认值 `--dshw-pop-grow` 由 1.45 改为 **1**，同时 `--dshw-u` 由 `calc(var(--dshw-base) / 1026)` 改为 `calc(var(--dshw-base) / 1026 / 1.45)`；外框与字号**同比例**回缩 ⇒ 相对布局（行数 / 顺序 / 间距 / 字号占比）完全不变，气泡绝对尺寸回到放大前。调 `--dshw-u` 末尾那个 `1.45` 即可整体缩放（改成 1.2 就是只缩一半）。

**v810（提醒常驻）**：自动弹出的提醒不再定时收起 —— `usageRemindTtlMs()` 未填 `ttlSec` 时返回 **0**（原 6500ms），`USAGE_ALERT_TTL` 常量 6500 → **0**，客户端 `var turnCostCloseMs` 5000 → **0**；宿主 `readSizeConfig()` / `writeSizeConfig()` / `.dshw-size.json` 合并兜底三处默认值同步 5000 → **0**。`sceneOpen()` 只在 `ttlMs > 0` 时布 `bubbleAutoClose` 计时器，因此 0 天然常驻；`bubbleTtlArmed(0)` / `bubbleTtlClear()` 与巡检逻辑沿用原「常驻」路径（与等待泡泡一致），不会误收。收起时机：用户点击鲸鱼，或新提醒经 `whaleSysSwapNext()` 覆盖旧提醒。显式配置（`autoClose:false`、`ttlSec>0`、面板秒数）仍按原样生效。

**v809**：字号回补 1/10 —— `--dshw-u` 的除数由 `1.45` 改为 **`1.32`**（1.45 / 1.1），外框仍是 1.0 倍；行数、顺序、间距比例不变，8 行仍放得下。用户若还要更大的字，继续调小该除数即可。| `package.json` | 包名 `dsh-whale-widget-pro`、版本 `0.3.0`、描述 / 关键词 / 仓库地址按本 fork 更新 |
| `cordis.patch.yml` | 插件 id 与 name 改为 `dsh-whale-widget-pro` |
| `README.md` | 按 fork 身份重写：新增 Command Code / OpenCode Go 接入说明与窗口金额变量，删除与本仓库无关的上游分支内容 |

兼容性说明：`~/.dsh/.dshw-*.json` 数据文件、`dshwv-*` DOM class、`/dsh-whale/*` 路由**沿用上游命名**，因此角色、音效、账本数据可无缝迁移；代价是本插件**不能与上游 `dsh-whale-widget` 同时启用**（同名路由与同一份状态会互相抢）。

OpenCode Go 的接口按官方公开结构实现，开发时无该套餐 API key，**未做真机联调**；Command Code 的两个接口在开发机实测通过。
**v811**：提醒不再堆积 —— whaleSysQueueInsert() 增加同类去重（cost / alert 各自只保留最新一条，wait 不受影响）；新增 whaleSysClearSeen()（清掉 cost / alert 的当前项与排队项、保留 wait），whaleClick() 与 bubbleNext() 在 cost / alert 场景下先调它再关闭 —— 原来点一下只 `whaleSysSwapNext()` 顶一条，用户要连点好几次才能清空。
**v812**：界面数字自动刷新 —— 以前 `apiModelsFetch()` 只在页面加载时跑一次，面板与气泡上的余额 / 今日已用 / 额度全是那一刻的快照（用户反馈"今日已用一直是 0"即此因）。现在：`sceneOpen()` 把渲染函数一起存进 `bubbleScene.render`；新增 `bubbleRepaintIfShown()`（数据刷新后原位重绘当前气泡）与 `apiModelsScheduleRefresh()`（每 5 分钟续期刷新）；`showUsageSub()` 与 `whaleClick()` 打开前各强刷一次 `loadApiModels(null, true)`；`apiFmtMoney()` 对 `null` 返回 `--`（原来 `Number(null)=0` 会显示 `$ 0.00`，把"没数据"伪装成"没花钱"）。
**v813**：每轮消耗气泡的「今日已用」改用本机口径 —— 默认消耗气泡（`assets/whale-widget.js` 的 `showCostBubble` 默认模板）里 `{expense_ds}` 换成新占位符 `{expense_local}`。`{expense_ds}` 取的是 `state.todayUsage`，来自宿主 `publicBalance()` 的 `summary.amount`，也就是 **DeepSeek 账户余额差**；走 Command Code 这类聚合网关时 DeepSeek 余额根本不动，所以永远是 `0.00`。宿主 `publicBalance()` 新增下发 `localToday: summary.eventEstimate` / `localTodayCurrency`（当天所有模型本地会话估算合计，CNY），前端存进 `state.localToday`，`bubbleContentTokenMap` 的 `today` 分支新增 `map['expense_local']`（有值就用，取不到回退 `{expense_ds}`）。`{expense_ds}` 语义不变，其它模板照旧。 `{expense_local}` 还有一条纯前端回退：`apiLocalTodayFallback()` 在宿主没下发 `localToday`（旧代码 / 未重启）时向 `/dsh-whale/usage-records.json` 要一次 `today.total` 并重绘气泡 —— 用户只刷新页面就能看到正确数字。
**v813b**：修 v813 的两处实现错误 —— ①回退路径取的是 `usage-records.json` 的 `today.total`，那是 `summary.amount`（DeepSeek 余额差口径，走网关时恒 0），应取 `today.modelTotal` / `today.eventEstimate`（本地会话估算合计），且只在 `> 0` 时落值；②`apiModelsFetch` 无条件执行 `state.localToday = data.localToday ... : null`，老宿主没有该字段时会把回退补到的值冲成 null ⇒ 改为 `hasOwnProperty(data,'localToday')` 才覆盖；③`{expense_local}` 不再要求 `> 0`（取到 0 就是今天没花钱，只有 null/非数字才回退）；④`showCostBubble()` 弹出时也补一次回退，避免首屏数据未到。
**v813c（真因）**：前两版都没生效，因为**运行时真正下发的消耗气泡模板在宿主** —— `usageSettingsDefaults().turnCost.lines`（第 1718 行）里是 `{expense_ds}`，`readUsageSettings()` 用 defaults 合并后经 `/dsh-whale/usage-records.json` 的 `settings.turnCost` 下发给前端，前端的常量与「恢复默认」那份根本用不上。修：①宿主该行改为 `{expense_local}`；②前端 `bubbleContentTokenMap` 的 `today` 分支增加回退 —— `{expense_ds}` 在「官方口径 <= 0 且本机口径有值」时也输出本机口径，这样宿主未重启、仍下发老模板时刷新页面即正确；③回退失败（`!d.ok` / 网络错误）时重置 `apiLocalTodayAsked`，允许下次重试。
**v814（提醒只留一条）**：用户要求「气泡消息队列里最多一条，新消息把旧的挤出去」。原 `whaleSysPush()` 在「新一轮消耗到来」时把正在展示的旧消耗项 `whaleSysQueueInsert(pre2)` **退回队列** —— 旧项当时是 `whaleSysItem`（正在显示），而上面的去重只清 `whaleSysQueue` 清不到它，于是旧项又变成一条待点击的队列项。改为：①账单类（`cost` / `alert`）**共用同一个展示槽与队列槽**，新来的一条直接把旧的丢掉（不退回队列）；②入队前把队列里所有 `cost`/`alert` 项清空；③`wait`（授权/提问）仍独立保留，不会被账单类顶掉。

**v815（统一「今日已用」口径）**：用户反馈「点一下气泡显示 `$ 0.48`，每轮对话结束弹出的消耗气泡显示 `¥ 1.40`，两处对不上」。真因是 `{expense_local}` 取的是**所有厂商本地估算合计（人民币）**，而点击气泡的 `{model_today}` 取的是**当前模型余额差实扣（美元）**，两个口径被摆在了同一屏。改为：`{expense_local}` 优先取 `apiModelBalanceInfo(apiCurrentModelId()).today`（与 `{model_today}` 完全同源），取不到才依次回退「本机估算合计」→「官方口径」。同步更新前端默认消耗模板的注释与占位符帮助文案。
