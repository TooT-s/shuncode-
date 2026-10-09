# ShunCode 0.8.1 更新日志（详细版；相对 0.8.0）

- **最后更新：** 2026-10-04（原版发布于 2026-10-01；10-03 条目保留当时状态，以最新增量为准）
- **适用：** Windows x64、macOS Apple Silicon、Linux x64；Code-OSS 基线保持 1.136.1。
- **版本边界：** 下方“0.8.0 历史记录”原样保留，供追溯此前能力与当时的发布记录。历史记录中的 0.8.0 安装路径、产物摘要、安装状态及旧策略均不代表 0.8.1；本节优先。此前 0.8.1 三端安装包已经构建，但生成于本轮后续源码／日志修订之前，不能据版本号推断包内已有最新修复；只有重新正式构建、比对包内功能与日志并核验新 SHA-256 才能作为新成品。

## 2026-10-04 任务展示栏高度可拖动（三端新一轮正式构建）

- **GitHub 登录诊断一并随新包交付：** `vscode-main/extensions/github-authentication/src/common/loginFailure.ts` 的最小分类只把明确的代理隧道失败、已知 DNS／证书错误与回调等待超时按证据提示；普通连接失败不冒充用户未授权，也不因存在 `http.proxy` 设置就断定实际路由。设备码超时与回调超时区分，敏感 URL／OAuth `code/state`／Token 不写入日志。既有代理／PAC／noProxy、设备码偏好和 TLS 校验保持原样；终端用户 Windows 电脑仍需自行复测登录和 Bridge 授权，不从开发机模拟结果推断修复了其网络。
- **交互：** Chat／Bridge 侧栏上方的任务展示栏增加底部拖动手柄，向下拖动可扩展展示区域；依据侧栏尺寸为原聊天、输入区及 Bridge MCP 活动和控制区保留最小空间。支持方向键微调、Home 或双击恢复默认；折叠时不占自定义高度，重新展开恢复。窗口变窄或变矮时按可用高度限制显示，不擅改任务内容或授权逻辑。
- **验证与边界：** 三端 `tests/task-shelf-resize.test.mjs` 在隔离浏览器中验证拖动、键盘、折叠、窄侧栏，定向测试各 5/5 且 Workbench TypeScript 检查通过。Mac DMG、Windows Setup（包括 `-SkipTests`）、Linux tar.gz／DEB 正式门禁必须运行新测试，并检查成品 workbench JS／CSS 中的拖动手柄与指针事件。此前同版本旧包及已安装程序不因源码更新而改变；须以本次正式包内检查、日志逐字节比对和新 SHA-256 为准，**本轮不代安装**。GitHub 登录诊断亦仅在包含相关修复的新包中可用，真实用户网络及回调须在用户设备自行复测。

## 0.8.1 本次变更

### 2026-10-03 后续源码修复与发布前门禁（尚未重新出包）

- API 渠道的 Base URL 在 Chat Completions 与 Responses 使用同一归一化规则：根地址、`/v1`、`/v1/` 和自定义 `/api/openai/v1` 前缀均避免重复拼接。渠道表单“测试”按钮仅请求 `GET /models`；这不等于真实聊天推理成功。请求失败时日志给出脱敏的最终 endpoint、HTTP 状态与 Content-Type，不记录 API Key 或 HTML 正文。Agent Host 使用模拟 HTTP 服务分别覆盖实际 Chat／Responses POST 路径、自动协议回退；真实网关和真实 Key 均未验收。
- 模型选择器按渠道分组，渠道与组内模型允许分别拖拽并持久化排序；搜索不改变原排序，新模型追加末尾，选中项不被滚动条遮挡。麦克风状态与可操作错误提示、顶部／中间／底部渠道右键菜单的渠道类型和弹出方向均有定向回归；保持 Responses 手动选择与自动回退、API Base URL 不暴露及排队消息顺序。
- Mac DMG、Windows Setup（含 `-SkipTests`）、Linux tar.gz／DEB 正式入口增加不可跳过的 `/models` 路径与脱敏诊断回归、真实 Agent Host 模拟 Chat／Responses 请求回归，沿用聊天 UI 门禁；三端生产扩展 bundler 校验 URL helper 进入扩展 bundle，Agent Host 的 OpenAI 处理进入运行时。Linux DEB 复用既有产物仍需通过回归与包内标记核验。三端定向本地模拟验收先前为 38/38，不能替代正式出包、真实网关或安装版验收。
- 本节仅记录 0.8.1 后续源码和脚本。2026-10-02 已生成的 0.8.1 旧安装包**不包含**本节改动及更新后的日志；本轮未重打包、未安装、未部署服务。交付这些修复前必须重新正式构建，并核对新包内两版日志及新 SHA-256。

1. **修复已登录用户启动 Bridge 被旧授权状态阻止（三端）。** 账号已经登录、但 Bridge 页面仍显示尚未授权时，点击“启动 Bridge”会调用与“刷新授权”按钮相同的在线授权刷新流程（沿用既有超时预算和进度反馈）；只有在线返回已授权且没有错误，才继续启动。此前必须手动先点“刷新授权”，否则即使权益有效也会直接提示购买或激活。
2. **未登录与失败处理。** 未登录时提示先使用 GitHub 或 Gitee 登录，不自动弹出登录、也不排队自动续启；配置不完整时继续先给出隧道预检查提示。在线刷新返回未授权、网络/代理错误、超时或无响应都不会启动，不以尚未到期的本地缓存授权代替这次在线确认。Bridge 已运行或正在启动时主按钮仍用于停止/取消启动，不受新流程影响。
3. **保留独立的启动安全门禁。** 即使页面本地显示已授权或刚在线刷新成功，扩展中的 `requireFeature("bridge")` 仍会在监听器/隧道启动前再次在线校验。运行中的临时网络/代理故障仅在已验证签名令牌未到期时保活并重试；签名到期立即在线复核，无法确认须停止，停止前再做独立在线检查；明确拒绝、撤销、无效签名立即拒绝。该运行期策略继承自 0.8.0，不应算作本次新增。
4. **版本与发布日志。** `package.json`、`package-lock.json` 的两处项目版本、`extensions/shuncode/package.json`、`vscode-main/product.json` 及 `src/version.ts` 同步为 `0.8.1`。本文件为详细版，`docs/mcp-changelog-0.8.1-2026-10-01-summary.md` 为简版；正式包应同时包含 `SHUNCODE_CHANGELOG.md` 与 `SHUNCODE_CHANGELOG_SHORT.md`，发布门禁逐字节校验两者，旧 0.8.0 包不会因源码升版而自动变化。
5. **回归范围。** 启动按钮覆盖在线刷新成功、网络失败但缓存仍有效、在线明确未授权、未登录、本地已授权以及后端独立在线门禁；此前构建阶段运行过版本/发布日志契约、浏览器 UI parity 和平台正式打包校验。本轮另做 Mac 本地模型协议模拟核验、更新源码日志；不能把先前打包校验视为对后续改动的成品验收。测试及打包只说明对应当时的自动化门禁通过，不等于真实付费账号、GUI、AI 网关或公网隧道端到端验收。
6. **支付状态与续费提示（三端）。** 已支付订单的已知权益到期时间早于当前时间时，不再把历史订单当成“正在同步”的新付款反复刷新；当服务端明确返回无有效权益时，允许按新套餐正常选购。新近已支付而服务端仍返回 `No active bridge entitlement` 的订单保持**禁止重复付款**，页面明确提示联系支持核对当前登录账号、订单与服务端权益记录。客户端不会自行补发或延长任何权益；要定位这种新订单异常，仍须核对服务端订单履约、权益状态与审计记录。
7. **订单三分钟支付窗口＋30 秒缓冲（三端 Worker 源码和打包门禁）。** 依用户确认，新单实际截止为创建后 **210 秒（3 分 30 秒）**；旧订单即使数据库保留 30 分钟期限，也以创建时间加 210 秒与原截止时间中较早者为准。订单读取与支付入口立即判定，Cron 每分钟将无人访问且仍为 `pending` 的行改为 `expired`（数据库状态可能晚至下一次分钟扫描落地），不会覆盖 `paid`；经过验签、商户、金额、交易号核查的迟到付款仍幂等履约，Windows 保留支付平台主动核对能力。三端正式打包入口现在不可跳过地运行同一支付订单源码／Cron／回调回归门禁；仅打包桌面应用不等于部署 Worker。Mac 11 项、Linux 10 项、Windows 5 项 Worker 测试已通过。此前已制作一轮 0.8.1 桌面安装包；仍未部署 Worker，线上仍按生产部署版本运行。
8. **API 供应商已存密钥编辑（三端后续源码）。** 编辑供应商时可以保持现有安全存储的 API Key 不变；若接口地址改变，测试不把旧 Key 静默发送到新地址，仍要求用户重新输入。原有配置、模型选择及 SecretStorage 不因协议选择而迁移明文密钥。三端对应行为回归各 6/6 通过；这些改动在上述先前的 0.8.1 安装包生成之后，尚未重新打包。
9. **`/v1/responses` 手选与自动协商（三端后续源码）。** UI 新增 Chat Completions（默认）、Responses、自动三项，编辑回显原配置；配置 schema 与 JSON 导入支持 `auto`，curl 导入的 `/responses` 端点可识别为 Responses。普通聊天与 Agent 流式工具调用都支持明确的 Responses 请求及同一网关的路径规范化。自动模式在**首次真实模型请求**优先 Responses：仅 HTTP 404／405／501、尚未输出且未取消时回退 Chat Completions；能识别的“模型不存在”、401／403、429、其它 5xx、超时、流式已输出内容以及返回 HTML 200 均不会回退。无额外能力探测 POST；负面路径识别短期缓存五分钟，不保存原始 API Key。表单“测试”仅验证 `GET /models`，不能保证实际 POST 路径可用；网关若无模型目录，原有表单新增流程仍受限。三端本地模拟网关 Agent／Agent-host 回归各 **35/35** 通过，根工程／扩展 TypeScript 检查通过；Windows/Linux Workbench 聚焦检查 0 错误，Mac Workbench 全量检查通过。未用真实网关或 Key 做端到端验证。
10. **Mac 截图错误的只读核验。** 当前安装的 `/Applications/ShunCode.app` `CFBundleShortVersionString` 是 **0.8.0**，截图表单也没有新增的协议选择；此前 0.8.1 DMG 文件生成于 2026-10-01 18:28，而本次 Responses 源码改动时间晚于该 DMG，旧 DMG **不包含**本项新能力。截图中发现 44 个模型只证明模型目录可访问；`Runtime -32000: Model endpoint returned non-JSON (200)` 后接 `<!doctype html>` 表明实际模型请求拿到了网页，不是 JSON／SSE，不等于 Key 被拒绝，也不能据此推断具体上游是哪一个。Mac 本地无真实 Key 模拟：`GET /models` 成功、Chat POST 返回 HTML 200 时复现相同报错；在同一模拟网关上 Responses 可成功回复 `1`；自动模式遇 HTML 200 不静默改走 Chat。实际网关 URL／路径仍需用户以不含密钥的方式提供或在更新并安装后的应用中自行验证，本次未读取已存 Key、未向真实网关发模型请求。
11. **聊天展示与排队对话（三端后续源码）。** 参考 Devin 的信息层级和可用空间原则，不复制品牌视觉：主输入工具栏伸展以使用发送工具栏之外的可用宽度，保留发送按钮独立空间；空间受限时先隐藏模式文案，再隐藏模型名称，模型名称允许使用更长的可见宽度；紧凑状态下模型与配置图标各自保留位置，长配置名截断。用户消息在宽窗口下有合适的阅读宽度，助手普通段落限制行长，长链接换行，不裁掉代码块或复杂内容。编辑排队消息显示发送箭头，提交仍遵守原队列顺序、不停止当前回复；模型选择不显示 API Base URL，错误卡片不铺开 HTML/data: URL/base64。`/v1/responses` 手选、自动识别与安全回退保持原有逻辑。新增布局单测和 5 项聊天 UI 源码回归，并将后者接入三端正式打包入口的不可跳过门禁（Linux 的基础构建与复用旧产物的 DEB 入口均覆盖）。这只是源码与自动化校验，未做新版已安装应用的视觉验收；本轮仅修改打包脚本，不执行打包或安装。
12. **2026-10-02 模型选择菜单尺寸（最新三端源码）。** 用户截图中的菜单被冗长的模型元信息撑到接近窗口宽，因为列表只设置最小宽度而没有最大宽度，按最长单行描述自动测量。现在菜单最大 560px，并按当前窗口宽度预留 32px 边距；仅对有描述的模型行给名称优先空间，超出的描述在一行内省略，搜索文本及无障碍完整描述不丢失，悬浮卡片仍可查看详细能力与费用。三端新增源码回归及 Mac Chromium 渲染回归。**该源码变更晚于本日 Mac→Windows→Linux 顺序打包的 0.8.1 产物，旧包不包含此项；本轮未重新出包或覆盖安装**，截图环境也未作新安装版视觉验收。

13. **2026-10-02 Mac Bridge 左下栏健康检查排版（后续源码）。** 之前 Mac 将健康检查结果接在 `Streamable HTTP` 等连接元信息后面，形成同一行；现在参照 Windows 在元信息下方单设健康状态行，正常／异常显示状态点和原有摘要，长错误在窄侧栏内换行。没有检查结果时不显示该行；重新获取状态不保留过时文案。仅调整 Mac 展示层及定向源码回归，不改变健康探测、MCP 连接、授权或隧道行为。Windows 原有独立健康行无需改动，Linux 未在本轮修改健康 UI。**此 Mac 源码修复及本次简、详两版日志修订都晚于前述三端 0.8.1 安装包**；旧包不含此修复，本轮未重新打包、安装或进行安装版视觉验收。

