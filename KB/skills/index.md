# 常用 Skills 使用说明

[← 返回企业应用手册](../index.md)

> 按技能分篇说明：**用途**、**安装与配置**、**怎么用**，便于快速上手。技能名称（slug）以 SkillHub / ClawHub 搜索为准。

---

## 技能列表

### 飞书集成类（与飞书通道搭配使用）


| 技能                                                        | 说明                  | 使用说明                 |
| --------------------------------------------------------- | ------------------- | -------------------- |
| [feishu-interactive-cards](./feishu-interactive-cards.md) | 飞书交互卡片（按钮、表单、投票、待办） | 在飞书中发可点击的卡片，用户点选即回复  |
| [feishu-calendar-tool](./feishu-calendar-tool.md)         | 飞书日历（零配置）           | 创建、查询、删除日程，自动用现有飞书凭证 |
| [feishu-calendar](./feishu-calendar.md)                   | 飞书日历自动化             | 日程管理、空闲/忙碌查询、会议排期    |
| [feishu-api-bitable](./feishu-api-bitable.md)             | 飞书多维表格 API          | 多维表格增删改查，表/记录/字段维护   |
| [feishu-bitable-creator](./feishu-bitable-creator.md)     | 飞书多维表格创建            | 从零创建多维表格，自动建字段与记录    |


### 搜索与自动化类

| 技能 | 说明 | 使用说明 |
|------|------|----------|
| [baidu-search](./baidu-search.md) | 百度搜索 | 联网检索中文信息，需千帆 API Key |
| [multi-search-engine](./multi-search-engine.md) | 多搜索引擎 | 聚合多个搜索引擎，多源对比与召回，[ClawHub](https://clawhub.ai/gpyAngyoujun/multi-search-engine) |
| [tavily-search](./tavily-search.md) | Tavily Search | AI 优化联网搜索，需 [app.tavily.com](https://app.tavily.com) 免费 KEY，[ClawHub](https://clawhub.ai/Jacky1n7/openclaw-tavily-search) |
| [agent-browser](./agent-browser.md) | 浏览器自动化 | 控制浏览器完成填表、抓取、内网操作 |
| [cron](./cron.md) | 定时任务 | 按计划执行提醒、报表、同步等 |
| [youtube-watcher](./youtube-watcher.md) | YouTube 视频文稿 | 用 yt-dlp 拉取字幕，做摘要、问答与信息提取，[ClawHub](https://clawhub.ai/Michaelgathara/youtube-watcher) |
| [automation-workflows](./automation-workflows.md) | 自动化工作流 | 识别与设计跨工具自动化（Zapier/Make/n8n），[ClawHub](https://clawhub.ai/JK-0001/automation-workflows) |
| [nano-banana-pro](./nano-banana-pro.md) | Nano Banana Pro | 轻量增强能力，详见 ClawHub 与本地 SKILL.md，[ClawHub](https://clawhub.ai/steipete/nano-banana-pro) |

### 摘要与知识类

| 技能 | 说明 | 使用说明 |
|------|------|----------|
| [summarize](./summarize.md) | Summarize | 网页、本地文件、视频链接智能摘要，[ClawHub](https://clawhub.ai/kn70pywhg0fyz996kpa8xj89s57yhv26/summarize) |
| [ontology](./ontology.md) | Ontology | 类型化知识图谱，结构化记忆与关系查询，[ClawHub](https://clawhub.ai/oswalpalash/ontology) |
| [self-improving-agent](./self-improving-agent.md) | Self-improving Agent | 从错误与纠正中学习，沉淀 LEARNINGS/ERRORS 等，[ClawHub](https://clawhub.ai/pskoett/self-improving-agent)；若提示问题需强制安装并自行解决 |

### 安全与质量类

| 技能 | 说明 | 使用说明 |
|------|------|----------|
| [skill-vetter](./skill-vetter.md) | 技能安全审查 | 安装前扫描技能安全与合规风险 |
| [skill-vetter-spclaudehome](./skill-vetter-spclaudehome.md) | Skill Vetter（安全评估） | 安全评估与测评，[ClawHub](https://clawhub.ai/spclaudehome/skill-vetter) |


---

## 安装前提醒

- **凭证**：飞书类技能多与 OpenClaw 飞书通道共用 `~/.openclaw/openclaw.json` 中的 `channels.feishu` 凭证。
- **权限**：飞书开放平台中需为应用申请对应权限（日历、多维表格、卡片等），见各篇「配置」。
- **国内访问**：优先用 [SkillHub](https://skillhub-1251783334.cos.ap-guangzhou.myqcloud.com) 或 ClawdHub 安装，减少境外源超时。

