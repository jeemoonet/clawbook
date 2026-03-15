# OpenClaw Dashboard 使用说明

[← 返回目录](./index.md)

> Dashboard（控制 UI）是 OpenClaw 网关提供的**浏览器管理界面**，用于聊天、配置、会话、技能与运维管理。本文说明为何需要、如何访问以及面板上的主要功能。

---

## 为什么需要 Dashboard

- **统一管理入口**：网关运行后，除飞书等通道外，还需要一个地方查看状态、改配置、看日志、管理技能和定时任务；Dashboard 就是这份「控制台」。
- **不依赖聊天通道**：在未配对飞书或网络受限时，仍可在浏览器里与 Agent 对话、调试模型与技能。
- **图形化配置**：无需直接改 `~/.openclaw/openclaw.json`，在界面上即可查看/编辑配置、应用并重启，并支持配置校验与防覆盖。
- **运维与审批**：查看实时日志、网关健康、执行审批（exec allowlist）、管理 Cron 任务与技能启用状态，便于企业运维与审计。

因此，只要部署了 OpenClaw 网关，就建议了解如何访问 Dashboard 并熟悉其功能。

---

## 如何访问 Dashboard

### 访问地址（默认）

- **本地**：Gateway 默认端口为 **18789**，在运行网关的机器上打开：
  - [http://127.0.0.1:18789/](http://127.0.0.1:18789/)
  - 或 [http://localhost:18789/](http://localhost:18789/)

若页面打不开，请先启动网关：`openclaw gateway`（或已安装守护进程时确认服务在运行）。

### 推荐：用 CLI 打开（带认证）

```bash
openclaw dashboard
```

该命令会：

- 复制带认证信息的访问链接（若配置了 token）
- 在可能的情况下自动打开浏览器
- 无头环境（如仅 SSH）时提示用 SSH 隧道并打印链接

首次连接或看到 **「unauthorized」/ 1008** 时，在 Dashboard 的**设置**里粘贴与网关一致的 **Token**（来自 `gateway.auth.token` 或环境变量 `OPENCLAW_GATEWAY_TOKEN`），然后重新连接。

### 远程访问（企业常见）

OpenClaw **默认不开放远程访问**，仅监听本机。从外网或另一台电脑访问时，需通过以下方式之一：

| 方式                  | 说明                                                                                                                             |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------ |
| **SSH 隧道**          | 在本地执行：`ssh -L 18789:127.0.0.1:18789 user@服务器IP`，再在本地浏览器打开 `http://127.0.0.1:18789/`（详见 [安装与运行 - 访问控制面板](./02-安装与运行.md#访问控制面板)） |
| **Tailscale Serve** | 网关加入 Tailscale 后使用 `openclaw gateway --tailscale serve`，通过 Tailnet 的 HTTPS 地址访问；可配置为信任 Tailscale 身份后免 token                    |
| **反向代理**            | 用 Nginx/Caddy 等代理到 `127.0.0.1:18789`，并配置 HTTPS 与 Token 认证；**不要**把 Dashboard 直接暴露在公网                                            |

**安全提醒**：Dashboard 是**管理界面**（含聊天、配置、执行审批），不要公网暴露；优先使用 localhost、Tailscale 或 SSH 隧道。

### 首次连接：设备配对

从**新浏览器或新设备**首次连接时，网关可能要求**一次性配对审批**（即使已配置 token）。界面会提示类似「disconnected (1008): pairing required」。

在**运行网关的服务器**上执行：

```bash
openclaw devices list          # 查看待批准请求
openclaw devices approve <requestId>   # 批准指定请求
```

批准后该设备会被记住，无需再次配对；本地 `127.0.0.1` 连接通常会自动通过。

---

## 面板上相关功能有哪些

以下基于 [OpenClaw Control UI 文档](https://docs.openclaw.ai/web/control-ui) 与 [Dashboard 文档](https://docs.openclaw.ai/web/dashboard) 整理，实际界面以你当前版本为准。

### 聊天（Chat）

- 在浏览器中**直接与 Agent 对话**，走网关 WebSocket，与飞书等通道共享同一套会话与模型。
- 支持**流式输出**、工具调用与实时工具结果卡片；可**中止**当前回复（如输入 `/stop` 或点击 Stop）。
- 便于在不依赖飞书的情况下调试提示词、模型或技能。

### Agent 模块（多智能体与路由）

当网关配置了**多个 Agent**（多团队、多场景）时，Dashboard 的 **Agent** 相关区域用于查看和管理这些智能体及其路由关系，并与 Chat、Config 配合使用。

#### 为什么有多 Agent

- **多团队隔离**：不同部门或群使用不同「大脑」——独立工作区、会话、模型与技能，互不串数据。
- **场景分工**：例如支持群用客服 Agent、技术群用代码 Agent、某私聊用深度推理 Agent。
- **路由规则**：通过 **bindings** 把飞书用户（open_id）、群（chat_id）或渠道（channel）绑定到指定 `agentId`，消息按规则进入对应 Agent。

#### 在 Dashboard 里能做什么

- **查看 Agent 列表**：当前网关下配置的智能体（如 main、support、dev）及其基本信息。
- **查看绑定（bindings）**：哪些渠道/用户/群路由到哪个 Agent，便于核对多团队配置。
- **Chat 与 Agent 对应**：在 Chat 中发起对话时，可**选择使用哪个 Agent**（或当前会话所属 Agent）；不同 Agent 对应不同工作区、人格与技能，便于分别调试。
- **与配置联动**：Agent 与 bindings 的详细配置仍在 `~/.openclaw/openclaw.json` 的 `agents.list` 与 `bindings` 中；Dashboard 的 Config 可查看/编辑该段，Agent 模块主要提供可视化概览与快速切换。

#### 路由优先级（概念）

绑定按「越具体越优先」匹配，例如：

1. **peer 精确匹配**（某用户或某群）→ 最高优先级  
2. **channel / accountId**（某渠道或某账号）  
3. **默认 Agent**（未匹配时使用的智能体）

企业场景下通常为：某群绑定到 support Agent、某群绑定到 dev Agent，其余走默认 main。详见 [多团队与多 Agent](./05-多团队与多Agent.md)。

#### 与 CLI 的对应

若在服务器上习惯用命令行，可与 Dashboard 对照使用：

```bash
openclaw agents list --bindings   # 查看所有 Agent 及绑定关系
openclaw agents add <agentId>     # 添加新智能体（需再在 Config 中补全 workspace 等）
openclaw agents bind --agent <id> --bind <channel:target>   # 绑定路由
openclaw agents unbind --agent <id> --bind <channel:target> # 解绑
```

### 配置（Config）

- **查看/编辑** `~/.openclaw/openclaw.json`（含网关、通道、技能等）。
- 支持**表单**与**原始 JSON** 两种方式；写入前有基础校验与防并发覆盖。
- **应用并重启**：修改后可一键应用并重启网关，使配置生效。

### 会话（Sessions）

- **会话列表**：查看当前与历史会话。
- **每会话覆盖**：可为某会话单独设置 thinking / fast / verbose / reasoning 等模型参数，便于调试或 A/B 对比。

### 技能（Skills）

- **状态**：查看已安装技能的启用状态。
- **启用/禁用**：在面板上开关技能，无需改配置文件。
- **安装与 API Key**：部分版本支持从面板安装技能或更新 API Key 等配置。

### 定时任务（Cron）

- **列表**：查看已配置的 Cron 任务。
- **新增/编辑/删除**：创建或修改定时任务（如每日提醒、周期报表）。
- **运行历史**：查看最近执行记录与是否成功。
- **高级选项**：如执行后删除、仅跑一次、指定模型或 thinking 覆盖、webhook 通知等（以当前 UI 为准）。

### 通道（Channels）

- **状态**：查看 WhatsApp、Telegram、Discord、飞书等通道的连接状态与健康情况。
- **飞书等**：部分通道支持在面板查看/触发登录（如二维码）、或对单通道做配置补丁。

### 节点（Nodes）

- **列表与能力**：查看已配对的移动端或其它节点（Node）及其能力（如 Canvas、相机、语音等）。

### 日志（Logs）

- **实时日志**：拉取网关文件日志的 tail，支持过滤与导出，便于排查问题。

### 调试（Debug）

- **状态/健康/模型**：查看网关状态、健康检查结果、可用模型列表等快照。
- **事件日志**：查看近期网关事件。
- **手动 RPC**：对网关进行手动 RPC 调用，用于高级调试。

### 执行审批（Exec approvals）

- **Allowlist 管理**：配置允许在网关或节点上执行命令的名单与策略，避免未授权执行。

### 系统与更新

- **实例与在线状态**：查看当前在线实例列表并刷新。
- **更新与重启**：执行包/代码更新并重启网关（`update.run`），并查看重启结果报告。

---

## 常见问题

### 打开后显示「unauthorized」或 1008

1. 在 Dashboard **设置**中粘贴与网关一致的 Token（`openclaw config get gateway.auth.token` 或查看 `OPENCLAW_GATEWAY_TOKEN`）。
2. 若 token 由 SecretRef 管理，需在能解析该 Secret 的环境执行 `openclaw dashboard` 获取带 token 链接，或在设置中手动填入已解析的 token。
3. 确保网关可达：本地用 `openclaw status`；远程先用 SSH 隧道或 Tailscale 再访问 `http://127.0.0.1:18789/`。

### 无头服务器上如何拿到访问链接

在服务器上执行：

```bash
openclaw dashboard --no-open
```

会输出带 token 的完整 URL（如 `http://127.0.0.1:18789/#token=...`）。在本地建立 SSH 隧道后，在**本地浏览器**中粘贴该 URL（或把 token 填到本地打开的 Dashboard 设置中，URL 用 `http://127.0.0.1:18789/`）即可访问。

### 语言

Control UI 支持多语言（含中文 zh-CN），首次加载可按浏览器语言选择，之后可在「访问」卡片中的语言选择器里切换。

---

## 相关链接

- [OpenClaw 仪表板（英文）](https://docs.openclaw.ai/web/dashboard)
- [OpenClaw 控制 UI 功能说明（英文）](https://docs.openclaw.ai/web/control-ui)
- [安装与运行 - 访问控制面板](./02-安装与运行.md#访问控制面板)
- [多团队与多 Agent](./05-多团队与多Agent.md)（Agent 与 bindings 配置详解）

---

[← 返回目录](./index.md) | [安装与运行](./02-安装与运行.md) | [多团队与多 Agent](./05-多团队与多Agent.md) | [通道配置与访问控制](./04-通道配置与访问控制.md)