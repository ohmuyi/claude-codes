# claude-codes

一个面向源码阅读、架构分析与防御性安全研究的 Claude Code `src/` 代码快照仓库。

## 概览

这个仓库集中展示了 Claude Code 终端代理的大部分核心实现，覆盖了 TUI 渲染、命令系统、工具调用、文件编辑、Shell 执行、MCP 集成、远程桥接、会话管理、插件与 Skills、团队协作与多代理编排等关键能力。

从目录结构看，它保留了比较完整的运行时核心，但当前仓库并未包含常见的工程根文件，例如 `package.json`、`tsconfig.json`、构建脚本或测试入口。因此它更像一份适合阅读与拆解的源码镜像，而不是开箱即用的发行包。

## 平台覆盖与代码重点

当前快照主要覆盖以下几个层面：

- 终端交互层：基于 `Ink`/React 风格组件构建的 CLI/TUI 界面。
- 命令层：大量 `/` 命令的声明、路由与交互逻辑。
- 工具层：文件、搜索、编辑、Shell、Web、MCP、任务、团队协作等工具抽象。
- 会话与桥接层：本地会话、远程传输、Bridge、移动端与桌面端相关逻辑。
- 扩展层：Plugins、Skills、MCP Servers、LSP、遥测与特性开关。
- 协作层：计划模式、任务编排、Swarm/Team、多代理上下文同步。

## 仓库统计

基于当前仓库内容统计：

| 项目 | 数值 |
| --- | ---: |
| `src/` 文件总数 | 1,913 |
| `src/` 总行数 | 512,749 |
| `src/commands/` 文件数 | 208 |
| `src/tools/` 文件数 | 185 |
| `src/components/` 文件数 | 390 |
| 关键入口文件 `src/main.tsx` 行数 | 4,683 |
| `src/QueryEngine.ts` 行数 | 1,295 |
| `src/Tool.ts` 行数 | 792 |
| `src/commands.ts` 行数 | 754 |

这些数字说明当前仓库并不是一个最小示例，而是一份规模较大的真实产品级代码快照。

## 架构亮点

### 1. 终端代理主入口

[`src/main.tsx`](./src/main.tsx) 是主要入口之一，承载了终端 UI 渲染、输入处理、状态流转、命令交互与会话驱动逻辑。单文件体量很大，说明不少高层调度与 UI 行为都集中在这里。

### 2. 命令注册与路由

[`src/commands.ts`](./src/commands.ts) 负责组织命令元数据与调度边界，而 [`src/commands/`](./src/commands/) 下则按功能拆分出大量命令实现，例如 `review`、`plan`、`plugin`、`mcp`、`memory`、`teleport`、`vim` 等。

### 3. 工具调用协议

[`src/Tool.ts`](./src/Tool.ts) 定义了工具能力的核心抽象，而 [`src/tools/`](./src/tools/) 提供了具体实现。这里可以看到 Claude Code 如何把“读文件、改文件、执行命令、联网、调用 MCP、发消息、管理任务”等动作统一成一套可调度的工具接口。

### 4. Query 与执行编排

[`src/QueryEngine.ts`](./src/QueryEngine.ts) 体现了请求执行、工具调度、上下文拼接与多阶段推进的核心逻辑，是理解代理行为的重要切入点。

### 5. API、鉴权与远程能力

[`src/services/`](./src/services/) 与 [`src/bridge/`](./src/bridge/) 展示了 API 调用、OAuth/JWT、MCP 鉴权、Bridge 传输、SSE/WebSocket 客户端、远程会话与跨端连接等产品级实现细节。

### 6. 团队协作与多代理

[`src/utils/swarm/`](./src/utils/swarm/) 以及 `Team*`、`Task*`、`SendMessageTool` 等相关模块表明，这个代码库并不只是单代理 CLI，还包含多代理协作、任务分发、权限同步与上下文共享能力。

## 目录地图

下面这份简化目录图适合第一次阅读时建立全局认知：

```text
src/
├── main.tsx              # 终端主入口与高层 UI/交互逻辑
├── QueryEngine.ts        # Query 执行与工具编排
├── Tool.ts               # 工具协议与类型定义
├── commands.ts           # 命令注册与筛选
├── assistant/            # 助手会话相关逻辑
├── bridge/               # 远程桥接与跨端传输
├── buddy/                # 伙伴/提示辅助能力
├── cli/                  # CLI 处理器、传输层与输出
├── commands/             # Slash commands 实现
├── components/           # Ink/React 组件
├── context/              # 上下文装配
├── hooks/                # 交互与状态 hooks
├── plugins/              # 插件入口
├── remote/               # 远程能力
├── services/             # API、认证、遥测、MCP 等服务
├── skills/               # Skills 相关逻辑
├── tasks/                # 任务框架
├── tools/                # 内建工具
├── utils/                # 公共基础设施与大量底层能力
├── vim/                  # Vim 模式
└── voice/                # 语音模式
```

