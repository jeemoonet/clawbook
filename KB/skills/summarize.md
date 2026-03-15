# Summarize：多源内容摘要

[← 返回技能列表](./index.md)

---

## 一句话用途

通过 **summarize CLI** 对**网址、本地文件、YouTube 链接、音频**等内容做摘要；支持多种长度（TLDR / brief / standard / extended）和输出格式（纯文本、JSON、仅抽取），便于快速把握长内容要点。

> 来源：[ClawHub - summarize](https://clawhub.ai/kn70pywhg0fyz996kpa8xj89s57yhv26/summarize)、[OpenClaw Skills 相关描述](https://openclawskills.best/skills/sakaen736jih/summarize-kx5u/)

---

## 适用场景

- **网页**：发链接，得到页面核心内容摘要。  
- **本地文件**：PDF、文档等，生成要点或短摘要。  
- **YouTube**：视频链接 + `--youtube auto`，依赖字幕/转写做摘要。  
- **被屏蔽站点**：可选 Firecrawl 做抓取兜底。  

---

## 安装

```bash
clawhub install summarize
# 或按 ClawHub 页面 slug（可能含作者）：
# clawhub install kn70pywhg0fyz996kpa8xj89s57yhv26/summarize
```

---

## 配置

- **LLM**：需配置所选模型的 API Key（如 `GEMINI_API_KEY`、`OPENAI_API_KEY` 等）；默认示例为 `google/gemini-3-flash-preview`。  
- **可选**：`FIRECRAWL_API_KEY`、`APIFY_API_TOKEN` 用于难抓取页面与 YouTube。  
- 在 `~/.openclaw/openclaw.json` 的 `skills.entries["summarize"]` 下配置 `env` 或 `apiKey`。

---

## 怎么用

### 典型用法（来自官方 Quick Start）

```bash
summarize "https://example.com" --model google/gemini-3-flash-preview
summarize "/path/to/file.pdf" --model google/gemini-3-flash-preview
summarize "https://youtu.be/dQw4w9WgXcQ" --youtube auto
```

输出可选：TLDR / brief / standard / extended 或按 token 限制；格式可为纯文本或 JSON。

---

### 示例 1：网页链接摘要（真实对话与返回）

**用户输入：**
> 帮我总结一下这个链接：https://docs.openclaw.ai/getting-started

**Agent 实现：**  
调用 summarize 技能，传入该 URL 及默认或用户指定的长度（如 brief）。

**返回给用户（示例摘要，实际以 API 输出为准）：**
> **摘要（brief）：**  
> OpenClaw 入门页介绍了如何安装 CLI、创建应用、连接飞书等通道。主要步骤包括：安装 openclaw、用 `openclaw init` 初始化、在开放平台创建应用并配置凭证。文档还说明了如何用 `openclaw run` 启动网关并与机器人对话。  
>  
> 详细内容可查看：https://docs.openclaw.ai/getting-started

---

### 示例 2：本地 PDF 要点（真实对话与返回）

**用户输入：**
> 把这份 PDF 的要点列出来：/workspace/docs/需求说明.pdf

**Agent 实现：**  
调用 summarize，传入文件路径，并指定输出为要点列表（若技能支持 `--bullets` 或类似参数）。

**返回给用户（示例）：**
> **《需求说明》要点：**  
> 1. 项目目标：Q2 完成客户门户 MVP。  
> 2. 核心功能：登录、工单提交、进度查询、文件上传。  
> 3. 非功能：响应时间 &lt; 2s，支持 Chrome/Safari 最新版。  
> 4. 里程碑：4 月原型评审，5 月内测，6 月上线。  
> 5. 风险：依赖第三方 SSO，需提前对接。

---

### 示例 3：YouTube 视频摘要（真实对话与返回）

**用户输入：**
> 这个视频讲了啥？https://youtu.be/dQw4w9WgXcQ

**Agent 实现：**  
调用 summarize，传入该链接并启用 YouTube 模式（如 `--youtube auto`）；技能通过字幕/转写生成摘要。

**返回给用户（示例）：**
> **视频摘要：**  
> 该视频为一段约 3 分钟的歌曲 MV，以歌词和画面为主。若需更准确的内容概括，可提供带字幕的链接或说明具体关注点（如「只要技术要点」）。

（实际输出取决于技能与 Apify/转写能力；无字幕时可能报错或提示无法摘要。）

---

## 注意事项

- **本地文件**：确保 Agent 有权限读该路径；企业环境勿暴露敏感目录。  
- **网页/视频**：部分站点反爬或需登录，可能失败；视频依赖字幕或第三方转写及配额。  
- **版权与合规**：仅对有权使用的内容做摘要。

---

[← 返回技能列表](./index.md) | [ClawHub: summarize](https://clawhub.ai/kn70pywhg0fyz996kpa8xj89s57yhv26/summarize) | [Ontology](./ontology.md)