14. **2026-10-02 多 API 渠道名称与协议编辑（三端后续源码）。** 模型选择器对多个 ShunCode API 渠道显示“短渠道名 / 模型名”，不以整条 Base URL 充当可见渠道名；旧配置若将 URL 当作渠道名，仅提取主机名，相同短名编号区分。保留其他供应商原有模型标识与菜单分组。新建渠道协议选项默认显示简体中文“自动（默认）：优先 Responses，端点不支持时回退 Chat”；已保存但缺少协议字段的旧配置仍走原 Chat Completions 默认，不擅自迁移。显式 Chat／Responses 使用标准根地址或 `/api` 时补 `/v1`，已有版本路径不重复；自定义代理路径与 DeepSeek 官方根地址不强行改写。自动模式若原地址无 `/v1`，仍从原根路径请求模型列表及 Responses/Chat，保持此前可用的网关；不放宽既有 404／405／501 且未输出时的安全回退条件。编辑既有渠道时，同一网址来源下仅修改路径、补齐 `/v1` 或切换请求协议，不用重新输入安全存储的 API Key；网址协议（http/https）、域名或端口改变仍必须输入新密钥，测试不会自动将旧密钥发送到新来源。表单“测试”只检查模型列表，不保证实际 Chat／Responses 请求可用。三端新增路径和密钥回归以及 Mac Chromium 模型菜单回归；未读取真实密钥、向真实 API 发送请求或完成安装版视觉验收。**该项晚于先前打包的 0.8.1 安装包，本轮未重新打包或安装。**

15. **2026-10-02 支付页不再导向后台／受控支付窗口（三端后续 Worker 源码）。** 公开 Worker 首页 `/` 不再重定向 `/admin`，仅显示普通服务提示；管理后台 `/admin` 与管理员鉴权仍保持原路径。受随机令牌哈希校验的结算入口展示启动页，点击后打开独立支付窗口；`/start` 保留原支付平台提交流程（Windows 仍优先跳转已保存的支付链接及其原后备表单），`/status` 在令牌有效时只返回订单状态及真实截止时间，不发放权益。确认 `paid` 或未付款到 210 秒变成 `expired` 后，启动页与支付结果页尝试关闭支付窗口／页面；过期不冒充付款、订单不删除，真实验签的迟到回调依旧可幂等履约。因系统浏览器启动标签、弹窗拦截或第三方页面隔离策略可能阻止 `window.close()`，自动关闭为尽力而非保证，保留手动关闭提示。三端 Worker `npm run check`：Mac 16/16、Windows 10/10、Linux 15/15；均为本地模拟源码回归，未创建真实订单或付款，未部署 Worker，未重新打包／安装；线上行为须在授权部署后核验。

**2026-10-02 打包门禁补记（仅源码）。** 新增 `scripts/verify-payment-checkout-window.mjs`，静态检查公开 `/` 不导向后台、结算 `/start` 与 `/status` 路由、令牌校验、仅成功／过期时尝试关窗、手动关闭提示和 Windows 原支付链接跳转，并实际运行 `payments-managed.test.js`（无真实订单）。Mac DMG 正式入口、Windows Setup 正式入口（即使指定 `-SkipTests`）、Linux tar.gz 构建入口及 DEB 入口均新增不可跳过的核验；Mac/Windows 同时将门禁脚本和回归测试列入必需源码清单。原有 210 秒截止／分钟 Cron／验签迟到履约门禁仍保留。已单独执行门禁与脚本检查；**没有运行完整打包、覆盖安装或 Worker 部署，也未验证安装包内含本次改动**。

16. **2026-10-02 MCP 任务卡流程中文展示（三端后续源码）。** Bridge `set_todos`／`update_plan`／`report_progress` 工具标题、任务状态确认文案与任务卡相关界面固定文案改为简体中文；工具规则、参数说明与原生工具提示要求新提交的步骤标题、阶段和进度使用简体中文。任务卡标题、各生命周期状态、未上报计划、当前步骤、历史步骤和进度等中文后备文案在英文界面下也保持中文。`task_id`、`status`、`lifecycle` 等协议字段及枚举值保持原样，任务归属、修订号和状态校验不变；已经由客户端提交的历史英文步骤或任意自由文本不自动翻译，须由调用方按新规则提供中文。新增 `scripts/verify-task-card-chinese.mjs`：Mac DMG 在仅打包预检时先做无需依赖的源码检查，依赖就绪后再执行任务回归；Windows Setup 即使指定 `-SkipTests` 也执行完整门禁；Linux tar.gz 执行完整门禁，复用现有产物的 DEB 执行不依赖开发包的源码门禁。Mac/Windows 将门禁列入打包必需源码清单。原有任务生命周期与安装包源码核验保留。以上仅是三端源码、脚本、回归和双版日志，既有安装包与运行中的 Bridge 不会热更新；未重新打包或安装。 源码核验：Mac 任务／修复／布局合并测试 41/41，Windows 任务／修复测试 36/36，Linux 32/32；中文打包门禁三端源检与对应测试均通过，Mac／Linux Bash、Windows PowerShell 入口语法及 0.8.1 日志源码契约通过。

## 获取与安装边界

- Windows：正式 Setup；macOS：Apple Silicon DMG；Linux：x64 tar.gz 与 DEB。2026-10-02 已按 Mac→Windows→Linux 顺序打包并核验此前聊天及协议源码；**随后修复的模型菜单限宽、Mac Bridge 健康行、本次 API 渠道／协议编辑优化与支付窗口／首页修复及任务卡中文化不在该批包内**，原包内日志哈希与此后修订的简、详两版日志也不同。只有重新正式打包并校验新包的 SHA-256 才能交付这些后续修复。打包不自动安装，也不等于已签名、公证或已在真实账号环境验收。
- macOS 默认只打包；覆盖安装必须显式使用 `SHUNCODE_OVERWRITE_INSTALL=1`。Linux 默认只打包；覆盖安装须显式使用 `--rebuild --install --yes`。请先备份工作并退出应用；Windows 安装仍需用户手动执行 Setup。本次发布不主动覆盖已安装应用。
- 默认复制提示词保持：快速连接这个 MCP（URL），先读工具规则；每次对话先建/更新任务卡，确认后等待任务。终端均为 Bash，勿按系统猜 Shell。

---

## 0.8.0 历史记录（以下为原版归档，不是 0.8.1 产物或新功能）
# ShunCode 0.8.0 更新日志（相比 0.7.7）

- **首次记录：** 2026-09-30
- **最后更新：** 2026-10-01（补记第一种 Bridge 的 CF 优化、运行期授权连续性、任务卡不可用兜底及发布门禁；版本保持 0.8.0）
- **适用：** Windows x64、macOS Apple Silicon、Linux x64 三端共用同一套版本定义、MCP / Skill / UI 契约与发布校验；平台专有条目仍按原标注适用。
- **对比基线：** 0.7.7（三端源码在本次升版前的版本）。

0.8.0 是在 0.7.7 之上的一次升版，承接 Code-OSS 1.136.1 基线、授权策略收紧和若干界面修复。版本号是标准三段 SemVer：`0.8.0`。

**发布状态：源码更新或隔离测试通过不等于已打包、已安装。** Windows 本次正式构建是否成功、是否产生新安装包，须以 `release/ShunCode-0.8.0-win32-x64-build.json`、Setup 文件时间与 SHA-256 校验结果为准；其他平台须以各自原生主机的构建记录为准，不能混用同名旧包。生成 Setup 不会自动覆盖安装，也不代表真实账号或 GUI 已验收。

### 2026-10-01 0.8.0 Windows 正式打包前补记（版本不变）

- **第一种 Bridge（CF / Cloudflare）优化（优先展示）：** 临时隧道使用 ShunCode 独立配置，避免读取用户现有 `~/.cloudflared/config.yml` 而导致 Bridge 地址 404；Windows 在 UDP/QUIC 不通时可改用 HTTP/2。自动路由先直连，满足超时且已配置可用代理时才尝试经代理连接 7844 端口；直连与经代理也可显式选择。失败指引区分边缘连接、系统 TUN/fake-IP 与公网健康检查，不把开启全局 TUN 或设置 `http.proxy` 描述为必然修复。以上为既有 0.8.0 第一种 Bridge 能力的提要，不冒充本轮新增的网络实现或端到端验收；详细说明见下文「临时隧道（第一种 Bridge）」。
- **运行期授权连续性（三端）：** 新启动 Bridge 仍须在线校验；已经运行时，网络／代理临时故障仅在本机已有的签名授权经验证且未到期时允许暂时保活，并继续重试。签名到期按其到期时间立即发起在线复核；无法确认则停止，自动断开前再做一次独立在线校验，若复核成功便继续运行。在线明确拒绝、授权签名无效或权益被撤销不走离线宽限。下方历史旧授权策略描述以本条为准；本条不代表旧安装包已经更新，也不代表已使用真实付费账号验收。
- **任务卡提示的不可用兜底：** 在使用 ShunCode Bridge MCP 时，对话开始先以 Bridge `set_todos` 建立或更新任务卡；不可用时只尝试一次 Bridge `update_plan`，两者都不可用则说明一次、继续用户任务，不循环重试，并在最终答复如实说明任务卡未同步，绝不假称成功。`report_progress` 仅是临时进度，不代替持久任务卡；未使用 Bridge 的原生工作不被此规则阻塞。Bridge 指令、原生工具 `modelDescription` 与默认复制提示词的分工沿用既有三层设计。
- **Windows Hono 生产依赖安全修复：** 本次正式打包在不可跳过的根仓库 `npm audit --omit=dev --audit-level=moderate` 门禁发现原锁定的 `hono 4.13.5` 命中 `GHSA-hxh3-vqpv-xpqv`（JSX 边界组件对纯字符串转义不足）；将 Windows 根依赖精确锁定到 `4.13.12`，同步 npm 锁文件并把离线安全下限及降级回归提升到 `4.13.12`。只改锁文件根声明与 `node_modules/hono` 项，不运行 `npm audit fix --force`，仍须以重新安装和复跑在线审计的结果确认可打包。
- **Windows Carrier Axios 传递依赖安全修复：** 根仓库审计恢复后，正式打包的 Carrier 生产依赖审计又发现 `axios 1.18.1` 的高危公告（含 `GHSA-44g4-m2mj-wpvx`）；它由 `@microsoft/dev-tunnels-management` 的兼容范围 `^1.8.4` 引入。Carrier 为 Axios 加入精确 `1.20.0` override（当前首个可获得、超出审计所报 `1.0.0–1.19.0` 受影响范围的版本），锁文件仅 `node_modules/axios` 项变化，离线版本下限和回归同步提升；不执行跨大版本或整树自动修复，仍以重装后在线审计、发布检查和最终成品为准。
- **Windows 打包强校验：** 正式入口 `PACKAGE_SHUNCODE_WINDOWS.ps1` 的 `tests/release-repairs.test.mjs` 检查兜底不能回退；最终应用在生成 System Setup 前运行 `scripts/verify-release-repairs.mjs`，检查已打包 Bridge、原生工具描述及 Workbench 提示；`-SkipTests` 也不能跳过这两道检查。此处记载源码与打包流程的检查契约，不预先宣称新 Setup 已构建或安装。


### 2026-09-30 0.8.0 升版内容（三端）

