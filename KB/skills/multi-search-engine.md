# Multi-search-engine：多搜索引擎聚合（免 API Key）

[← 返回技能列表](./index.md)

---

## 一句话用途

**聚合多个搜索引擎**的检索结果，一次查询可走多个引擎（中文 + 国际），**无需为各引擎单独配置 API Key**；支持高级运算符、时间过滤和隐私向引擎，适合多源对比、提高召回与中英市场调研。

> 来源：[ClawHub - multi-search-engine](https://clawhub.ai/gpyAngyoujun/multi-search-engine)、[OpenClaw 技能描述](https://useclaw.pro/skills/multi-search-engine/)、[SkillAvatars](https://www.skillavatars.com/skills/multi-search-engine)

---

## 适用场景

- **多源对比**：同一问题用不同引擎查，对比结果、减少单一源偏差。  
- **提高召回**：小众或冷门内容由多引擎补足。  
- **中英市场**：同时查中文引擎（百度、必应中国、360、搜狗等）与全球引擎（Google、DuckDuckGo、Brave 等）。  
- **隐私向检索**：使用 DuckDuckGo、Startpage、Brave、Qwant 等。  
- **知识/计算**：WolframAlpha 支持数学、单位换算、股票、天气等。  

---

## 安装

```bash
clawhub install multi-search-engine
# 或（以 ClawHub 页面为准）：
# clawhub install gpyAngyoujun/multi-search-engine
```

国内用户可优先：`skillhub install multi-search-engine`；若镜像未收录，需网络可达 ClawHub 时安装。

---

## 配置

- 技能**不要求各引擎的 API Key**（与 Tavily、部分百度 API 等不同），安装并启用即可使用。  
- 若技能支持「仅启用部分引擎」，在配置或环境中指定引擎列表；具体以 SKILL.md 为准。

---

## 能力概览（来自公开描述）

- **约 17 个引擎**：8 个中文（如百度、必应中国、360、搜狗、微信、头条、集思录、Bing INT）+ 9 个国际（Google、Google HK、DuckDuckGo、Yahoo、Startpage、Brave、Ecosia、Qwant、WolframAlpha）。  
- **高级运算符**：site:、filetype:、精确匹配、排除、OR 等。  
- **时间过滤**：过去 1 小时/天/周/月/年。  
- **DuckDuckGo Bangs**：如 !g、!gh、!so、!w、!yt 等快捷方式。  
- **安全**：低风险、仅需网络权限，遵循最小权限；曾有安全审计与信任分记录（以当时为准）。

---

## 怎么用

### 示例 1：多源对比同一话题（真实对话与返回）

**用户输入：**

> OpenClaw 在国内和国外分别有哪些报道？各找 2 条。

**Agent 实现：**  
调用 multi-search-engine，分别用「OpenClaw 国内」「OpenClaw 国外」或中英关键词在已启用引擎中检索，合并结果后按来源分类、去重，取国内外各约 2 条。

**返回给用户（示例）：**

> **国内来源（2 条）：**  
> 
> 1. 《OpenClaw 与飞书集成实践》— 某技术博客，摘要：介绍如何在企业内用 OpenClaw 接飞书机器人…  
> 2. 《开源 Agent 框架 OpenClaw 入门》— 某社区，摘要：从安装到第一个技能…  
> 
> **国外来源（2 条）：**  
> 
> 1. "OpenClaw: Build AI agents that use tools" — docs.openclaw.ai，摘要：Official docs for skills and channels…  
> 2. "OpenClaw on GitHub" — github.com/openclaw，摘要：Repository and releases…  
> 
> 以上结果来自多个搜索引擎汇总，如需可点链接查看原文。

（实际条目与摘要以技能返回为准，通常包含标题、链接、摘要片段。）

---

### 示例 2：提高召回查小众项目（真实对话与返回）

**用户输入：**

> 某某小众开源项目最近有没有更新？多查几个来源。

**Agent 实现：**  
用项目名 + “release”“changelog”“更新”等关键词调用 multi-search-engine，从多引擎结果中筛选发布时间、release 说明等。

**返回给用户（示例）：**

> 从多个引擎查到该项目的近期动态：  
> 
> - **GitHub**：最新 release v0.2.1，约 2 周前，修复了 XX 问题。  
> - **博客**：作者上月在个人博客写了「v0.2 路线图」。  
> - **论坛**：某社区有一篇「从 0.1 升级到 0.2 的注意事项」。  
>   链接：[GitHub releases 链接]、[博客链接]、[论坛链接]

---

### 示例 3：企业仅用允许的引擎（真实对话与返回）

**用户输入：**  
（在已配置「仅用引擎 A、B」的环境中）  

> 查一下我们公司名在行业内的最新舆情。

**Agent 实现：**  
multi-search-engine 按配置只向引擎 A、B 发起请求，返回仅来自这两类源的结果；Agent 归纳为舆情概览并标注来源合规。

**返回给用户（示例）：**

> **舆情概览（来源：仅用已批准的搜索引擎）：**  
> 近期与「XX 公司」相关的内容主要有：产品发布、某展会报道、一篇第三方评测。未发现负面舆情。  
> 详细链接与摘要已按来源列出，均来自合规引擎。

---

## 注意事项

- **用量与限流**：多引擎即多请求，注意各站点的访问频率与条款；技能不消耗 Tavily/百度等 API 额度，但可能受目标站点限制。  
- **国内可用性**：部分国际引擎在国内可能不可达，可主要依赖中文引擎或企业允许的代理策略。

---

[← 返回技能列表](./index.md) | [ClawHub: multi-search-engine](https://clawhub.ai/gpyAngyoujun/multi-search-engine) | [Tavily Search](./tavily-search.md) | [百度搜索](./baidu-search.md)