## 值得关注的命令面

`src/commands/` 下可以看到非常宽的命令覆盖面。以下几类最值得优先阅读：

- 会话与上下文：`context`、`resume`、`session`、`summary`
- 规划与协作：`plan`、`agents`、`tasks`、`memory`
- 代码与审查：`review`、`diff`、`branch`、`commit-push-pr`
- 扩展能力：`plugin`、`skills`、`mcp`
- 运行环境：`permissions`、`remote-env`、`sandbox-toggle`
- 体验控制：`theme`、`output-style`、`vim`、`voice`
- 远程与跨端：`teleport`、`remote-setup`、`desktop`、`mobile`

如果你的目标是尽快理解产品功能边界，直接顺着这些命令目录阅读通常比从所有 UI 组件开始更高效。

## 工具系统面

从 `src/tools/` 可以看到当前仓库暴露出的核心工具族：

- 文件与搜索：`FileReadTool`、`FileEditTool`、`FileWriteTool`、`GlobTool`、`GrepTool`
- 代码理解：`LSPTool`、`NotebookEditTool`
- 命令执行：`BashTool`、`PowerShellTool`、`REPLTool`
- 联网与资源访问：`WebFetchTool`、`WebSearchTool`
- MCP 生态：`MCPTool`、`McpAuthTool`、`ListMcpResourcesTool`、`ReadMcpResourceTool`
- 任务与协作：`TaskCreateTool`、`TaskUpdateTool`、`TaskOutputTool`、`SendMessageTool`
- 组织模式：`EnterPlanModeTool`、`ExitPlanModeTool`、`EnterWorktreeTool`、`ExitWorktreeTool`
- 团队与待办：`TeamCreateTool`、`TeamDeleteTool`、`TodoWriteTool`

这套设计说明 Claude Code 的核心不是“把提示词发给模型”这么简单，而是围绕工具协议构建了一整套可执行、可中断、可审计、可扩展的代理运行时。

## 主要系统分层

### UI 与交互

`components`、`ink`、`hooks`、`screens`、`keybindings` 共同构成终端交互层，负责输入响应、组件渲染、快捷键与状态驱动。

### 命令与流程控制

`commands`、`cli`、`context`、`tasks` 负责命令执行路径、状态推进与用户交互流程。

### 工具与执行环境

`tools`、`utils/shell`、`utils/sandbox`、`utils/processUserInput` 决定了代理如何安全地读写文件、运行命令与处理用户输入。

### 会话、远程与桥接

`bridge`、`remote`、`server`、`session*` 相关模块负责跨端同步、远程连接、桥接传输与长生命周期会话。

### 扩展生态

`plugins`、`skills`、`services/mcp`、`utils/plugins` 展示了插件系统、技能系统与 MCP 集成的扩展路径。

### 协作与多代理

`utils/swarm`、`teammate*`、`AgentTool`、`Team*`、`Task*` 等模块体现了多代理协作、团队管理与任务同步能力。

### API、认证与遥测

`services/api`、`services/auth`、`services/analytics`、`utils/telemetry` 覆盖了 OAuth、JWT、接口调用、特性开关与埋点链路。

## 安全研究与审计价值

这份代码快照尤其适合以下方向的研究：

- 工具权限边界与命令执行链路
- 文件编辑、Shell 调用与沙箱约束
- MCP 接入面、认证流程与资源访问
- Prompt 拼装、上下文注入与缓存策略
- 多代理协作时的状态同步与权限传递
- 远程桥接、SSE/WebSocket 传输与会话恢复

如果你的目标是做安全审计，建议优先阅读 `Tool.ts`、`QueryEngine.ts`、`services/api/claude.ts`、`bridge/`、`tools/`、`utils/sandbox/` 与 `utils/swarm/`。

## 阅读建议

推荐按下面顺序进入代码库：

1. `src/main.tsx`
2. `src/commands.ts`
3. `src/Tool.ts`
4. `src/tools/`
5. `src/QueryEngine.ts`
6. `src/services/api/`
7. `src/bridge/`
8. `src/utils/swarm/`

这样可以先建立“用户输入如何进入系统”，再理解“系统如何调度工具”，最后再看远程、扩展与协作能力。



## 最后

配套图书：《Claude Code 源码精讲》也将同步开源，敬请期待。