- **版本定义同步：** `package.json`、`package-lock.json`（顶层与 `packages[""]`）、`extensions/shuncode/package.json`、`vscode-main/product.json` 的 `shuncodeVersion`、`src/version.ts` 的 `SHUNCODE_BEHAVIOR_VERSION` 六处统一为 `0.8.0`；三端打包入口在任何耗时步骤之前核对这六处，产物名由该版本号派生，不在脚本里硬编码。
- **发布说明与校验契约：** 本文取代 0.7.7 的发布说明成为打包进应用的 `SHUNCODE_CHANGELOG.md`；`scripts/verify-release-notes.mjs`、`scripts/verify-bridge-076.mjs` 与浏览器特性契约（`scripts/browser-feature-contract.json` 及其基线）同步到 0.8.0，其余 0.7.7 增量校验保持不变。
- **Linux 补齐 Bridge/浏览器发布门禁（2026-09-30 三端对齐）：** 此前 `scripts/verify-bridge-076.mjs`、`scripts/browser-feature-contract.json` 及其依赖只存在于 Windows/macOS 树，Linux 缺失，「三端同步」不成立。现已把 `verify-bridge-076.mjs`、`verify-task-display.mjs`、`verify-bridge-continuity.mjs`、`verify-browser-feature-parity.mjs`、`tests/browser-feature-release.test.mjs`、`tests/browser-packaging-contract.test.mjs` 与 golden fixture 移植到 Linux 树，并按平台特异性做两处必要调整：`verify-bridge-continuity.mjs` 的平台枚举扩为 `mac|win|linux`（三端同步）；打包门禁测试新增 `process.platform==='linux'` 分支，校验 `scripts/linux/build-linux.sh` 的源预检、回归与产物门禁顺序及 `build-deb.sh` 的 follow-latest 标记。基线不共用同一份：Linux 的 `browserArenaFollowLatest.ts` 与 `browserArenaFollowLatestPage.ts` 保留 Linux 专有的回退滚动实现，故新增 `tests/fixtures/browser-linux-080-baseline.json` 记录本端哈希，其余 5 个文件与 Windows/macOS 规范化后逐字节一致。`scripts/linux/build-linux.sh` 已接入 `verify-bridge-076 .`、`verify-browser-feature-parity source|run|artifact` 四道门禁。Linux 实跑：`browser parity source/run` 通过，33 用例全绿；`verify-bridge-076.mjs` 返回 `sourceVersionsAligned:true`。
- **启动失败契约的三端差异（补记）：** `shunCodeBridgeStartupFailureCatalog.ts` 并非三端逐字一致——Windows 独有失败码 `tunnel-retrying` 与动作 `reload-window`，macOS/Linux 无。该差异为 Windows 隧道重试路径所需，属有意保留；此前日志只记录了 `PRODUCT_WORDS` 白名单的平台差异，未记录枚举差异，现补齐。
- **修复：Windows `run_command` 结果信封误报协议错误（三端源码对齐）：** Windows PTY 分支在 `ide-tool-broker.ts` 输出 `echo_gate`、`echo_gate_released_bytes` 两行，但 `src/ide-tool-output.ts` 的字段白名单未登记，导致每次 `run_command`/`get_command_output` 都抛出 `Invalid run_command result envelope. The operation may already have executed; do not retry automatically.`——命令实际已执行成功（`status: completed`、`exit_code: 0`），客户端却可能据此误判失败并重试，非幂等命令有风险。现已把两个字段补入白名单。macOS/Linux 不产生这两行、白名单同步保持一致。该修复在重新构建 `dist` 并覆盖安装后才对已安装实例生效。
- **Code-OSS 1.136.1（三端）：** 三端 Carrier 源码统一为 Code-OSS 1.136.1（Windows 原为 1.132.0），工具、提示词、界面与核心功能按同一基线对齐；平台环境造成的差异仍然保留。
- **严格授权校验（三端）：** Bridge 启动与运行中的周期复核（15 分钟）必须能与授权服务器校验成功；离线、超时、429、5xx 等“无法校验”的情况不再回退到本地缓存令牌，Bridge 不启动。401/403/404 的既有处理不变，缓存令牌仍保留以便网络恢复后自动恢复。界面新增“授权无法验证，Bridge 未运行”的原因提示（含网络/代理排查建议）。已在运行的 Bridge 会在下一次 15 分钟复核时停止。
- **API 供应商页（智能体自定义）可滚动（三端）：** 供应商状态行（例如“已添加……已加载 4 个模型”）出现后表格被挤出可视区且无法滚动的问题已修复：模型表设最小高度，状态变化后重新布局，外层容器改为纵向可滚动。Codex 页共用同一组件，同样生效。
- **macOS：** 修复 `toFront` 调用点（3 处）；隧道端口只做进程组终止、校验归属后回收，不误杀未被跟踪的进程（不使用全局 pkill 扫描，不使用 flock 监督进程）。
- **工具调用次数不再上传（三端，2026-09-27 起）：** 客户端不再上报 MCP 工具调用次数，三端已有测试守住这一点；本地活动统计仍保留。
- **Bridge 页头新增「新建窗口」按钮（三端）：** 位于「打开文件夹」左侧、同一行动作区，使用与其完全相同的主按钮样式（`defaultButtonStyles`，非 secondary 变体），图标为 `empty-window`。点击直接执行文件菜单本身的命令 `workbench.action.newWindow`，不自建窗口逻辑，因此窗口还原、配置文件与远程行为与「文件 > 新建窗口」一致；提示词包含快捷键 Ctrl+Shift+N（macOS 显示 ⇧⌘N）。命令 ID 与快捷键规则放在 `shunCodeBridgeOpenFolder.ts`，由回归测试固定；失败会以提示条显示原因，不静默吞掉。
- **启动中「启动 Bridge」变为「取消启动」（三端）：** 此前 `starting` 状态会把主按钮置灰并显示「启动中…」，隧道卡住时用户无处可点。现在该按钮在启动期间保持可点击，文案与图标切换为「取消启动」（`stop-circle`），点击即执行既有的停止命令，让 Bridge 回到已停止状态；`running` 时仍为「停止 Bridge」，其余控件的禁用规则不变。
- **API 供应商页汉化（三端）：** 该页由上游 `localize()` 构建、语言包只有英文，中文界面下长期中英混排。新增 `chatManagement/chatModelsLocalization.ts` 中文文案表（与 Bridge 页共用 `isSimplifiedChinese()` 判定），`chatModelsWidget.ts` 的 187 处调用统一改走 `localizeModels()`：命中则显示中文，未命中回落上游英文原文，不会出现空串或键名。覆盖接口地址/API Key 表单、测试与添加、模型表头与列、能力与价格、Codex 账号区、导入供应商的各类校验错误以及无障碍标签；左侧导航项与说明也改为「API 供应商」。品牌名（GitHub Copilot、Codex、API Key 等）保持原文。
- **三端打包脚本同步更新：** `scripts/verify-bridge-076.mjs` 的 Bridge 页头标记表新增 `shuncodeBridge.newWindow`、`shuncodeBridge.newWindowTitle`、`shuncode-bridge-new-window-button`、`shuncodeBridge.cancelStart`（三端同一份文件）；Windows `PACKAGE_SHUNCODE_WINDOWS.ps1` 增加新建窗口、取消启动、API 页中文文案表与 `localizeModels(` 四条源码断言，并在正式包工作台产物上复核新建窗口与取消启动标记；macOS `package-mac-dmg.sh` 与 Linux `build-deb.sh` 增加同一组 `require_source_marker`。回归：`tests/bridge-open-folder.test.mjs` 扩为覆盖两个按钮（真实 Chromium 中校验数量、顺序、主按钮配色、快捷键提示与点击所执行的命令）与取消启动的源码契约，三端 3/3 通过；`tests/bridge-076-release.test.mjs` 固定件同步（Windows/macOS）。
- **验证：** 三端 `tsc --noEmit -p src/tsconfig.json` 通过；`node scripts/verify-bridge-076.mjs .` 返回 `sourceVersionsAligned:true`；`browser parity source` 通过。本轮未打包、未签名、未安装。
- **configure_mcp 配置文件位置可被发现，默认与 Skill 同处（三端）：** 此前外部 MCP 服务器清单固定写在 `~/.shuncode/external-mcp.json`，工具的任何输出都不提示该路径，智能体只能满盘搜索仍找不到。现在：默认位置改为 **Skill 全局目录（`list_skills` 返回的那个目录）下的 `external-mcp.json`**；旧的 `~/.shuncode/external-mcp.json` 在首次读写时**自动迁移**过去，原文件保留为 `.migrated.bak`，不删除。若 Skill 全局目录对当前用户不可写（例如 Linux DEB 装在 `/usr/share` 的 root 目录），则保持使用用户主目录那份，并在输出中说明原因，绝不会因为目录只读而让 `add` 失败。
- **configure_mcp 新增只读动作 `action=where`（三端）：** 返回配置文件绝对路径与是否存在、Skill 全局目录、旧位置及迁移情况、密钥存储位置（Windows 凭据管理器 / macOS 钥匙串 / libsecret），并声明路径属于所连接主机而非聊天沙箱；不触碰任何配置。同时 `add`/`list`/`remove` 的文本与 `structuredContent` 都会附带该路径，`list` 为空时也会说明「尚未创建」。密钥仍只存于操作系统凭据库，不写入该 JSON，也不回显。
- **回归：** `tests/configure-mcp.test.mjs` 增加 5 条用例（where 的内容与只读性、三个动作都回显路径、空清单提示、无位置信息时的降级、未知动作提示里包含 where），三端 14/14 通过；三端 `tsc --noEmit`（根工程与扩展工程）通过；`scripts/test-mcp-builtins.mjs` 190 通过 / 1 跳过。工具 schema 的 `action` 枚举与描述同步新增 where，MCP initialize instructions 未增长，Windows 打包的 5500 字符上限不受影响。
- **configure_mcp add 改为「配好环境并验证可用」（三端）：** 此前 add 只是写配置：HTTP/SSE 本就够用，但 stdio 服务器要等到连接时才暴露宿主没有运行时。现在 add 分三步——① **运行时就绪**：解析 `cmd /c npx …`、`bash -c "uvx …"` 这类包装取出真实可执行文件，`where`/`which` 探测；缺 Node.js 时按平台自动安装（Windows `winget install OpenJS.NodeJS.LTS`、macOS `brew install node`、Linux `sudo apt-get install -y nodejs npm`，可能弹 UAC 或需要 sudo），uv/uvx 因已内置从不需要安装；安装失败则**不写入任何配置**，并回传确切的手动安装命令与安装输出末尾。② **写入**：沿用原子写与凭据库存储。③ **连接验证**：刷新后真实连接一次并轮询状态，报告每个服务的 state 与工具数，首次 `npx -y` 下载也在这一步完成（默认最多等 180 秒，`verify_timeout_ms` 可调至 600 秒）。
- **验证失败时保留配置并请用户协助（三端）：** 未就绪的服务不会被自动回滚——配置与凭据保留，结果标记为错误并列出服务 id、上游报错（如 401）、以及「首次下载较慢／检查网络、代理与 API 密钥」的排查提示，用户修好后用 `action=test` 复测即可。新增参数：`install_runtime`（默认 true，设 false 则缺运行时即刻失败、不碰宿主）、`verify`（默认 true）、`verify_timeout_ms`。
- **新增纯模块 `src/mcp-runtime-provision.ts`（三端同源）：** 负责「哪种运行时 / 怎么探测 / 怎么安装」的判定，宿主副作用（which、install）由 Bridge 注入，因此可单测且不把 shell 逻辑带进扩展。Bridge 侧以不经 shell 的 `spawn` 执行探测与安装，输出截断保留末 64 KB 并写入 Bridge 日志。
- **回归：** 新增 `tests/mcp-runtime-provision.test.mjs`（4 条：包装解析、三平台 Node 安装计划、uvx 永不安装、未知可执行文件如实报告）与 `tests/configure-mcp.test.mjs` 7 条新用例（缺运行时先装后写、安装失败零写入并给命令、install_runtime=false 快速失败、已存在则不重装、验证失败保留配置并求助、verify=false、HTTP 不做任何运行时动作）。三端 25/25 通过，`tsc --noEmit`（根 + 扩展）通过。
- **打包脚本同步守住 configure_mcp 新行为（三端）：** Windows `PACKAGE_SHUNCODE_WINDOWS.ps1` 新增 6 条源码断言（`planRuntime`、`winget install`、`ensureRuntimes`、`verifyImported`、`describeLocations`、`defaultExternalMcpConfigPath`，路径均以 `Join-Path $repoRoot` 形式定义以符合 `windows-skill-mcp-packaging` 的断言解析规则）；macOS `package-mac-dmg.sh` 与 Linux `build-deb.sh` 增加等价的 `require_source_marker`（各含本平台的安装计划标记 `brew install node` / `apt-get install -y nodejs npm`）。三端脚本语法校验通过，`tests/windows-skill-mcp-packaging.test.mjs` 19/19 通过。
- **冻结的 17 工具契约快照同步（三端）：** `configure_mcp` 新增 `action=where` 与 `install_runtime` / `verify` / `verify_timeout_ms` 三个参数后，`tests/fixtures/mcp-tool-surface.json` 与实际 schema 不再一致，Windows 打包在「Three-platform built-in MCP, Skill, OAuth and UI parity (mandatory)」处失败。已用与该测试完全相同的规范化规则（剥离 description/title，保留 enum、默认值与安全标记）重新生成快照：三端产物逐字节一致（规范化 sha256 `04ff40c9acbd6a46…`，31,674 字节，仍为 17 个工具），`tests/mcp-skill-parity.test.mjs` 三端 16/16，`scripts/test-mcp-skill-parity.mjs` 三端退出码 0。
- **运行时探测/安装移出 Bridge facade（三端）：** configure_mcp 的宿主副作用最初直接写在 `bridge-server.ts` 里，触犯了既有迁移契约「facade 不得直接调用 `spawn()`」（`tests/bridge-tunnel-runtime-ownership.test.ts`），macOS 打包因此中止。现已抽出独立模块 `extensions/shuncode/src/mcp-runtime-host.ts`（`runProvisionCommand` / `hasExecutableOnPath`，不经 shell、argv 数组传参、输出截断保留末 64 KB、超时强制结束），facade 只做注入。三端核验：facade 中不再出现 `spawn(`，且 `this.domain`/`this.tunnelProcess` 等已迁移字段仍未回流；`tsc --noEmit`（根 + 扩展）三端通过。Windows 树未做该隧道迁移，其 facade 原有的隧道 `spawn` 属既有实现，保持不变。
- **Windows Carrier 镜像同步（仅 Windows 树）：** Windows 树在 `vscode-main/extensions/shuncode/src/` 保留一份 Bridge 扩展源码镜像，`tests/carrier-layout.test.ts` 要求它与 `extensions/shuncode/src/` 逐字节一致。本轮新增的 `mcp-runtime-host.ts` 与改动后的 `bridge-server.ts` 只落在主副本，导致「运行 ShunCode 原有测试集」失败。现已把两个文件同步进镜像；`npx tsc -p tsconfig.test.json` 通过，`node scripts/build-packaging-tests.mjs` 后 `node --test dist/tests/*.test.js` 为 **477 条 / 471 通过 / 0 失败**。macOS 与 Linux 树不含该镜像，无需同步。
- **本轮范围：** 仅修改三端权威源码、测试、打包脚本与本更新日志；已安装实例须经正式重建并安装后生效。

