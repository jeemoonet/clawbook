# Tavily Search：AI 优化联网搜索

[← 返回技能列表](./index.md)

---

## 一句话用途

通过 **Tavily Search API** 做**为 AI 优化的联网搜索**：返回清洗后的摘要、可抽取的答案和结构化结果（如 JSON），一次调用即可得到适合大模型使用的检索内容，比裸网页 HTML 更干净、更易用。

> 参考：[ClawHub - openclaw-tavily-search](https://clawhub.ai/Jacky1n7/openclaw-tavily-search)

---

## 适用场景

- **实时信息**：新闻、事实、产品说明、技术文档等需要最新或权威来源的问答。  
- **高质量摘要**：Tavily 对结果做提取与结构化，便于 Agent 直接组织成回复。  
- **深度与过滤**：支持不同搜索深度（如 basic / fast / advanced）及按域名、时间筛选，适合企业或深度调研。

---

## 安装

```bash
clawhub install openclaw-tavily-search
# 或按 ClawHub 页面 slug：
# clawhub install tavily-search
```

---

## 获取免费 API Key（必做）

Tavily 提供**免费额度**（如每月约 1000 次），适合个人或小团队。

1. 打开 **https://app.tavily.com**  
2. 注册/登录  
3. 在控制台或 Dashboard 中复制 **API Key**（常以 `tvly-` 开头）

---

## 配置

在 `~/.openclaw/openclaw.json` 的 `skills.entries` 中为该技能配置 API Key，例如：

```json5
{
  skills: {
    entries: {
      "tavily-search": {
        enabled: true,
        env: {
          TAVILY_API_KEY: "tvly-你的密钥",
        },
      },
    },
  },
}
```

具体键名以技能 SKILL.md 为准；修改后新会话生效。

---

## 怎么用

### 示例 1：公司/产品介绍（真实对话与返回）

**用户输入：**
> Tavily 这家公司是做什么的？

**Agent 实现：**  
识别为事实类查询 → 调用 Tavily Search，传入查询 "Tavily company what does it do"（或等价中文）→ 技能请求 Tavily API，得到清洗后的结果。

**返回给用户（示例，基于典型 API 结构）：**
> **Tavily** 是一家面向 AI 的搜索 API 服务商，主要为 LLM 和 Agent 提供「为 AI 优化」的联网搜索：返回清洗后的摘要、可抽取的答案和结构化结果，而不是原始 HTML，方便模型直接使用。  
> 官网：https://tavily.com（具体链接以 API 返回为准）

（API 常见字段包括：title、url、content/snippet、有时含 answer；Agent 据此组织成上述自然语言。）

---

### 示例 2：当日新闻汇总（真实对话与返回）

**用户输入：**
> 查一下今天 AI 领域的重要新闻。

**Agent 实现：**  
解析为「实时、新闻、AI」→ 调用 Tavily Search，可选新闻类或时间过滤 → 根据返回条目整理成简短列表。

**返回给用户（示例）：**
> **今日 AI 要闻（来源：Tavily Search）：**  
> 1. [某巨头发布新模型] — 摘要片段…，链接：…  
> 2. [某国 AI 监管新进展] — 摘要片段…，链接：…  
> 3. [某开源项目重大更新] — 摘要片段…，链接：…  
> 以上为今日检索结果，点击链接可查看全文。

---

### 示例 3：精确事实查询（真实对话与返回）

**用户输入：**
> 某某开源项目最新的 release 版本号是多少？

**Agent 实现：**  
识别为「精确版本号」→ 调用 Tavily Search（可选较深深度以命中 GitHub/发布页）→ 从返回中抽取版本号、发布日期。

**返回给用户（示例）：**
> 某某项目**最新版本为 v1.2.3**，发布于 2025 年 3 月 10 日。发布说明：修复了 XX、新增 YY。  
> 发布页：https://github.com/xxx/releases/tag/v1.2.3

---

## 注意事项

- **网络**：Tavily 为境外服务，国内直连可能较慢或需网络条件；企业需评估合规与可达性。  
- **密钥安全**：勿将 API Key 提交到代码库或公开文档，仅放在配置或环境变量中。

---

[← 返回技能列表](./index.md) | [ClawHub: openclaw-tavily-search](https://clawhub.ai/Jacky1n7/openclaw-tavily-search) | [Multi-search-engine](./multi-search-engine.md) | [百度搜索](./baidu-search.md)
