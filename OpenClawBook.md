# OpenClaw 使用手册 — 调研报告

> 调研目的：为编写《OpenClaw 使用手册》收集官方文档结构、功能范围、现有教程与建议大纲。  
> 调研日期：2025-03-14

---

## 一、OpenClaw 产品概览

### 1.1 是什么

- **定义**：OpenClaw 是一个**自托管的 AI 网关**，把常用聊天应用（WhatsApp、Telegram、Discord、飞书、iMessage 等）和 AI 编程助手（如 Pi）连接起来。
- **定位**：面向开发者和高级用户，希望「从任何地方发消息就能用上 AI、数据在自己手里、不依赖托管服务」的场景。
- **特点**：
  - 开源（MIT）、社区驱动
  - **Agent 原生**：为编程 Agent 设计，支持工具调用、会话、记忆、多 Agent 路由
  - **多渠道**：一个 Gateway 同时服务 WhatsApp、Telegram、Discord、飞书等
  - **自托管**：运行在自己的机器或服务器上

### 1.2 核心架构（「一个大脑，多张嘴巴」）

- **Gateway（网关）**：默认端口 `18789`，是核心控制中心，负责：
  - AI 推理
  - 对话记忆
  - 技能执行
- **数据**：会话、配置、凭证等保存在本地（如 `~/.openclaw/`）。
- **接入方式**：
  - 聊天 App（Telegram/WhatsApp/飞书等）→ Gateway
  - CLI、Web 控制台、macOS 应用、iOS/Android 节点 → Gateway

---

## 二、官方文档与索引

### 2.1 主要来源