以下是 0.7.7 期间的更新记录，保持原文，作为历史。

### 2026-09-29 内置 configure_mcp 工具、实时联网解析接入方式与外部 MCP 上限放宽（三端）

- **内置 `configure_mcp` 工具（三端）：** 新增一个 AI 可直接调用的内置 MCP 工具，用 `action=add/list/remove/test` 四个动作管理**其它** MCP 服务器。它是一个工具（tool），不是 MCP 配置页里的按钮或面板。`action=add` 接受标准 `mcpServers` JSON、单个 `https://` URL，或带内联 API key 的一行启动命令，随后把密钥写入操作系统凭据存储并一步启用；`action=list/remove/test` 分别查看、删除与复测。用户给出 URL/密钥或已知 MCP 服务器包名时即可无感落盘、立即可用，不弹框、不分模式；工具不回显明文密钥。
- **实时联网解析接入方式（B 方案，三端）：** 不内置静态精选目录（静态表会过时、需人工维护，也会让模型记错 URL）。改由 AI 侧在需要时联网查询官方 MCP registry（`registry.modelcontextprotocol.io`）等权威来源获取最新接入方式；`configure_mcp` 自身保持纯执行器，不含联网/HTTP 代码，「联网查最新接入方式」的指令写进工具 description。
- **外部 MCP 服务器上限 20 → 100（三端）：** 将原先分散的上限收敛为单一常量 `MAX_EXTERNAL_MCP_SERVERS = 100`（`external-mcp-catalog.ts` 定义、`external-mcp-registry.ts` 以 `.slice(0, MAX_EXTERNAL_MCP_SERVERS)` 引用），三端取值一致；UI 无需改动。
- **Windows 打包指令长度修复：** 新增 `configure_mcp` 说明后，`BRIDGE_SERVER_INSTRUCTIONS` 长度升到 5527，超过 Windows 打包回归的 `< 5500` 上限。精简 MCP config 说明条目（443 → 395 字符，保留 configure_mcp、四动作、`mcpServers` JSON、`https://` URL、一行启动命令、内联 API key、OS 凭据存储、已知包名与「不回显密钥」等全部要点），长度降至 5479；三端该条目逐字对齐。
- **工具清单回归修复（macOS/Linux）：** `configure_mcp` 落地后，`tests/bridge-tool-prompts.test.ts` 的期望工具名表仍停留在 16 项、缺 `configure_mcp`，与实际 17 项不符而失败；将期望表补为 17 项（新增 `configure_mcp`）、断言标题同步为 17。Windows 不含该测试（改由 `windows-mcp-file-tools.test.mjs` 与专用 `configure-mcp.test.mjs` 覆盖）。
- **本轮范围：** 仅修改三端权威源码、测试与本更新日志；版本保持 `0.7.7`，未执行新的正式打包、签名、公证或安装，也未进行真实供应商账号授权。已安装实例须经正式重建并安装后生效。

### 2026-09-29 Carrier 生产依赖 ip-address 提升到 10.7.2（三端）

- **触发原因（Windows 实跑）：** 正式打包在 `Production dependency audit - Carrier (mandatory)`（`vscode-main` 的 `npm audit --omit=dev --audit-level=moderate`）失败。Carrier 锁定的 `node_modules/ip-address` 是 10.3.1，命中 2026-09-28 公布的两条 moderate 公告：`GHSA-rpw4-54j3-4h4q`（`Address6.isLinkLocal()` 只匹配 `fe80::/64`，漏判 `fe80::/10` 内其余地址）与 `GHSA-2vr4-cq9g-pvrc`（未识别 NAT64 本地用途前缀 64:ff9b:1::/48）；两条都可能被用来绕过基于地址分类的 SSRF／信任边界判断。生产引用链为 `@vscode/proxy-agent` → `socks-proxy-agent` → `socks`（`ip-address ^10.1.1`）与 `express-rate-limit`（`^10.2.0`）。
- **修复方式：** 不运行 `npm audit fix`（它会连带改写锁文件里无关的解析结果），而是把 `vscode-main/package.json` 的 `overrides.ip-address` 补齐并精确锁定为 `10.7.2`（Windows 原为 10.3.1、macOS 原锁文件为 10.2.0、Linux 为 10.5.0，均低于首个修复版本 10.5.1），再用 npm 重新生成 `vscode-main/package-lock.json`；逐条比对确认本次锁文件**只有** `node_modules/ip-address` 一个条目变化（版本、resolved、integrity 同步，`engines` 不变），其余已锁定依赖与父依赖范围（`^10.x`）没有被改写。10.5.1 是首个修复版本；取 10.7.2 是因为它当前在 `npm audit --omit=dev` 下同样为 0 漏洞、且已超过发布冷却期，可减少短期内重复提升的概率。
- **下限与回归（三端）：** `scripts/verify-security-dependencies.mjs` 的 `ip-address` 下限同步提升为 `10.7.2`，继续强制 override ＝ 下限、锁文件 ≥ 下限、已安装 ＝ 锁版本；`tests/security-dependency-packaging.test.mjs` 增加“10.3.1／10.5.0 必须被新下限拒绝”的断言；`tests/dependency-remediation.test.mjs` 增加攻击面回归：加载 Carrier 的已安装副本，要求 `fe81::1`、`febf::1` 判为 link-local、NAT64 本地用途前缀内的地址判为 private，同时 `fe80::1`、`64:ff9b::a9fe:a9fe` 等既有分类保持不变；`scripts/verify-release-notes.mjs` 与 `tests/release-notes-077.test.mjs` 增加本节标记，防止同日同版本日志回退。注（三端范围）：离线下限脚本与上述两项安全依赖测试目前只随 Windows 打包链路运行，Windows 树已包含，macOS/Linux 树未包含；这两树的核验为 `overrides`、锁文件与已安装副本三处同为 `10.7.2` 的逐条比对、同一组地址分类回归、`npm ci --dry-run` 复核，以及本机 `npm audit --omit=dev` 不再包含 ip-address。
- **Windows 实跑结果：** 修复后根仓库与 Carrier 的 `npm audit --omit=dev --audit-level=moderate` 均为 0 漏洞（Carrier 生产依赖 219 个）；`node scripts/verify-security-dependencies.mjs source|installed` 通过；使用打包脚本同一套工具链（Node 24.18.0 ＋ 项目本地 VS Build Tools）执行 `npm ci --dry-run --legacy-peer-deps` 返回 0，确认锁文件、`overrides` 与 `package.json` 一致。锁文件指纹变化后，正式打包会按既有逻辑重新执行 `npm ci` 恢复锁定依赖，再进入审计门禁。
- **边界：** npm 审计与离线版本下限是两道相互独立的门禁，通过它们不等于对全部依赖做过人工代码审查；本轮未执行打包、签名或安装，已安装实例不会热更新。

### 2026-09-29 Cloudflare 网络指引、uv/uvx 双端运行时与发布强校验（三端）

- **Cloudflare 诊断边界：** 修正边缘连接、通用网络失败和公网健康检查三类中英文指引，移除残留的“开启全局 TUN”建议；不把三端都描述成 UDP 优先，不把配置 `http.proxy` 说成必然回退成功。QUIC 使用 UDP 7844，HTTP/2 使用 TCP 7844；TCP 握手成功不代表 TLS／QUIC 已连通，公网 HTTPS 健康检查也不等同于边缘连接检查。
- **系统级 TUN／fake-IP 不是应用可自动修复项：** CONNECT 代理和直连 IP 都不保证绕过系统级 TUN。代理软件的直连／真实 DNS 规则或改用 ngrok 须由用户确认；本次没有关闭代理、改路由、绕过 TCC、结束隧道进程或新建公网隧道。附件中的 Mac 故障是特定机器的诊断，不推广成三端共同的网络结论。
- **当前只读证据：** 三端现有内置 `uv`／`uvx --version` 均可运行，版本为 `0.12.19`。本次 Mac 的 `region1/2.v2.argotunnel.com` 仍解析到 `198.18.0.97/98`，与所附 fake-IP 诊断一致；Linux 返回公网地址。这些检查不是 Cloudflare 隧道、TLS 或真实 MCP 包下载的端到端验收。
- **stdio 双端接线补齐：** 原生编辑器与 Bridge 现在使用同一受控环境函数，把捆绑 `runtime/bin` 作为默认 PATH 的末尾后备，已有用户工具优先；显式 PATH（包括空值及 Windows 的大小写变体）保持原样，不被追加。Windows 显式小写代理变量也不会被静默丢弃；宿主的无关凭据仍不继承，工作区信任检查不变。
- **成对运行时，不接受半个 uv：** `scripts/bundled-uv-runtime.mjs` 从同一候选目录预检完整 uv/uvx，检查原生 PE／Mach-O／ELF、目标架构、可执行性和一致的版本，再写入生成目录。显式 `SHUNCODE_UV_DIR` 是确定来源；正式构建强制需要完整运行时，不能用开发模式的缺失容忍来出包。可选开发构建缺失时清除旧的 uv/uvx 生成副本，避免“跳过复制”却悄悄保留旧文件。
- **打包入口与实际产物检查：** `scripts/verify-077-platform-assets.mjs` 在正式源预检及实际 app 上检查安全网络指引、Git 默认值与两个 uv 入口。Windows 在 Setup 前检查；Linux 在 source-state 封存和归档前检查；Mac 在签名前核对当前构建字节，显式签名两个 uv Mach-O 叶节点，再在签后、只读 DMG 和安装中转副本复核。保持原有停止、源状态、签名、回滚、摘要及不可跳过测试保护；不新增平行打包入口。
- **Git 提醒默认值保留：** 三端源码中的 `git.ignoreMissingGitWarning=true` 和 `git.enabled=true` 已核对；新增源／包检查防止旧 Git manifest 混入。没有修改用户个人设置或把 Git 禁用来隐藏提醒。
- **日志完整性：** 合并 uv／Git、Cloudflare 以及此前 Windows 和 POSIX 终端修复记录，保留历史包大小、哈希和测试数字的原归属；新日期与内容标记共同约束随包 `SHUNCODE_CHANGELOG.md`，不以相同版本号替代构建证据。
- **本轮范围：** 仅修改三端权威源码、测试、更新日志与正式打包脚本，并执行隔离回归和受限只读探测。版本保持 `0.7.7`；没有生成或覆盖 Setup／DMG／DEB，没有实际签名、公证、安装或真实供应商授权。已安装实例须经正式重建并安装后才会生效。

### 2026-09-29 Bridge 启动失败契约修复：edge-unreachable 详细排障文案三端统一、契约白名单补协议术语、network-failed 动作收敛

- **背景（发布预检拦截）：** macOS 打 DMG、Windows 打 exe 的发布前置契约 `tests/bridge-startup-failure.test.mjs`（P7 强制：每个启动失败码须有 ≤20 字中文标题、中文 cause 与 1–3 个 action，且中文文案里除产品名外不得出现连续英文）拦截打包。两个失败点：`edge-unreachable` 的中文 cause 含未列入白名单的标准协议缩写；`network-failed` 配了 4 个 action、超过上限。这些是「文案/配置与契约不同步」，非功能缺陷。
- **edge-unreachable 详细排障文案（三端逐字节统一）：** 把 `PROXY_TUN_ZH/EN`（`vscode-main/src/vs/workbench/contrib/chat/browser/aiCustomization/shunCodeBridgeStartupFailureCatalog.ts`，被 `edge-unreachable` 的 cause 引用）升级为详细排障版——说明边缘走 7844 端口（QUIC 用 UDP、HTTP/2 用 TCP）、仅自动路由模式且配置了可用 HTTP 代理时才会尝试 CONNECT 转发且代理须放行 7844、不保证绕过系统 TUN/fake-ip、若边缘域名解析到 198.18.0.0/15 或出现 TLS EOF 则核对 `argotunnel.com`·`trycloudflare.com` 的直连与真实 DNS 规则、或改用 ngrok，并强调「TCP 握手成功不等于 TLS/QUIC 成功、ShunCode 不改系统网络」。以 Windows 侧手写的权威版为准，逐字节精确同步到 macOS/Linux（此前 mac/linux 为较短版本），三端文案一致。
- **契约白名单补标准术语（三端测试）：** 三端 `tests/bridge-startup-failure.test.mjs` 的产品词白名单 `PRODUCT_WORDS` 精确追加 `argotunnel.com` 与 `QUIC / UDP / TCP / TLS / DNS / CONNECT / EOF`——它们与既有的 `HTTP / TUN / fake-ip / trycloudflare.com` 同类，是无法用中文替代的标准网络协议缩写与 Cloudflare 边缘域名，属逐词精确添加、不放宽整体正则，使详细文案里的这些术语不再被误判为「超出产品名的英文」。（三端白名单各自的平台差异保留：Windows 有 `pid / Winget / PowerShell / ngrok.yml`，macOS/Linux 有 `Homebrew`。）
- **network-failed 动作收敛（三端）：** 将 `network-failed` 的 actions 从 4 个（重试 · 打开代理设置 · 打开连接设置 · 查看日志）收敛为 3 个（重试 · 打开代理设置 · 查看日志），去掉「打开连接设置」以满足「每个失败码 1–3 个动作」的 UI 卡片契约上限；口径与 `edge-unreachable` 一致，也贴合该 cause 强调的「按日志分阶段 + 核对代理规则」。该第 4 个动作此前被 `edge-unreachable` 的失败挡在前面、未暴露。
- **carrier-layout 契约（macOS）：** 顺带修复 macOS 侧 `tests/carrier-layout.test.ts` 因终端 start-frame 重构遗留的 3 行过时断言（引用已删除的 `COMMAND_ECHO_TIMEOUT_MS` / `consumeCommandEcho`）；删除后以 `tsc -p tsconfig.json` 全量重建 `dist` 再跑 `node --test` 通过。Windows/Linux 未做该终端重构，其测试与源码本就一致，无需改动。
- **验证：** 三端 `node --test tests/bridge-startup-failure.test.mjs` 全绿——Windows 71/71、macOS 55/55、Linux 55/55（用例数差异属各端既有测试集不同）；macOS `carrier-layout` 1/1。全静态扫描确认整个 catalog 仅 `network-failed` 一处 action 超限，其余合规。测试经「运行时 `ts.transpileModule` 编译源 `.ts`」验证，正式打包会按各端流程重新编译产物，无需额外重建 dist。
- 本轮仅修改三端源码文案、契约测试白名单与本更新日志，**未执行新的正式打包、签名、公证或安装**；既有安装包/已安装应用不会被自动改写，须重新打包并安装后生效。

