# ShunCode 源码归档

ShunCode —— 基于 Microsoft **Code - OSS**（Visual Studio Code）构建的 Electron 集成开发环境。

本仓库收录 **ShunCode 第一方源码**，即作为产品核心的 `shuncode` 内置扩展（原生 Chat / Agent 集成层）的完整 TypeScript 源码、构建产物与配套文档。

| 项目 | 信息 |
| --- | --- |
| ShunCode 产品版本 | 0.8.1 |
| 内核版本（Code - OSS） | 1.136.1 |
| 内核构建号 | `5c03086397d13f66eb8c46dc97043a91d9e97c9f` |
| 目标平台 | Windows x64 |
| 许可证 | MIT |

## 如何获取

| 方式 | 入口 |
| --- | --- |
| **下载 ZIP（推荐，免装 Git）** | [Releases](https://github.com/TooT-s/shuncode-/releases) 页面 → 下载 `shuncode-v0.8.1-source.zip`，或直接点该版本的 **Source code (zip)** |
| 固定版本直链 | https://github.com/TooT-s/shuncode-/archive/refs/tags/v0.8.1.zip |
| 克隆仓库（完整历史） | `git clone https://github.com/TooT-s/shuncode-.git` |
| 克隆指定版本 | `git clone -b v0.8.1 --depth 1 https://github.com/TooT-s/shuncode-.git` |

> 仓库体积很小（约 4.5 MB），包含全部 99 个源码与文档文件，克隆/下载都不需要额外依赖。
> 解压后即为本仓库的完整目录树，可直接用编辑器打开阅读源码。

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
│   └── extensions/shuncode/                 # ★ 第一方扩展
│       ├── src/                             # TypeScript 源码（68 个文件）
│       ├── dist/                            # 构建产物（extension.js 等 7 个文件）
│       ├── test/                            # 单元测试（3 个文件）
│       ├── agents/                          # 内置 Agent 定义（ask / code / plan）
│       ├── media/                           # 图标与 Workspace Hub 前端资源
│       ├── package.json                     # 扩展清单（命令、激活事件等）
│       └── tsconfig.json
└── README.md
```

## src/ 源码模块概览

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

## 未收录内容

本目录中以下内容**未**纳入版本控制（规则见 [`.gitignore`](.gitignore)）：

| 内容 | 体积 | 说明 |
| --- | --- | --- |
| `ShunCode.exe` 及 `.dll` / `.pak` / `locales/` 等 | 约 330 MB | Electron 与 Chromium 运行时 |
| `resources/app/out/` | 106 MB | Code - OSS 内核编译产物 |
| `resources/app/node_modules.asar(.unpacked)` | 138 MB | 内核依赖归档 |
| `resources/app/extensions/shuncode/runtime/` | 433 MB | 捆绑的 git / uv 运行时 |
| 其余 96 个内置扩展 | 约 51 MB | 来自 Microsoft Code - OSS |
| `LICENSES.chromium.html` | 19.5 MB | Chromium 第三方许可证 |
| `resources/app/extensions/shuncode/vendor/` | 653 KB | PSReadLine（微软第三方组件） |

## 许可

本仓库内容遵循 MIT 许可证，详见 [`resources/app/LICENSE.txt`](resources/app/LICENSE.txt)。

ShunCode 基于 Microsoft Code - OSS 构建，Code - OSS 代码版权归 Microsoft Corporation 所有（MIT）；第三方组件声明见 [`resources/app/ThirdPartyNotices.txt`](resources/app/ThirdPartyNotices.txt)。