| 来源                                                                     | 说明                |
| ---------------------------------------------------------------------- | ----------------- |
| [docs.openclaw.ai](https://docs.openclaw.ai)                           | 英文主站，Mintlify 构建  |
| [docs.openclaw.ai/zh-CN/](https://docs.openclaw.ai/zh-CN/start/wizard) | 中文文档（部分页面）        |
| [docs.openclaw.ai/llms.txt](https://docs.openclaw.ai/llms.txt)         | 完整文档索引（供 LLM/检索用） |


### 2.2 文档分层（按主题）

根据 `llms.txt` 索引，官方文档大致分为：

- **入门**：安装、快速开始、新手向导（`openclaw onboard`）
- **渠道（Channels）**：WhatsApp、Telegram、Discord、Feishu、iMessage、Signal、Slack、Matrix、Mattermost、Google Chat、LINE、IRC、Nostr、Twitch、Zalo 等
- **CLI**：`onboard`、`configure`、`gateway`、`agents`、`channels`、`pairing`、`skills`、`dashboard`、`health`、`doctor`、`logs` 等
- **概念**：Agent 运行时、工作区、会话、记忆、多 Agent 路由、OAuth、系统提示词等
- **Gateway**：配置、认证、远程访问、Tailscale、健康检查、故障排查
- **安装与部署**：npm/pnpm、Docker、Kubernetes、Fly.io、GCP、Hetzner、Nix、Ansible 等
- **节点（Nodes）**：iOS/Android、音频、相机、媒体、Talk 模式等
- **工具与技能**：Skills、Skills 配置、Web 工具（Brave Search、web_fetch）等
- **自动化**：Cron、Webhooks、Hooks、Gmail PubSub 等
- **帮助**：FAQ、故障排查、环境变量、调试、测试

手册可优先覆盖「入门 + 常用渠道 + CLI + Gateway 基础 + 常见问题」，再按需引用上述专题链接。

---

## 三、安装与首次使用

### 3.1 安装方式

- **npm**：`npm install -g openclaw@latest`
- **pnpm**：`pnpm add -g openclaw@latest`
- **一键脚本**（社区常见）：
  - macOS/Linux：`curl -fsSL https://openclaw.ai/install.sh | bash`
  - Windows：`curl -fsSL https://openclaw.ai/install.cmd | install.cmd`
- **从源码（开发）**：`git clone` → `pnpm install` → `pnpm ui:build` → `pnpm build` → `openclaw onboard --install-daemon`

### 3.2 推荐首次配置流程

1. **新手向导**（推荐）：
  `openclaw onboard --install-daemon`  
   可一次性配置：本地/远程 Gateway、渠道、Skills、工作区默认值。
2. **快速开始聊天（不先配渠道）**：
  `openclaw dashboard` 在浏览器中打开控制台即可聊天。
3. **重新配置**：
  `openclaw configure`

### 3.3 向导模式

- **快速开始**：采用默认（如 Telegram/WhatsApp 私信用允许列表、Tailscale 关、端口 18789、本地 Gateway 等）。
- **高级**：逐步配置模式、工作区、Gateway、渠道、守护进程、Skills。

### 3.4 非交互（脚本化）示例

```bash
openclaw onboard --non-interactive \
  --mode local \
  --auth-choice apiKey \
  --anthropic-api-key "$ANTHROPIC_API_KEY" \
  --gateway-port 18789 \
  --gateway-bind loopback \
  --install-daemon \
  --daemon-runtime node \
  --skip-skills
```

官方文档对 Gemini、Moonshot、Vercel AI Gateway、OpenCode Zen 等也有对应 `--non-interactive` 示例。

---

## 四、AI 模型与认证

- **常用提供商**：Claude（Anthropic API Key 或 setup-token）、OpenAI（含 Codex）、Google Gemini、MiniMax、Moonshot、Synthetic、Vercel AI Gateway、OpenCode Zen 等。
- **验证**：`openclaw models status`
- **凭证与配置**：  
  - 凭证：`~/.openclaw/credentials/`（如 oauth.json、各渠道凭证）  
  - 认证配置：`~/.openclaw/agents/<agent>/auth-profiles.json`

手册可写「模型选择 + 如何填 API Key / OAuth + 如何检查状态」。

---

## 五、渠道（Channels）与配对

### 5.1 国内常见渠道

- **飞书（Feishu / Lark）**：官方内置渠道，WebSocket 长连接，无需公网回调地址；支持国内飞书与国际版 Lark，NAT 与企业内网友好。企业自建应用需在飞书开放平台创建应用、配置权限与事件订阅（长连接）。
- **企业微信（WeChat Work）**：国内企业常用办公与沟通平台；可通过社区插件或扩展接入 OpenClaw，实现企微内与 AI 助手对话。
- **钉钉（DingTalk）**：国内企业协同与考勤等场景广泛使用；国内社区（如 ClawdHub）提供钉钉集成能力，可对接 OpenClaw 网关。
- **其他国内相关**：部分渠道以插件或社区扩展形式提供（如 QQ、Zalo 等），具体以官方与中文社区文档为准。手册可优先覆盖飞书、企业微信、钉钉的创建应用、配置与配对流程。

### 5.2 配对（Pairing）

- 私信默认多为「配对」：首条消息会发验证码，在服务器执行  
`openclaw pairing approve telegram <code>` 批准。
- 常用命令：  
  - `openclaw pairing list`  
  - `openclaw pairing approve <channel> <code>`

手册可按「每个渠道一小节：创建 Bot/应用 → 在 OpenClaw 里配置 → 配对/允许列表」来写。

---

## 六、Gateway 与守护进程

- **安装/启停**：  
  - `openclaw gateway install`（安装守护进程）  
  - `openclaw gateway status`  
  - `openclaw gateway --port 18789`（手动前台运行）
- **默认**：绑定 `127.0.0.1:18789`，支持 Token 认证。
- **日志**：`openclaw logs --follow`
- **健康与诊断**：  
  - `openclaw health`  
  - `openclaw status --deep`  
  - `openclaw doctor`

手册可包含「端口与绑定、认证、守护进程（LaunchAgent/systemd）、简单故障排查」。

---

## 七、Skills 与浏览器 Relay

### 7.1 Skills

- **作用**：为 AI 提供「可执行能力」（如网页浏览、搜索、邮件等）。
- **命令**：  
  - `openclaw skills list`  
  - `openclaw skills install <skill>`（如 web-search）  
  - `openclaw skills enable <skill>`
- **配置**：在 `~/.openclaw/openclaw.json` 中，可启用/禁用、配 API Key 等。
- **扩展**：可从 ClawHub 等安装更多技能（文档提到 3000+ 技能）。

### 7.2 浏览器 Relay（Browser Relay）

- **三种模式**（常见教程总结）：
  - **Extension Relay**（如 18792）：挂到现有 Chrome 标签，复用登录态。
  - **Managed Browser**（如 18800）：独立 Chromium，隔离环境（推荐）。
  - **Remote CDP**：连接远程浏览器，适合分布式/云端。
- **用途**：通过 CDP 让 AI 控制真实浏览器，完成自动化操作。

手册可设「Skills 安装与配置」和「浏览器 Relay 选型与基本配置」两小节。

---

## 八、多 Agent 与工作区

- **添加独立 Agent**：  
`openclaw agents add <name>`  
可配合 `--workspace`、`--model`、`--bind` 等。
- **切换**：`openclaw agents switch <name>`
- **工作区**：默认 `~/.openclaw/workspace`，含 Agent 指令、用户偏好、记忆等；完整布局见官方「Agent Workspace」文档。

手册可简要说明「何时需要多 Agent、如何添加与切换」。

---

## 九、Web 控制台与远程访问

- **本地控制台**：Gateway 启动后访问 `http://127.0.0.1:18789/` 或执行 `openclaw dashboard`。
- **远程**：文档涉及 Web 访问方式与 Tailscale 等，可按「远程访问」单独一小节或引用官方链接。

---

## 十、配置文件与路径

- **主配置**：`~/.openclaw/openclaw.json`（JSON5）
- **工作区**：`~/.openclaw/workspace/`（可配置）
- **凭证**：`~/.openclaw/credentials/`
- **会话**：`~/.openclaw/agents/<agent>/sessions/`

手册可提供「配置文件结构概览 + 常用字段说明」，并指向官方 Configuration Reference。

---

## 十一、常用 CLI 速查


| 命令                                                   | 用途               |
| ---------------------------------------------------- | ---------------- |
| `openclaw onboard`                                   | 新手向导             |
| `openclaw configure`                                 | 重新配置             |
| `openclaw dashboard`                                 | 打开 Web 控制台       |
| `openclaw gateway install/status`                    | 网关守护进程           |
| `openclaw gateway --port 18789`                      | 手动启动网关           |
| `openclaw health` / `openclaw doctor`                | 健康与诊断            |
| `openclaw pairing list/approve`                      | 配对管理             |
| `openclaw channels login`                            | 渠道登录（如 WhatsApp） |
| `openclaw agents add/switch`                         | 多 Agent 管理       |
| `openclaw skills list/install/enable`                | Skills 管理        |
| `openclaw models status`                             | 模型状态             |
| `openclaw logs --follow`                             | 实时日志             |
| `openclaw message send --target ... --message "..."` | 发送测试消息           |


手册可设「命令速查表」并链接到官方 CLI 文档。

---

## 十二、现有社区教程与参考

- **Cursor IDE 博客**：  
  - Telegram / 飞书双通道配置  
  - Browser Relay 从入门到实战  
  - Telegram 配置（约 15 分钟搭建）
- **中文社区/镜像站**：  
  - open-claw.me、openclawcn.com、openclaws.io、openclawdoc.com 等有安装、配置、术语表等。
- **阿里云**：有「10 分钟部署」类教程（轻量服务器 + 百炼 API + 端口 18789）。
- **Cursor 集成**：存在「cursor-council」类 Skill（多 Cursor 并行/多模型议会），可作进阶示例。

手册可在「参考资料」中列出上述来源，便于读者延伸阅读。

---

## 十三、建议的《OpenClaw 使用手册》大纲

1. **前言与阅读说明**
  - 适用读者、环境要求、文档与社区链接。
2. **简介**
  - OpenClaw 是什么、架构概览、典型使用场景。
3. **安装与运行**
  - Node 版本、npm/pnpm 安装、一键脚本（可选）、从源码安装（可选）。  
  - 首次运行：`onboard`、`dashboard`、`gateway`。
4. **首次配置（向导）**
  - 快速开始 vs 高级、模型/认证、工作区、Gateway、渠道、守护进程、健康检查、Skills 推荐。  
  - 非交互/脚本化示例（可选小节）。
5. **AI 模型与认证**
  - 支持的模型与提供商、API Key / OAuth 配置、`models status`。
6. **渠道配置**
  - Telegram、WhatsApp、飞书（必选 2～3 个重点写），其余列名称 + 链接官方。  
  - 配对流程与 `pairing` 命令、允许列表与安全建议。
7. **Gateway 与守护进程**
  - 端口、绑定、认证、安装/启停、日志与健康检查。
8. **Skills 与浏览器 Relay**
  - Skills 安装与启用、配置要点；Browser Relay 三种模式与适用场景。
9. **多 Agent 与工作区**
  - 何时用多 Agent、`agents add/switch`、工作区路径说明。
10. **Web 控制台与日常使用**
  - 控制台功能概览、远程访问简述。
11. **配置与路径参考**
  - `openclaw.json` 与重要目录、链接官方配置参考。
12. **命令速查与故障排查**
  - 常用命令表、`doctor`/`health`/`logs`、常见错误与解决思路、FAQ 链接。
13. **参考资料与更新**
  - 官方文档、社区教程、版本与更新说明。

---

## 十四、调研小结

- **官方文档**：以 [docs.openclaw.ai](https://docs.openclaw.ai) 及 [llms.txt](https://docs.openclaw.ai/llms.txt) 为主，中文分散在 `/zh-CN/` 下，可作为手册的权威引用来源。
- **手册定位建议**：面向「从零安装到能稳定在 1～2 个渠道上用起来」的用户，以「安装 → 向导 → 渠道 → Gateway/Skills」为主线，其余用小节 + 链接方式覆盖。
- **可补充方向**：更多中文截图/步骤、国内网络与 API 注意事项、飞书/Telegram 的详细步骤、以及「与 Cursor 一起用」的简要说明（含 cursor-council 等 Skill）。

如需，我可以按上述大纲直接开始写《OpenClaw 使用手册》的某一章或全本初稿（可分批生成并保存）。