### 2026-09-29 Cloudflare 隧道「连不上 Cloudflare 网络」启动失败提示文案修正（三端）

- **背景（真实案例根因）：** 某 mac 用户第二个窗口选 Cloudflare Quick Tunnel 启动 Bridge 时报「连不上 Cloudflare 网络」。排查确认二进制与路径均正常（`cloudflared version 2026.8.2` 可手动运行、app 内那份也可运行、bundle 已签名），真因是**本机 Shadowrocket 的 TUN + fake-ip 全局劫持**：`argotunnel.com` 被解析成 `198.18.0.x`（fake-ip 段），去真实边缘 IP 的路由也落进 `utun4`，机场节点放行 TCP 握手却不完整转发 QUIC/TLS，导致 `TLS handshake with edge error: EOF`、QUIC 被 `Application error 0x0 (remote)` 重置，隧道反复重试后被关闭。同机另一窗口的 ngrok 传输始终正常，佐证是「专门去 Cloudflare 的路被代理破坏」，而非机器或二进制问题。
- **问题所在：** 原提示 `PROXY_TUN_ZH/EN` 与 `HEALTH_PROXY_ZH/EN`（`vscode-main/src/vs/workbench/contrib/chat/browser/aiCustomization/shunCodeBridgeStartupFailureCatalog.ts`）**无条件建议「开启全局(TUN)模式」**。但对 fake-ip 型 TUN（Shadowrocket/Clash 等)，开全局 TUN 恰恰是致病根源，照做只会更糟——排障方向是错的。
- **修正内容（三端一致）：** 重写这两组文案：保留「允许 7844 端口」这一正确建议；去掉「无脑开全局 TUN」；补充说明 fake-ip/全局(TUN)可能把 `argotunnel.com` 解析成虚拟地址、且不转发 7844/UDP 而导致失败，并引导正确解法——把 `*.argotunnel.com`、`*.trycloudflare.com` 设为**直连(DIRECT)**、或暂停代理后重试、仍不行改用 **ngrok**。口径与同文件已写对的 `quick-tunnel-api-failed` 条目一致；错误弹窗的按钮/actions（重试启动 · 打开代理设置 · 查看日志）不变。
- **验证：** 三端各命中 1 处新文案、旧的三种「开启全局(TUN)…重试」措辞全部为 0；补丁对每个目标串断言唯一匹配并做「无残留旧建议」护栏。纯字符串字面量替换，类型/结构不变，未跑 vscode-main 全量类型检查。
- **说明（本项非连接修复）：** 该文案改动只让提示更准确，**不改变连接能力**；真正的连接根因在用户侧代理配置（TUN 是内核网络层全局劫持，应用层无法绕过；且 Shadowrocket 容器受 macOS 沙盒保护，命令行无权读写其配置），需用户在代理软件里为上述域名加直连或暂停代理，或改用 ngrok 传输。本轮仅改源码文案与本日志，未执行新的打包、签名或安装。

### 2026-09-28 Windows 托管 Bash 实测修复与日志同步

- **`read -t` 输入修复（Windows）：** 在创建私有 PTY 时，仅关闭额外的 canonical 行缓冲（`stty -icanon min 1 time 0`），保留信号与回显；随后 `exec` 同一份产品自带 Bash。此一次性启动引导不包装或重复执行用户命令，不切换 PowerShell，也不加载用户的 Shell profile／rc 脚本。
- **管道退出码修复（Windows）：** 不再通过 Windows 的 `PROMPT_COMMAND` 采集动态状态；改在父 Shell 的 PS1 阶段、cwd 编码子命令之前取得真实状态快照。`pipeline_exit_codes` 以严格数据格式解析，拒绝含糊或部分损坏的数组，绝不 `eval` 返回内容。POSIX 旧协议及 Bash 3.2 路径保持兼容。
- **迟到输入保护：** 终端已到达结束提示符、处于输出排空或恢复阶段，以及命令已失去终端占用时，拒绝 `send_command_input`，不再先返回成功或将输入交给下一条 Shell 命令。
- **交互输出兼容：** 对 stdin 输入不再使用“丢弃输出直到找到输入回显”的策略，保留不回显程序（例如 Node readline）的正常响应；真实终端的输入回显可能保留。`run_command` 的命令回显过滤保持原规则。
- **日志同步与防回退：** 合并最新 read_image／任务卡／PTY 更新段落，默认复制提示词与真实 Workbench 一致；相同 `0.7.7` 版本号及同一更新日期不能掩盖缺失的最新修复段落。发布时仍按当前规范源日志校验随包 `SHUNCODE_CHANGELOG.md` 的内容摘要。
- **新增验证与发布门禁：** 协议解析、输入边界回归加入共同必跑入口；Windows 正式发布在准备已锁定的开发 Electron 后必跑真实 PortableGit／ConPTY 回归，`-SkipTests` 不能绕过。该测试加载当前源码，不同步 Carrier，不改写已安装应用。
- **生效边界：** 本次修改当前 Windows 工作区源码、测试和发布脚本，未执行正式打包、签名或覆盖安装；当前运行实例、既有安装包及已安装的随包日志不会被热改写。须使用正式入口重新构建、退出应用并安装后生效，不据此宣称完成 macOS／Linux 原生主机、真实账号或 GUI 全量验收。

### 2026-09-28 macOS/Linux 终端输入与完成帧边界修复

- **实测结论：** macOS arm64 的 `/bin/bash` 3.2 与 Linux x64 的 Bash 5.2 均未复现 Windows 交互 Shell 的管道空数组及 `read -t` 提前失败；两端原有短行 base64 命令帧继续保留，不移植 Windows 的 `stty -icanon`、PS1 或 Bash 4+ 专用语法。
- **共有输出缺陷：** 无回显程序确实收到输入，但“丢弃直到找到回显”的过滤会吞掉正常响应。两端移除该输入过滤，保留真实 PTY 输出；输入本身若由终端回显，可能出现在结果中。命令帧的开始标记仍用于隔离传输回显。
- **迟到输入保护：** 命令尚未启动、已进入完成／排空阶段、正在接收完成标记，或已失去终端占用时，`send_command_input` 明确返回 `COMMAND_NOT_ACCEPTING_INPUT`，不虚报已投递。
- **完成帧完整性：** 开始／完成帧的序号须精确匹配；过期、未来或带尾部垃圾的标记不能结束当前命令或改写 cwd。管道数组拒绝 `1oops`、小数、负值、越界值等损坏字段，不再把它们截断成看似正常的整数。
- **回归与发布保护：** `managed-posix-boundaries.test.mjs`、`managed-posix-pty.test.mjs` 和发布接线测试加入共同必跑清单；入包源码映射检查补齐实际使用的终端、管理器与协议模块，避免只检查薄 broker 却漏掉旧实现。原生测试使用各自主机现有 Node、node-pty 与 `/bin/bash`，不下载依赖、不触碰运行中应用。
- **日志一致性：** 同步已有 read_image／任务卡更新记录及真实默认连接提示词，新增同版本、同日期旧日志拦截。历史测试数字仍归属各自小节，不冒充本次实跑或相加去重。
- **生效边界：** 本次为 Mac 与 Linux 权威源码、测试、文档和发布门禁修复；未执行正式打包、签名、公证、系统安装或重启 Bridge。现有安装与随包日志不会被热改写，须经各平台正式入口重新构建并安装后生效。

### 2026-09-28 内置 uv/uvx 运行时（`uvx` 型 stdio MCP 开箱即用）与 Git 缺失弹窗默认关闭（三端）

- **捆绑便携 uv/uvx 运行时（三端）：** 将 `uv`/`uvx`（Windows 为 `uv.exe`/`uvx.exe`）随扩展打包到 `extensions/shuncode/runtime/bin/`（与既有 `rg`、`cloudflared` 同处），发布安装包整目录携带。用户无需自行安装 uv 或配置 PATH，即可运行 `uvx` 型本地 stdio MCP 服务器（例如 Blender 的 `uvx mcp-for-blender`）。注意：MCP 传输仍是既有三种之一（stdio / Streamable HTTP / SSE）中的 **stdio**，本项不新增传输模式，仅消除“装 uv + 配 PATH”这一前置摩擦；首次运行 `uvx <包>` 仍需联网拉取 Python 与依赖，Blender 本体与其 addon 仍需用户自行安装。
- **受管 stdio 环境 PATH 注入（三端）：** `external-mcp-stdio-env.ts` 的 `managedStdioEnvironment` 新增可选 `bundledBinDir`，把 `runtime/bin` **追加**到受管 stdio 环境的 `PATH`（POSIX 用 `:`，Windows 用 `;` 且写入规范化的 `PATH` 键）；采用“追加而非前置”，用户自身 PATH 上的同名工具仍优先，用户在服务器配置里显式提供的 env 仍最高优先。链路：`bridge-server.ts`（注入 `context.asAbsolutePath("runtime/bin")`）→ `external-mcp-registry.ts`（`Deps.bundledBinDir` 透传）→ `external-mcp-connection.ts`（spawn 时带入）。仅影响 ShunCode 自身的受管 stdio 环境，不做全局安装、不修改系统 PATH，不被其它软件借用。
- **Git “找不到 Git” 弹窗默认关闭（三端）：** 内置 Git 扩展的 `git.ignoreMissingGitWarning` 默认值由 `false` 改为 `true`（`vscode-main/extensions/git/package.json`）——装了 Git 正常使用（想用就用），未装也不再弹窗打扰（想不用不打扰）；需要该提醒者可在设置里改回 `false`。`git.enabled` 不变；macOS 系统级“命令行开发者工具”对话框由 `findGitDarwin` 既有逻辑（`/usr/bin/git` + `xcode-select -p` 失败即拒绝、不触发系统安装）规避，无需改动。
- **打包脚本对应更新（三端）：** 扩展打包脚本新增 uv/uvx 捆绑步骤——macOS `scripts/release/macos/build-vscode-extension.mjs`、Linux `scripts/linux/build-extension.mjs`、Windows `scripts/build-vscode-extension.mjs`；从候选目录（`SHUNCODE_UV_DIR`，及 `~/.local/bin` 或 `%USERPROFILE%\.local\bin`、Homebrew、WinGet Links、`/usr/local/bin` 等）拷入 `runtime/bin`（Linux 附 ELF 校验、Windows 用 `.exe` 名），该初版实现对缺失采用非致命处理；2026-09-29 已改为正式构建强制成对校验，开发模式边界见上方最新小节。外层安装包（DMG / deb / Windows）沿用对整个 `runtime` 目录的整体拷贝，故 uv/uvx 与既有 rg/cloudflared 一同自动随包，无需额外改动；扩展无 `.vscodeignore` 剔除二进制，`verify-copilot-runtime` 等门禁仅校验 Copilot 的 ASAR `runtime.node`、不列举 `runtime/bin`，加入 uv 不会中断现有发布门禁。
- **测试与实跑（三端）：** `tests/external-mcp-network` 新增 PATH 追加行为用例，并更新“单一环境函数”契约断言以匹配新的 `managedStdioEnvironment(config.env, process.env, process.platform, bundledBinDir)` 调用——macOS 8/8、Windows 8/8 通过（Linux 该树无此测试文件，扩展 `tsc` 通过）；构建校验 macOS `build:dev`、Windows 扩展 `tsc`、Linux 扩展 `tsc` 均 EXIT=0；三端均已实际把 uv 拷入 `runtime/bin` 并运行内置二进制（`uv/uvx 0.12.19`，Windows 为 `x86_64-pc-windows-msvc`）。
- 本轮仅修改三端源码、打包脚本、测试与本更新日志并执行隔离回归，**未执行新的正式打包、签名、公证或安装**；既有安装包/已安装应用不会被自动改写，须重新打包并安装后生效。

### 2026-09-28 read_image 路径修复、任务卡强制提示词与 Windows 输出/构建优化

