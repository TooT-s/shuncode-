# ShunCode 源码归档

ShunCode —— 基于 Microsoft **Code - OSS**（Visual Studio Code）构建的 Electron 集成开发环境。

本仓库收录 **ShunCode 的源码与完整实现**：作为产品核心的 `shuncode` 第一方扩展的 TypeScript 源码、Code - OSS 内核的编译产物与运行时依赖，以及全部 97 个内置扩展。

| 项目 | 信息 |
| --- | --- |
| ShunCode 产品版本 | 0.8.1 |
| 内核版本（Code - OSS） | 1.136.1 |
| 内核构建号 | `5c03086397d13f66eb8c46dc97043a91d9e97c9f` |
| 目标平台 | Windows x64 |
| 许可证 | MIT |
| 仓库体积 | 约 175 MB（约 2065 个文件） |

## 如何获取

| 方式 | 入口 |
| --- | --- |
| **下载 ZIP（推荐，免装 Git）** | [Releases](https://github.com/TooT-s/shuncode-/releases) 页面 → 下载 `shuncode-v0.8.1-source.zip`，或直接点该版本的 **Source code (zip)** |
| 固定版本直链 | https://github.com/TooT-s/shuncode-/archive/refs/tags/v0.8.1.zip |
| 克隆仓库（完整历史） | `git clone https://github.com/TooT-s/shuncode-.git` |
| 克隆指定版本（浅克隆） | `git clone -b v0.8.1 --depth 1 https://github.com/TooT-s/shuncode-.git` |

> 解压后即为本仓库的完整目录树，可直接用编辑器打开阅读源码；内核部分为**未压缩的可读 JavaScript**，无需构建即可阅读。

## 目录结构

```
.
├── bin/                                     # 命令行入口（shuncode / shuncode.cmd）
├── resources/app/
│   ├── package.json                         # 内核 app 层清单
│   ├── product.json                         # 产品定制描述（名称、协议、内置扩展清单等）
│   ├── LICENSE.txt                          # MIT 许可证
│   ├── ThirdPartyNotices.txt                # 第三方组件声明
│   ├── SHUNCODE_CHANGELOG.md                # 完整变更日志
│   ├── SHUNCODE_CHANGELOG_SHORT.md          # 简要变更日志
│   ├── resources/                           # 应用图标与 UI 资源
│   ├── node_modules/                        # 内核运行时依赖包（@vscode/*、zod 等）
│   ├── out/                                 # ★ Code - OSS 内核编译产物（115 MB）
│   │   ├── main.js                          # Electron 主进程入口
│   │   ├── cli.js                           # 命令行入口
│   │   ├── bootstrap-fork.js                # 子进程引导
│   │   ├── nls.*.json / nls.messages.js     # 多语言（NLS）资源
│   │   ├── media/                           # 启动画面等静态资源
│   │   ├── vscode-dts/                      # 扩展 API 类型定义（.d.ts）
│   │   └── vs/                              # 内核主体
│   │       ├── base/                        # 基础工具库
│   │       ├── platform/                    # 平台抽象层（依赖注入、命令、配置…）
│   │       ├── editor/                      # Monaco 编辑器内核
│   │       ├── workbench/                   # 工作台（界面、视图、贡献点…）
│   │       ├── sessions/                    # 会话视图（ShunCode 定制模块）
│   │       └── code/                        # 桌面端入口
│   └── extensions/                          # 内置扩展（共 97 个）
│       ├── shuncode/                        # ★ 第一方扩展，展开如下：
│       │   ├── src/                         #   TypeScript 源码（68 个文件）
│       │   ├── dist/                        #   构建产物（extension.js 等 7 个文件）
│       │   ├── test/                        #   单元测试（3 个文件）
│       │   ├── agents/                      #   内置 Agent 定义（ask / code / plan）
│       │   ├── media/                       #   图标与 Workspace Hub 前端资源
│       │   ├── package.json                 #   扩展清单（命令、激活事件等）
│       │   └── tsconfig.json
│       ├── node_modules/typescript/         # TypeScript 语言服务
│       ├── ms-vscode.js-debug/              # JavaScript 调试器
│       ├── markdown-language-features/      # Markdown 语言特性
│       ├── vscode-language-pack-zh-hans/    # 简体中文语言包
│       ├── git/、git-base/、github/          # 版本控制集成
│       ├── cpp/、java/、python/、go/…        # 各语言语法与片段支持
│       └── …（其余 80 余个，来自 Microsoft Code - OSS）
└── README.md
```

## 源码模块概览

`resources/app/extensions/shuncode/src/` 共 68 个文件（57 个 `.ts`、10 个 `.mts`、1 个 `.js`），按功能大致分为：

| 模块 | 代表文件 | 说明 |
| --- | --- | --- |
| 扩展入口 | `extension.ts`、`native-chat.ts` | 激活、命令注册与原生会话界面 |
| Bridge 服务 | `bridge-server.ts`、`bridge-route-token.ts`、`bridge-tunnel-lease.ts`、`bridge-access-controller.ts`、`bridge-tool-dispatcher.ts` | 会话桥接、路由令牌、隧道租约与工具派发 |
| 隧道 / 中继 | `bridge-quick-tunnel.ts`、`cloudflare-edge-relay.ts` | Cloudflare 快速隧道与边缘中继 |
| MCP 集成 | `external-mcp-catalog.ts`、`external-mcp-connection.ts`、`external-mcp-oauth-flow.ts`、`external-mcp-secret-admin.ts`、`bridge-mcp-modern.ts` 等（18 个文件） | 外部 MCP 的目录、连接、OAuth、密钥与网络诊断 |
| 模型接入 | `model-provider.ts`、`model-defaults.mts`、`model-endpoint-url.mts`、`model-reasoning.mts`、`deepseek-compat.mts` | 模型提供商、端点与推理参数 |
| 许可与支付 | `bridge-license-service.ts`、`bridge-license-config.ts`、`bridge-license-network-policy.mts`、`bridge-payment-network.mts` | 许可校验、网络策略与支付 |
| Codex 集成 | `codex-auth.ts`、`codex-account-view.ts` | Codex 账号授权与视图 |
| 工作区 | `workspace-hub.ts`、`workspace-hub-store.ts`、`workspace-hub-types.ts` | Workspace Hub 存储与类型 |
| Skill 中心 | `skill-center.ts`、`skill-list-tool.ts` | Skill 列表与归档 |
| 运行时 / 工具 | `runtime-client.ts`、`lsp-tool.ts`、`ide-tool-broker.ts`、`managed-bash-protocol.ts`、`mcp-runtime-host.ts` | 运行时客户端、LSP、IDE 工具代理 |
| 配置与状态 | `config.ts`、`chat-history.mts`、`custom-agents.ts`、`agent-checkpoint-store.ts`、`branch-state.ts` | 配置、会话历史、自定义 Agent 与检查点 |

> `src/` 中 `.mts` 为 ESM 模块，`.ts` 为常规 TypeScript 模块。

## 内核部分说明

`resources/app/out/` 为 Code - OSS 内核的**编译产物**（上游未提供 TypeScript 源码，此即发行版中的实现形态）：

- 代码为**未压缩、可读**的 JavaScript，保留了原始模块划分与注释结构，可直接阅读与检索
- `vs/base` → `vs/platform` → `vs/editor` → `vs/workbench` → `vs/code` 构成自底向上的分层架构
- `vs/sessions/` 为 ShunCode 相对上游 Code - OSS 的定制模块
- 体积最大的两个 bundle 为工作台与会话视图：`workbench.desktop.main.js`（37 MB）、`sessions.desktop.main.js`（38 MB）
- 不含 sourcemap（发行版未附带）

## 内置扩展（97 个）

`resources/app/extensions/` 收录全部内置扩展，代码均为**未压缩的可读 JavaScript / JSON**：

| 类别 | 扩展 |
| --- | --- |
| ★ 第一方 | `shuncode`（源码见上） |
| 语言服务 | `node_modules/typescript`、`typescript-language-features`、`ms-vscode.js-debug`（JS 调试器，含 `.node` / `.wasm`）、`markdown-language-features`、`css-language-features`、`html-language-features`、`json-language-features`、`php-language-features` |
| 语言基础（语法高亮 / 片段 / 括号） | `cpp`、`csharp`、`java`、`python`、`go`、`rust`、`julia`、`latex`、`ruby`、`php`、`swift`、`r`、`lua`、`perl`、`shellscript`、`powershell`、`sql`、`xml`、`yaml`、`docker`、`bat`、`clojure`、`coffeescript`、`dart`、`fsharp`、`groovy`、`handlebars`、`hlsl`、`ini`、`log`、`make`、`objective-c`、`pug`、`razor`、`restructuredtext`、`scss`、`shaderlab`、`vb`、`ipynb` 等 |
| 版本控制 | `git`、`git-base`、`github`、`github-authentication`、`merge-conflict` |
| 账户与隧道 | `microsoft-authentication`（含 `msalruntime.dll`）、`tunnel-forwarding` |
| 界面与主题 | `theme-defaults`、`theme-seti`、`theme-monokai`、`theme-abyss`、`theme-quietlight`、`theme-solarized-*`、`theme-tomorrow-night-blue`、`theme-kimbie-dark`、`theme-modern-icons`、`media-preview`、`simple-browser` |
| 构建与工具 | `npm`、`grunt`、`gulp`、`jake`、`emmet`、`configuration-editing`、`extension-editing`、`debug-auto-launch`、`debug-server-ready`、`diff`、`terminal-suggest`、`notebook-renderers`、`mermaid-markdown-features`、`references-view`、`search-result` |
| 语言包 | `vscode-language-pack-zh-hans`（简体中文） |

> 扩展的体积大头是 `node_modules/typescript/lib/typescript.js`（8.7 MB）；其余多为 KB～百 KB 级。

## 未收录内容

本目录中以下内容**未**纳入版本控制（规则见 [`.gitignore`](.gitignore)）：

| 内容 | 体积 | 说明 |
| --- | --- | --- |
| `ShunCode.exe` 及 `.dll` / `.pak` / `locales/` 等 | 约 330 MB | Electron 与 Chromium 运行时（二进制） |
| `resources/app/node_modules.asar(.unpacked)` | 138 MB | 依赖打包归档，与 `node_modules/` 内容重叠 |
| `resources/app/extensions/shuncode/runtime/` | 433 MB | 捆绑的 git / uv 运行时（二进制） |
| `LICENSES.chromium.html` | 19.5 MB | Chromium 第三方许可证 |
| `resources/app/extensions/shuncode/vendor/` | 653 KB | PSReadLine（微软第三方组件，二进制） |

## 许可

本仓库内容遵循 MIT 许可证，详见 [`resources/app/LICENSE.txt`](resources/app/LICENSE.txt)。

ShunCode 基于 Microsoft Code - OSS 构建，Code - OSS 代码版权归 Microsoft Corporation 所有（MIT）；第三方组件声明见 [`resources/app/ThirdPartyNotices.txt`](resources/app/ThirdPartyNotices.txt)。