- **read_image 路径解析修复（三端）：** 修复 `read_image` 在传入相对路径或工作区根相对路径时误报 `FILE_NOT_FOUND` 的问题；改为复用与其它工具一致的 `workspace-paths` 解析逻辑（相对路径、工作区根、绝对路径统一处理），使浏览器/独立 MCP runtime/原生三种运行时下的读取契约一致。
- **三层提示词新增“任务卡工具”章节并强制每次对话建/更新任务卡（三端）：** Bridge instructions（Layer 1）、原生工具 `modelDescription`（Layer 2）与默认复制提示词（Layer 3）统一加入任务卡工具（`set_todos` / `update_plan` / `report_progress`）说明，并要求**每次对话开始（含纯连接消息）都先建立或更新任务卡**再执行操作。同步调整“连接只读”冲突文案，避免“连接后只读、等待任务”与“先建任务卡”相互矛盾。
- **Windows 长命令输出/构建优化：**
  - **PTY 输出时序修复：** 命令结束后先 `drainPendingOutput` 将待处理的终端输出排空（受安静间隔与上限约束）再做结果快照，避免上一条命令的尾部输出被延迟计入下一条命令的采集窗口，减少“长命令易丢输出”。
  - **低风险构建加速：** 拆分测试 `tsconfig` / 让主 `tsconfig` 排除测试目录，缩小增量编译范围，不改变产物与安全边界。
- **对应发布门禁新增（三端）：** `scripts/verify-release-repairs.mjs` 新增 `verifyTaskCardPrompt`，直接读取入包的扩展 bundle 与 Workbench bundle，断言 Layer 1 的 `START of EVERY conversation` 与 Layer 3 的 `每次对话先建/更新任务卡` 两个抗压缩字符串标记存在（用固定字符串而非可能被压缩改名的标识符）。三端发布接线测试 `release-script-gates-077` 追加对应断言；Windows 另加源级门禁，断言 `ide-tool-broker.ts` 中 `drainPendingOutput` 已接入并在结果快照之前执行。
- **本轮实跑（隔离测试）：** 发布门禁 + 修复回归（`release-script-gates-077` 与 `release-repairs`）——macOS 35、Linux 27、Windows 31 项全部通过。
- 本轮仅修改三端源码、打包校验脚本与本更新日志并执行隔离回归，**未执行新的正式打包、签名、公证或安装**；既有安装包/已安装应用不会被自动改写，须重新打包并安装后生效。

### 2026-09-28 三端正式打包脚本复核

- Windows、macOS、Linux 的正式构建链均显式加入 UI 发布前置检查，在依赖安装/编译之前拦截旧 Skill 界面、原生 Agents 窗口误启用、回归文件缺失或未加入必跑入口等问题。
- 三端共同入口必跑真实源页面的布局、点击、作用域筛选、HTTP 自动/严格/SSE、菜单可见性与原生窗口禁用回归；新增发布接线测试，拒绝前置检查漏接、重复、放在依赖安装后，或仅在注释里出现。
- 最终发布检查继续核对主进程当前源码映射及入包字节、Skill 管理 JS/CSS 标记、产品能力标记和 Copilot 缺席；不会因修改更新日志而改写旧安装包。
- Windows 保留 UTF-8 BOM、全部强制检查和原生 Electron 回归前置准备；macOS 保留唯一 DMG 入口、停止/签名/安装回滚保护；Linux 保留源码指纹、共用锁、普通用户构建及按构建记录覆盖安装。
- 本轮仅更新并核验三端打包脚本、源码与隔离测试，未执行新的正式打包、签名、公证或安装。既有安装包须重打后才包含新 UI。
- 本轮实跑：三端发布接线与日志回归各 22 项通过；共同入口 Windows 255、macOS 247、Linux 247 项通过；平台定向回归 Windows 60、macOS 79、Linux Python 打包/安装夹具 107 项通过。各组可能重复覆盖，不相加作为去重总数。

### 2026-09-28 Skill 管理与原生 Agents 入口修复

- Skill 列表默认显示全部来源，明确显示“当前显示 / 总数、全局 / 当前项目”计数，避免标签显示有技能却被默认全局筛选隐藏；导入目标仍为全局库，不移动已有项目 Skill。
- 现有 Skill 的列表和管理操作放在导入区之前；“删除”作为行内一级操作，未加载或非当前生效副本显示禁用原因，不再无提示地消失。实际删除仍须本机确认，不在本次修复中删除用户 Skill。
- 更多菜单根据页面/视口空间向上或向下展开、约束高度和宽度，支持 Escape 关闭并返回焦点，改善窄窗口/高缩放下菜单被裁切的问题。
- 关闭原生 Agents 会话窗口模式：顶部“在智能体中打开”、相关菜单/命令/快捷键及引导入口不再注册；主进程阻止旧入口重新打开该模式，恢复窗口时跳过其专用工作区，保留历史会话数据。后台 Agent Host、Bridge、MCP、Skill 和浏览器不随之移除。
- Mac 表单回归更新为当前 `transport: 'auto'` 契约，并覆盖自动、严格 Streamable HTTP、SSE；不通过删除自动协商字段来迎合旧测试。真实源页面的浏览器交互回归加入三端共同发布门禁。
- 本轮更新三端源码、打包校验与日志；既有安装包/已安装应用不会被自动改写，需要重新打包并安装后生效。

### 2026-09-28 打包与覆盖安装摘要

- Linux 的正式 DEB 入口新增 `--install`：先成功打包，再由现有安装器按本次构建记录校验并安装，整条链路复用同一锁；不会按文件名或修改时间猜包。
- `--yes` 仅在与 `--install` 配合时生效；构建仍以普通用户运行，只有 apt 安装阶段使用 sudo。运行中的应用、摘要不一致、旧构建记录及未请求的降级仍会阻止安装。
- macOS 保留唯一 DMG 入口与 `SHUNCODE_OVERWRITE_INSTALL=1`；使用唯一空白暂存目录，不复用旧 `.new`，覆盖切换前再次检查应用已退出。
- Mac 不再先删除旧 App：旧 App 先移入备份，新 App 切换失败会恢复；回滚受阻时保留备份并报告路径。
- 两端继续强制校验去 Copilot 策略、MCP/Skill、版本与随包 `SHUNCODE_CHANGELOG.md`；本轮仅修改脚本和执行隔离回归，不自动安装。

### 2026-09-27 增量摘要

- 外部 MCP 导入兼容常见配置别名、忽略字段预览、Bearer 与额外请求头，并补齐描述、cwd、超时设置。
- 新增/补齐远程 HTTP 自动协商、严格 Streamable HTTP、SSE，以及 OAuth 浏览器授权、共享安全存储、刷新与退出授权。
- 16 个内置 MCP 工具的输入/输出 schema 与安全标记三端对齐；目录、命令控制、任务/进度返回更准确。
- 独立终端三端均为 Bash；统一 MCP instructions、原生工具 `modelDescription` 与默认复制提示词，保留 Mac Bash 3.2 兼容约束。
- ngrok 域名占用不再盲目重试或默认归因于本机残留进程；提示检查控制台端点/流量策略，避免把无关端点当成可自动清理对象。
- 发布检查覆盖主扩展、Skill 解压 worker、独立 MCP runtime、原生启动层、Workbench 与模型提示词，拒绝遗漏、旧副本和旧日志混入。

---

## 一、Skill：全局 Skill 库与独立 Skill 页

### 1. 全局 Skill 库

- **导入一次，处处可读：** 同一台电脑、同一用户的所有工作区和空窗口都能发现和管理。Skill 是说明文件夹，Agent 通过 `list_skills` 获取目录与 `skillFile` 路径后读取，不会把每个 Skill 注册成同名 MCP 工具。
- **默认位置：** 安装目录旁。
  - Windows：`C:\Program Files\ShunCode-Skills\<用户标识>`
  - macOS：`/Applications/ShunCode-Skills/<用户标识>`
  - Linux：按实际应用安装位置计算，AppImage 场景按 AppImage 所在目录计算；以 Skill 页显示的位置为准，可通过「更改位置…」选择可写目录。
- **更改位置：** 可在 Skill 页「更改位置…」。位置不可写时会明确提示另选：不提权，也不会悄悄放进当前项目。
- **变化同步：** 已打开的 Skill 页每 2 秒检查一次变化，其他窗口导入的 Skill 会自动出现。

### 2. 独立的「Skill 与 MCP」页

- 位于 Bridge 下方，也可以在命令面板搜索「ShunCode: 技能中心」打开。页面分为「Skill」和「MCP 服务」两个标签，分别显示数量；有待处理 MCP 状态时显示提示点。
- **基本功能：**
  - 搜索（名称、描述、路径）、刷新、查看 SKILL.md、打开目录、启停、删除；
  - 列表区分「全局」和「当前项目」；
  - 没通过加载检查的 Skill 会标出原因和处理指引。
- **原来 Bridge 页「高级功能」里的 Skill 管理全部移到这里：**
  - 「加载 Skill」开关，并注明它写进哪一层设置（项目里单独设置过，就只改这个项目）；
  - 「诊断全部」和诊断汇总，以及每一行的「诊断」「重建入口」；
  - 批量生成入口：先确认，逐条显示结果，一条失败不影响其他条目；
  - 「现有自定义工具（JSON）」区：`.shuncode/mcp-tools` 下的 JSON 工具可以刷新、启停、删除，和 Skill 分开管理。
- **修复（macOS）：** 页面内容较多时现在可以上下滚动（Windows 原本正常）。

### 3. 文件夹与压缩包导入

- **普通入口选择文件夹：** 所选根目录必须有唯一的 `skill.md` 普通文件，`SKILL.md`、混合大小写均识别；不能用外层父目录或直接选择 Markdown 文件代替。
- **名称与标题：** 保留所选文件夹名称，支持中文、空格及大小写；不以临时 `input` 或 frontmatter 标题覆盖。frontmatter 和长描述不是文件夹导入的必填条件。
- **压缩包单独入口：** 支持 `.zip`、`.tar.gz`、`.tgz`，也可拖入；不支持 `.skill`、7z、RAR 或 ZIP64。
- **后台解压：** 在单独的后台线程里解压，界面不卡顿。
- **容量规则：** 不再设置旧的 Skill 数量、32 MB/128 MB、2000 条目产品限额；仍受内存解码器、ZIP 格式及操作系统路径等技术边界限制。压缩包一次读入上限约 2 GiB；更大资源库应解压后导入文件夹。
- **拒绝不安全的内容：** 路径穿越、符号链接/特殊文件、含糊根目录、异常压缩比、损坏或加密档案等；不以“无限制”绕过安全和系统路径限制。
- **中途失败不留残缺：** 导入按事务提交，中途失败或退出不会留下残缺的 Skill，下次启动会自动清理。
  - 替换失败时保留旧版本；
  - 文件被占用时（Windows）给出中文的下一步说明。
- **同名 Skill：** 先确认，再替换。
- **目录适配：** 点击、拖入或“复制到全局”走同一预览与事务流程；原文件夹不改动。macOS 保留执行位、行尾/shebang 与 Finder 杂项修复；Linux 保留对应 POSIX 脚本处理；Windows 保留占用重试和路径长度提示。
- **元数据兼容：** 三端统一识别带 UTF-8 BOM、CRLF 行尾的 Skill frontmatter，避免合法名称或描述因文件开头/换行格式而丢失。

### 4. 跟随工作区

- 切换或增删文件夹后，Skill 列表立即更新；旧请求晚到，也不会覆盖新列表。
- 空窗口可照常发现和读取全局 Skill；执行命令仍受工作范围、信任和权限规则约束，Skill 本身不是可调用 MCP 工具。

---

## 二、外部 MCP 服务（三端）

### 1. 在 Skill 页统一管理

- 在「Skill 与 MCP」页的「MCP 服务」标签添加外部 MCP，一份配置可分别启用编辑器与远程 Bridge；历史原生服务仍需显式授权开放给 Bridge。
- **「添加 MCP 服务」表单：** 支持名称、描述、远程地址或本地 stdio 启动命令/参数，以及适用的工作目录、环境变量、超时与连接选项。
- **远程传输：** 可选择自动协商、严格 Streamable HTTP 或 SSE；本地服务继续使用 stdio。
- **鉴权与请求头分开配置：** 无鉴权、Bearer 或 OAuth 与额外自定义请求头按规则组合；Bearer 不再排斥项目标识等非鉴权 headers，OAuth 的 Authorization 由授权流程管理。
- **「导入 JSON」：** 接受 URL、启动命令、`mcpServers` / `servers` / 单项 JSON，先预览、勾选，再确认。
  - 识别 `streamableHttp`、SSE、remote 等常见配置写法及兼容别名；
  - 兼容描述、cwd、timeout 等设置，进行规范化与校验；
  - 可用配置中的未知字段不再一概导致导入失败，预览列出忽略的字段名，不回显其敏感值；
  - 仍拒绝互相冲突、格式错误或不安全的配置，不把“宽容导入”当作绕过校验。
- **服务列表：**
  - 分别显示编辑器和 Bridge 的连接状态，包括连接中、运行中、需要设置密钥、需要授权、代理错误等；
  - 可启停两端、单独启停编辑器/Bridge、重试、移除，删除仍需确认；
  - 修复 Skill 操作后 MCP 菜单遗留禁用状态，使用独立的**行级忙碌**状态；一行等待不冻结其他行，成功、取消和错误均有反馈；
  - 关闭的菜单显式隐藏；非交互内容面板不整块聚焦，保留真实控件的键盘操作。
- **已有原生服务：** 同页列出，默认不开放给 Bridge；通过「开放已有服务给 Bridge…」显式复制，原配置不变，敏感值只显示打码视图。
- **工具命名：** Bridge 以 `ext__<服务>__<工具>` 提供上游工具，保留文本、图片等内容和结构化输出；Skill 仅提供说明文件路径，不注册同名工具。

### 2. OAuth 与密钥

- **OAuth 浏览器授权：** 支持受保护资源/授权服务器发现、PKCE S256 与 state 校验；按服务支持情况使用动态客户端注册，或填写 Client ID / Client Secret。
- **共享凭据：** 原生编辑器和 Bridge 的 HTTP/SSE 连接共用同一受控授权记录，不各自维护互不一致的登录状态。
- **后台刷新：** 使用已有客户端、发行者和资源绑定信息刷新；正常后台刷新不会偷偷打开浏览器或再次动态注册。不能刷新时给出重新授权提示。
- **退出授权：** 清除本地 OAuth 凭据并使旧引用失效；不把本地退出等同于已经撤销供应商侧的所有授权。
- **信任与身份边界：** 保留工作区信任、配置代次及发行者/资源校验；凭据变更、禁用和退出后不能继续沿用失效的授权引用。
- **敏感值安全存储：** 令牌、敏感请求头、环境变量值及适用的 OAuth 凭据进入 SecretStorage / 系统凭据存储，应用维护的 `~/.shuncode/external-mcp.json` 保存引用，而非明文凭据。
- 导入预览、普通 UI 和应用错误信息不主动回显凭据；OAuth 原始错误响应体不直接显示。
- **「密钥…」菜单：** 设置或清除单项、清除全部、同源替换地址或参数；修改后连接使用新凭据。OAuth 授权与退出使用独立动作，不允许手工 Authorization 覆盖授权流程。

### 3. 网络与启动

- **代理可按服务设置：** 继承 ShunCode 的代理设置 / 不用代理 / 自定义代理；敏感代理配置按安全引用保存。
- **本地命令服务：** 使用精简的基础环境变量，再加服务自己的环境/代理配置；三端托管进程的显式 PATH 覆盖行为对齐，非托管原生进程保留平台行为。
- **受控回退：** 自动模式只在兼容的初始 HTTP 4xx 响应上尝试旧 SSE；严格 HTTP 不回退。鉴权、限流、服务端错误，以及已成功 POST 后的 `endpoint` 事件，均不能被用来盲目重发请求。
- 修改 `http.proxy`、`http.noProxy` 等配置后，相关连接按策略重连；「网络诊断」不携带服务凭据，只展示必要的协议、主机与诊断结果。
- 窗口恢复后可启动已启用、受信任且允许自启动的服务，包括缓存及晚注册服务；尊重 Never/访问策略、禁用与手动停止，不因此自动启动公开 Bridge。

### 4. 支持范围与限制

- 支持 **stdio、Streamable HTTP、SSE 与 OAuth**；OAuth 用于适用的远程 HTTP/SSE 服务，不是任意 stdio 程序的登录适配器。
- 非回环地址须使用 HTTPS；重定向、同源与凭据边界继续校验。
- 不转发上游的 resources / prompts；OAuth 的实际兼容性仍取决于供应商的发现信息、客户端注册与授权实现。
- 既有变更完成源码、模拟服务与 SDK 回归，**未进行真实供应商账号授权或已安装编辑器的完整端到端验收**。

---

## 三、Bridge

### 1. 启动失败的中文提示

- **醒目的失败卡片：** 启动按钮附近会显示「Bridge 启动失败」，用中文说明原因，并给出下一步按钮，例如重试、安装 cloudflared、打开连接设置、代理设置、查看日志。
- **覆盖的常见情况：** 授权、登录、设备数量、端口占用、隧道工具缺失、ngrok 和命名隧道配置、Cloudflare 网络等。
- **技术详情：** 默认折叠，并隐藏令牌、密码等敏感内容。
- **其他出口一致：** 自动启动失败的通知、Chat 侧的状态也改用同一套中文说明；通知里可以直接「打开 Bridge 页面」。
- **说明停止原因：** 因授权续期失败或权限失效而停止时，页面会写明原因。

### 2. 页面精简

- 「高级功能」里的「自定义工具 / Skills」整块移到 Skill 页（见第一节）；「已公开的工具」保留为只读。Bridge 页的其他功能、位置和默认值不变。

### 3. 工作范围

- **修复：** 项目里有单独设置时，切换「工作范围」不起作用。现在写入当前生效的那一层，并提示项目覆盖。

### 4. 内置 MCP 返回与命令作用域

- **17 个内置工具**（含 2026-09-29 新增的 `configure_mcp`）的输入/输出 schema 与安全标记三端对齐，并加入统一快照；说明性文字可保留真实平台差异，不忽略真实数据字段的约束。
- 目录结构化结果直接使用实际条目，不从展示文本反推；保留 Unicode、换行文件名、路径和截断状态。
- 对齐运行、轮询、输入、取消的结构化返回，保留取消过程中的风险和强制终止状态，不把正常取消误报为协议错误。
- 2025 连接保留原 MCP 会话作用域；2026 无状态请求使用稳定的凭据/客户端/工作区命令身份。同一身份可在后续请求继续轮询、输入或取消，改变身份不能借用命令句柄。
- `timeout_ms` 返回 `running` 不等于命令被终止，也不应据此盲目重跑；继续使用原 `command_id` 与 `next_offset`。
- 任务/计划/进度回执满足声明的结构，保留 `0%` 等合法值；任务作用域与命令作用域分别维护。
- Skill provider 不可用时明确报错，不能伪装成“没有安装 Skill”；成功的空目录或禁用状态仍是合法结果。

### 5. ngrok 域名占用与错误展示

- `ERR_NGROK_334` 表示所用域名已有在线端点占用，可能来自其他代理会话、设备或云端端点/流量策略，**并不证明本机有可自动清理的残留进程**。
- Windows 不再针对域名占用盲目进行多轮启动重试；三端恢复流程遇到确定的占用、授权、配置或策略错误时停止无效重试，并退出一直显示“启动中”的状态。可恢复的网络故障保留其重试策略。
- 状态卡显示短原因与可操作入口：查看 ngrok 控制台、修改连接域名、选择快速隧道；不再把清理本机进程作为域名占用的默认处理动作。
- 主错误文本不再塞入整段 ngrok 启动 JSON、配置路径或凭据；原始诊断保留在相应日志中。
- 补充旧 AI Gateway / `ai-gateway` 流量策略退役（`ERR_NGROK_3820`）的诊断指引。此类云端配置问题不能靠切换 Bash 或反复本地重试解决。
- 不自动停止无关端点、不改云端配置、不启用 pooling 将独立 MCP 工作区混用同一地址。刚关闭的端点可能需要时间释放，应确认占用者后手动重试，或为 MCP 使用账号允许的独立域名。

---

## 四、临时隧道（第一种 Bridge）

- **修复 404：** 电脑上已经有 `~/.cloudflared/config.yml`（例如给别的服务配过隧道）时，Bridge 地址的所有请求都返回 404。现在临时隧道始终使用 ShunCode 自己的配置文件。
- **修复（Windows）：** 临时隧道不再只走 QUIC（UDP 7844），UDP 被封时自动改用 HTTP/2。macOS 版本来就默认使用 HTTP/2。
- **新增：经代理连接 Cloudflare。** 7844 端口直连不通、但有 HTTP 代理时，ShunCode 会经代理连接 Cloudflare。
  - 新设置 `shuncode.bridge.cloudflareEdgeRoute`：
    - **自动（默认）：** 先直连；45 秒内没连上、并且配置了系统代理或 `http.proxy` 时，自动改走代理；
    - **直连 / 经代理：** 固定使用其中一种。
  - 不需要改 hosts，不需要管理员权限，也不用另外运行转发脚本；代理需要允许连接 7844 端口。
- 「连不上 Cloudflare 网络」的提示同步更新，并新增「代理设置」按钮。

---

## 五、账号、付款与授权

- **付款后授权恢复：**
  - 已登录的账户启动时会在后台刷新授权，即使本地没有保存订单记录；不会新建订单。
  - 查单确认已付款后，立即停止轮询、单独刷新授权，并提示不要重复付款。
  - 遇到网络失败、超时、限流、服务端错误，或付款后权益还没生效时，会有限次退避重试：最多 10 次，间隔最长 60 秒。签名无效、设备被拒等情况不会重试。
- **修复：「检查支付状态」看起来没反应。** 后台没有新订单时，现在也会给出明确结果，续费按钮随之恢复。
- **超时按场景设置：** 授权与支付请求的等待更短，例如授权刷新整轮 15 秒、套餐和订单查询 9 秒。显式配置的代理失败时，不会悄悄改成直连。
- **Windows/macOS 手动「刷新授权」：** 点击后立即显示进度，约 8 秒后给出慢连接提示；手动流程使用总计 20 秒、单次 8 秒预算，重复请求复用已有尝试，不延长正在运行的后台期限。完成或销毁后清理提示计时器。
- **Windows/macOS 本机时钟偏慢：** 已验签令牌的 `nbf`/`iat` 最多容忍 5 分钟未来时间；`exp`、授权到期、签名、发行者、受众、安装身份和能力范围仍严格校验。

---

## 六、其他改进与修复

- **修复：** 任务卡片上「进行中」标签多出一圈透明边框、掉到下一行。
- **（Windows）** 移除模型探针功能，保留 Arena 自动下滑。macOS 已在 0.7.6 移除。
- **（Windows 历史记录）** 此前曾保留内置 Agent Host 所需的 Copilot 原生运行时；该记录已被下文当前“移除 Copilot”发布策略取代。
- **Windows/macOS `NO_PROXY`：** 支持 IPv4 网段简写，例如 `169.254/16`、`10/8`；匹配不触发 DNS 查询，非法规则不会放宽为全匹配。
- **设置导航：** 暂时隐藏概述、智能体、技能、指令、提示、挂钩、插件入口，保留底层功能和旧入口回退；Bridge、Skill 与 MCP、API Provider、Codex、多模型博弈仍可用。
- **MCP 文件编辑：** `apply_patch` 新增/移动文件可创建工作区内缺失的父目录；完整预检、权限、版本与重叠路径锁检查后再写入，失败只清理本次创建且仍为空的目录。越界、链接和用户已有文件仍受保护；纯空目录可用已授权的 `run_command`。
- **三端独立终端均为 Bash：** Windows 不因宿主系统而默认采用 PowerShell/CMD，Mac 不因登录 Shell 而采用 zsh。保留平台路径与工具差异；Mac 按 Bash 3.2 编写兼容脚本，Windows 使用产品自带 PortableGit Bash。
- **三层提示词一致：** MCP initialize instructions、工具描述/原生 `modelDescription`、默认复制提示词使用同一 Bash 契约；移除“2026 后续请求不能轮询命令”的过时说明。PowerShell 仅在确需 Windows 原生设施或 `.ps1` 时显式作为子程序调用。
- **默认复制提示词已更新，自定义覆盖不变：** 地址仍来自运行中的 Bridge 状态，不从可编辑文本中读取。新默认正文为：

  > 快速连接这个 MCP（URL），先读工具规则；每次对话先建/更新任务卡，确认后等待任务。终端均为 Bash，勿按系统猜 Shell。

- **工具说明：** 精简重复说明，明确真实能力、错误恢复、任务状态及取消确认；不会因一次路径错误声称工具不支持目录。Mac bootstrap 同步新默认文案，兼容旧模板迁移，重放不会还原旧提示词。

---

## 七、安全

- **（Windows）依赖安全更新：**

  | 依赖 | 更新到 |
  | --- | --- |
  | `@anthropic-ai/sdk` | 0.91.1 |
  | Hono | 4.13.5 |
  | undici | 7.29.1 |
  | tar | 7.5.22 |
  | `ip-address` | 10.7.2（原 10.3.1；首个修复版本 10.5.1） |
  | uuid | 11.1.1 |
  | adm-zip（含 Copilot 内置副本） | 0.6.1 |

  以上为此前 Windows 依赖升级记录；其中 `ip-address` 已于 2026-09-29 提升到 10.7.2（见顶部同名小节），修复后 Windows 生产依赖审计实跑为 0 漏洞。各端按自己的锁文件、原生依赖及发布审计要求验证，不直接覆盖另一平台的依赖树。
- 压缩包保留解码技术边界、路径/链接和压缩炸弹保护（见第一节）；外部 MCP 敏感值只通过密码框和安全存储流转，不回显到普通 JSON 或日志（见第二节）。
- 三端正式发布链路接入共同 MCP/Skill/UI/SDK 契约回归与编译后的内置工具回归；Windows 的 `-SkipTests` 不能绕过对应强制门禁，Mac 保留其必跑合同检查与既有跳过限制。
- 通过源码映射和最终字节比对，校验主扩展、Skill 解压 worker、独立 MCP runtime、Workbench、原生启动及提示词相关输入。原生工具 manifest 声明也必须与当前源码一致。
- 生成的 Carrier 副本在真实构建/同步后验证；源码测试不为了通过检查而同步或改写已安装应用。
- **修复 Windows 打包预检误拒绝新源码：** 发布入口不再要求已经移除的 `NGROK_ENDPOINT_ONLINE_RETRY_BACKOFF_MS`，改为检查类型化 ngrok 错误、不可重试契约并禁止恢复旧的盲目重试。新增只读回归核对全部 129 条构建前源码字符串断言；不通过跳过测试或回滚 Carrier 同步来规避错误。

---

## 八、安装与升级

- **覆盖安装：** 安装前请完全退出 ShunCode。
- **需手动安装：** 自动更新不保证提示，请按平台手动使用本次 0.8.0 产物：Windows Setup、macOS DMG、Linux DEB 或 AppImage；打包命令本身不等于安装命令。
- **历史 Windows 安装包记录（不包含本轮源码修复，不作为新包校验值）：**
  - 约 319 MiB（0.7.6 为 241 MiB），主要因为保留了 Copilot 原生运行时；
  - 安装包未签名，请核对 SHA-256：
    - `ShunCode-0.7.7-win32-x64-Setup.exe`（2026-09-25 18:57 构建）
    - `EA96F77C76964150771641602EF0A5C090AECA1F5FB0ADD29A4A04C0E6ABFAB3`
- **2026-09-30 三端实际构建产物（本轮真实结果，取代上面的历史记录）：**
  | 平台 | 产物 | 大小 | SHA-256 | 签名 |
  | --- | --- | ---: | --- | --- |
  | Windows x64 | `release/ShunCode-0.8.0-win32-x64-Setup.exe` | 232,957,088 B（222.17 MiB） | `D98607795BED86778DD4F9112A84A70377B26ABF721B7D3F1AA7C1BDD8751228` | NotSigned |
  | macOS arm64 | `release/ShunCode-0.8.0-mac-arm64-AppleSilicon.dmg` | 193,041,408 B（184.1 MiB） | `20ff9d6405baf6d75230c1519466ccab06f2f587ed6c18523113384530e18f9b` | 本机身份签名，未公证 |
  | Linux x64 | `release/shuncode_0.8.0+code1.136.1-1_amd64.deb` | 185,903,832 B（177.3 MiB） | `3d3e19bb6afe41e5a92a74468412e5dbc17f1b3db165b36b891522fd5634f875` | 不适用 |
  每个产物的 `.sha256` 旁证文件与实际摘要已逐字符核对一致；Windows 另有 `ShunCode-0.8.0-win32-x64-build.json`（buildId `0f6cd481cdfe24d2`、carrierCommit `5c03086397d13f66eb8c46dc97043a91d9e97c9f`）。同名重打的包不能沿用以上摘要。
- **已完成的覆盖安装与安装版核验（Windows / macOS）：** Windows `C:\Program Files\ShunCode` 与 macOS `/Applications/ShunCode.app` 均已更新到 `shuncodeVersion 0.8.0`（Code-OSS 1.136.1），Windows 安装版的 buildId 与本次 `build.json` 一致。两端安装包内的 `workbench.desktop.main.js` 均命中 `shuncode-bridge-new-window-button`、`shuncodeBridge.cancelStart`、`localizeModels` 以及中文文案「新建窗口 / 取消启动 / API 供应商 / 正在测试连接」（压缩产物中为 `\uXXXX` 转义形式），随包 `SHUNCODE_CHANGELOG.md` 为本文件。Windows 安装版实跑 `run_command` 已不再出现 `Invalid run_command result envelope` 误报。Linux 也已用本次 DEB 覆盖安装：`dpkg -l` 显示 `shuncode 0.8.0+code1.136.1-1 amd64`，`/usr/share/shuncode/resources/app` 的 `shuncodeVersion` 为 0.8.0（Code-OSS 1.136.1），同一组页头、取消启动与 API 汉化标记全部命中，随包日志同为本文件。三端安装版至此一致。
- **升级后：**
  - 新设置 `shuncode.bridge.cloudflareEdgeRoute` 默认为“自动”；
  - 原有设置、账号授权、快速打开列表、自定义提示词和已导入的 Skill 全部保留；
  - 旧项目里的 Skill 仍在「当前项目」下可用，也可以用「导入全局」放进全局库。

---

## 九、平台差异与已知限制

### 三端已核验的共同范围

| 项目 | Windows | macOS | Linux |
| --- | --- | --- | --- |
| MCP/Skill 添加、导入与管理 UI | 已纳入共同回归 | 已纳入共同回归 | 已纳入共同回归 |
| HTTP/SSE/stdio 与 OAuth 源码契约 | 有 | 有 | 有 |
| 17 个内置工具 schema / 安全标记 | 对齐 | 对齐 | 对齐 |
| 独立执行终端 | PortableGit Bash | 系统 Bash 3.2 | 系统 Bash |
| 文件与原生适配 | Windows 路径、NTFS/占用保护 | POSIX/BSD 工具、钥匙串、签名保护 | Linux/POSIX 路径、发行版依赖与权限 |
| ngrok 占用/不可恢复错误处理 | 明确失败，不盲目重试 | 明确失败，不盲目重试 | 明确失败，不盲目重试 |
| 构建入口 | Windows 原生 PS1，BAT 转发 | 唯一 Mac DMG 入口 | Linux build / DEB / AppImage 链路 |
| 当前日志随应用分发 | `SHUNCODE_CHANGELOG.md` | `SHUNCODE_CHANGELOG.md` | `SHUNCODE_CHANGELOG.md` |

### 保留的 Windows/macOS 历史差异

| 项目 | Windows | macOS |
| --- | --- | --- |
| 临时隧道不再只走 QUIC | 本版修复 | 原本默认 HTTP/2 |
| Skill 页滚动 | 原本正常 | 本版修复 |
| 模型探针 | 本版移除 | 0.7.6 已移除 |
| 验签后 `nbf`/`iat` 5 分钟容差 | 有；不放宽到期 | 有；不放宽到期 |
| 手动刷新授权反馈、去重与 `NO_PROXY` 网段简写 | 按此前记录保留 | 按此前记录保留 |

Linux 本轮新增核验不等同于把上表中的计费、浏览器、依赖历史与完整安装行为全部重新验收一遍。

### 已知限制与验收边界

- 不支持 7z、RAR、`.skill` 或 ZIP64；压缩包仍受格式、安全及解码技术边界限制。
- 上游 resources / prompts 暂不转发；真实 OAuth 账号、供应商特性与已安装编辑器完整交互仍需发布后验收。
- 临时隧道申请地址这一步（`api.trycloudflare.com`）仍有 cloudflared 自身的代理能力限制；连接到 edge 的代理转发不等同于所有启动请求都经代理。
- 现有 Windows 运行实例曾出现较长命令空输出等待；本轮用短脚本入口完成源码回归，不把这一现象视为已安装版本的 PTY 端到端修复证明。
- 扩展回归中的既有平台/文件系统条件跳过仍保留；Windows 的分段 `tunnelRuntime` 关停结构断言未因本轮工作而变成已验证。共用功能门禁本身无跳过项。
- ngrok 域名、云端端点及流量策略须由账号所有者确认和处理；源码修复不自动解除既有域名占用。
- 各平台签名、Linux 包/运行时沙箱行为、实际安装与启动均按本次正式构建另行验收；对外分发必须发布本次实际产物的 SHA-256。

---

### 本轮预装组件精简：移除 Copilot

- 三端产品不再预装 Copilot CLI/SDK，并关闭对应的本地及远程原生 Copilot Agent 提供器。
- ShunCode 的 MCP、Skill、独立 Bash 终端及浏览器功能保留；不删除用户账户、聊天记录或自行安装的 Copilot。
- SDK/CLI 仅保留为构建依赖，用于兼容上游类型与测试，不进入发布运行时；共享协议辅助库不按名称误删。
- 发布流程检查生产依赖图、显式下载/复制路径，以及应用 ASAR 和解包目录中均无 Copilot 运行时；缺少策略标记或残留运行时都会阻断发布。
- 此次只重新生成 Windows 安装包；Mac/Linux 已同步源码与打包规则，须在各自原生主机重建后才形成不含 Copilot 的新产物。此前日志中的体积、哈希与构建记录仍是历史证据，不改写为本次结果。

---

## 十、三端发布流程与校验（入口对 0.8.0 生效；本节测试数、包体积与哈希为 0.7.7 历史证据）

### 1. 正式入口

- **Windows：** `PACKAGE_SHUNCODE_WINDOWS.bat` → `PACKAGE_SHUNCODE_WINDOWS.ps1`；在 Bash 中可显式调用 `powershell.exe -NoLogo -NoProfile -File ./PACKAGE_SHUNCODE_WINDOWS.ps1`。这是 PowerShell 打包脚本，不改变独立执行终端使用 Bash 的约定。
- **macOS：** `npm run package:mac:dmg` → `scripts/release/macos/package-mac-dmg.sh`；保留唯一入口、停止探针、原生组件、稳定身份签名、可选公证和最终只读 DMG 校验。不新增平行入口。
- **Linux：** `scripts/linux/build-linux.sh` 构建、验证并封存基础应用；`scripts/linux/build-deb.sh`、`scripts/linux/build-appimage.sh` 共用锁和 source-state，发现产物缺失或过期时重建，`--rebuild` 显式强制重建。按主机选择支持的架构，不在 Mac 仓库里冒充 Linux 原生构建。
- 正式构建请先保存工作并退出相关 ShunCode 实例，再在**应用外的系统终端**执行。默认不跳过测试、不自动结束占用进程、不自动安装；不要绕过停止、源状态、签名或依赖保护。

### 2. 发布物与日志

- 三端使用相同的规范日志：自 0.8.0 起为 `docs/mcp-changelog-0.8.0-2026-09-30.md`（`scripts/verify-release-notes.mjs` 的 `RELEASE_NOTES_SOURCE` 已指向该文件）；`docs/mcp-changelog-0.7.7-2026-09-25.md` 保留为历史文件。文件名保留首次记录日期，正文的“最后更新”日期为准。
- 真实应用组装后，将当前日志写入应用资源目录的 `SHUNCODE_CHANGELOG.md`，按版本与 SHA-256 验证；缺失或旧日志不能通过发布检查。
- Windows 在生成 Setup 前检查；Mac 在签名前、最终只读挂载 DMG 与可选安装中转副本检查；Linux 在 source-state 封存与归档前写入和检查，DEB/AppImage 复用该已验证基础应用，日志源文件也进入输入指纹。
- 保留 Mac DMG-only（加 `.sha256`）规则；各端外部产物命名、同版本归档/冲突处理与原有发布保护不变。

### 3. 本轮已完成的验证

| 验证 | Linux | macOS | Windows |
| --- | ---: | ---: | ---: |
| 共同 MCP / Skill / UI / SDK / 发布契约回归 | 247 通过 | 247 通过 | 255 通过 |
| 上述共同入口失败 / 跳过 | 0 / 0 | 0 / 0 | 0 / 0 |
| 无 Copilot 产品策略、入口与包内缺席回归（包含在共同入口中） | 20 通过 | 20 通过 | 20 通过 |
| 根项目、扩展、Carrier 类型检查 | 通过 | 通过 | 通过 |
| 相关提示词、ngrok 与发布源专项 | 通过 | 通过 | 通过 |

这些结果来自此前的源码与隔离测试验证，不是本次更新日志整理时重新打包的结果。共同入口按当前 `scripts/test-mcp-skill-parity.mjs` 的显式清单执行，编译后内置工具采用独立清单；分组之间存在测试复用，不能直接相加当作去重总数。

新增日志校验使用临时目录验证规范产物路径、版本、内容摘要、旧文案与旧副本拒绝，不能借此宣称已完成真实安装、GUI、账号连接或签名/公证验收。


### 4. 原生系统终端的一行执行命令

先保存工作并完全退出 ShunCode，在应用外的系统终端运行。以下示例统一使用 Bash 语法；MCP 独立终端三端均为 Bash 的契约不变。

**Mac：只打包，不覆盖安装**

```bash
cd "/Volumes/Shcode/Shuncode" && SHUNCODE_OVERWRITE_INSTALL=0 npm run package:mac:dmg
```

**Mac：打包通过后覆盖安装到 `/Applications/ShunCode.app`，随后按原规则重开应用**

```bash
cd "/Volumes/Shcode/Shuncode" && SHUNCODE_OVERWRITE_INSTALL=1 npm run package:mac:dmg
```

**Linux：只生成当前源码的 DEB**

```bash
cd "/home/robertslee/Shuncode-Linux" && bash scripts/linux/build-deb.sh --rebuild
```

**Linux：打包成功后覆盖安装本次 DEB**

```bash
cd "/home/robertslee/Shuncode-Linux" && bash scripts/linux/build-deb.sh --rebuild --install --yes
```

Linux 不要给整条命令加 `sudo`，密码只在本机系统终端的 sudo 提示中输入。`--yes` 不绕过哈希、版本、架构、停止检查或降级保护。该命令适用于 DEB 系统安装，不代表 AppImage 的安装方式。

### 5. 已完成的 Windows 去 Copilot 构建证据（保留记录）

- 版本仍为 `0.7.7`；实际安装包由 319.42 MiB 降到 **213.24 MiB**（223,598,871 字节），减少 106.18 MiB / 33.24%。
- 该包 SHA-256：`696D8ACD47CFCD00EDBA1100519049EEF0586E972B7C6D796E9A6C90830C9620`；构建 ID：`b6f9b2c3ffccc440`；Authenticode：`NotSigned`。
- 原含 Copilot 包已归档。此处哈希只对应那一次 Windows 构建；它不是本次 Mac/Linux 待构建产物的哈希，也不能用于以后同名重打的 Windows 包。
- 本次源码、脚本与日志修复不回写或篡改上述已生成 EXE/DMG/DEB/AppImage 的内容。源码日志文件名仍保留 `docs/mcp-changelog-0.7.7-2026-09-25.md`。
