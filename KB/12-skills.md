# Skills 获取与安装

[← 返回首页](./index.md)

> 本文**仅介绍在中国国内能正常访问和使用的企业级 Skills**：获取方式以国内镜像与中文社区为主，推荐技能均为不依赖境外服务或可在国内直连的企业场景能力。

---

## 国内获取 Skills 的途径

### 1. SkillHub（腾讯云国内镜像，推荐）

**SkillHub** 是面向国内用户的 OpenClaw 技能加速方案，基于官方 ClawHub 生态，提供：

- **国内节点镜像**：从腾讯云国内节点拉取，避免访问境外源时的限速与超时
- **中文界面与分类**：中文搜索、简介与分类，便于筛选
- **精选榜单**：Top 50 等精选技能，便于企业选型
- **自动回退**：若某技能镜像未收录，可回退到官方源（需网络可达时）

安装 SkillHub CLI 后，用 `skillhub install <技能名>` 安装，优先走国内镜像。

```bash
# 安装 SkillHub CLI（一键脚本）
curl -fsSL https://skillhub-1251783334.cos.ap-guangzhou.myqcloud.com/install/install.sh | sh

# 验证
skillhub -h

# 安装指定技能
skillhub install <skill-name>
```

具体文档与技能列表以腾讯云 SkillHub 官方页面为准。

### 2. ClawdHub 中文社区

**ClawdHub** 为 OpenClaw 中文社区的技能与工具仓库，侧重国内办公与协作场景：

- 飞书（文档、多维表格、日历等）
- 钉钉、企业微信等国内办公平台集成
- 技能发布与社区分发

可通过社区站点或文档了解技能名称与安装方式，再通过 `clawhub install`（若网络可达）或手动将技能目录放到 `~/.openclaw/skills/` 或工作区 `skills/` 使用。

### 3. 手动安装（完全离线/内网可用）

不依赖任何外网技能库时，可将技能包（含 `SKILL.md` 的目录）直接放到本地：

- **本机共享**：`~/.openclaw/skills/<技能名>/`
- **当前工作区**：`<工作区路径>/skills/<技能名>/`

优先级：工作区 `skills` > `~/.openclaw/skills` > 内置技能。适合企业内网或已有技能包分发的场景。

### 4. ClawHub 官方（可选）

官方 ClawHub（如 [clawhub.ai](https://clawhub.ai/)）需境外网络可达。国内用户建议优先使用 **SkillHub** 或 **ClawdHub**；若网络条件允许，也可使用 `clawhub` CLI 搜索与安装。

```bash
npm i -g clawhub
clawhub search "关键词"
clawhub install <slug>
```

---

## 安装后的配置与检查

### 在 OpenClaw 中查看技能

```bash
openclaw skills list
openclaw skills list --eligible
openclaw skills info <技能名>
openclaw skills check
```

### 启用与配置（openclaw.json）

在 `~/.openclaw/openclaw.json` 的 `skills.entries` 下按技能名配置 API Key、开关等：

```json5
{
  skills: {
    entries: {
      "某技能名": {
        enabled: true,
        apiKey: { source: "env", provider: "default", id: "XXX_API_KEY" },
      },
    },
  },
}
```

修改后新会话生效。

### 更新已安装技能

- 使用 **SkillHub** 安装的：按 SkillHub 文档或 `skillhub update`（若有）更新
- 使用 **clawhub** 安装的：`clawhub update --all` 或 `clawhub update <slug>`（需网络可达官方源）

---

## 安全注意

- 第三方技能视为**不可信代码**：安装前阅读其 `SKILL.md` 与脚本，再启用。
- 技能会获得你配置的 `env`/`apiKey`，勿将敏感信息写入日志或提示词。
- 企业环境请结合 [安全](./10-安全.md) 与 [企业应用](./11-企业应用.md) 做访问控制与审计。

---

## 国内可用的企业级 Skills 推荐（10 个）

以下均为**在国内能访问或运行**的企业级能力（不依赖境外服务，或使用国内 API/镜像）。具体技能名称（slug）以你使用的技能源（SkillHub、ClawdHub、ClawHub）搜索为准。


| 序号  | 技能类型 / 名称示例                         | 典型用途             | 国内可用说明                                        |
| --- | ----------------------------------- | ---------------- | --------------------------------------------- |
| 1   | **飞书文档 / 多维表格 / 日历**                | 飞书文档、多维表格、日程读写   | 飞书为国内服务，与本文飞书通道一致；可在 ClawdHub/ SkillHub 搜「飞书」 |
| 2   | **钉钉**                              | 钉钉消息、审批、日程、群组    | 钉钉为国内服务；国内社区有对应集成技能                           |
| 3   | **企业微信**                            | 企微消息、应用、通讯录      | 企业微信为国内服务；国内社区有对应集成技能                         |
| 4   | **百度搜索（baidu-search）**              | 联网搜索、中文信息检索      | 使用百度千帆 API，国内直连；需配置百度千帆 API Key               |
| 5   | **浏览器自动化（agent-browser / browser）** | 内网系统、报表页、填表、抓取   | 本地 CDP，不依赖境外服务；注意权限与 [安全](./10-安全.md)         |
| 6   | **cron**                            | 定时任务、周期报表、提醒     | 在网关上本地执行，无境外依赖                                |
| 7   | **知识库 / 语雀 / 飞书知识库**                | 内部文档与知识库问答       | 语雀、飞书知识库等为国内服务；具体 slug 以技能源搜索「语雀」「飞书」「知识库」    |
| 8   | **安全审查（skill-vetter）**              | 安装前技能安全扫描        | 本地或通用逻辑，适合在安装第三方技能前做审查                        |
| 9   | **百度千帆系列能力**                        | 搜索、文档、知识库等千帆开放能力 | 百度智能云千帆在国内；千帆已有多款官方 Skills 登陆 OpenClaw，可按需选用  |
| 10  | **国内邮件 / 企业邮箱**                     | 企业邮箱收发、审批与工单     | 若技能源提供网易、腾讯企业邮等国内邮箱集成，可在内网或国内 API 下使用         |


### 使用建议

- **优先国内源**：用 SkillHub 或 ClawdHub 搜索「飞书」「钉钉」「企业微信」「百度」「搜索」「知识库」等关键词，再安装对应 slug。
- **API 与密钥**：百度千帆、飞书、钉钉、企微等均需在对应开放平台申请应用并配置 API Key 或 OAuth，仅在内网或国内控制的数据中心使用。
- **按需启用**：在 `skills.entries` 中只启用需要的技能，减少暴露面与 token 占用；结合 [企业应用](./11-企业应用.md) 做多 Agent 与允许列表。

---

## 常用Skills

1. Multi-search-engine， [https://clawhub.ai/gpyAngyoujun/multi-search-engine](https://clawhub.ai/gpyAngyoujun/multi-search-engine)
2. Tavily Search：[https://clawhub.ai/Jacky1n7/openclaw-tavily-search，](https://clawhub.ai/Jacky1n7/openclaw-tavily-search，) 需要登录[https://app.tavily.com](https://app.tavily.com/home)获取免费KEY
3. Ontology：[https://clawhub.ai/oswalpalash/ontology](https://clawhub.ai/oswalpalash/ontology)
4. Summarize：[https://clawhub.ai/kn70pywhg0fyz996kpa8xj89s57yhv26/summarize](https://clawhub.ai/kn70pywhg0fyz996kpa8xj89s57yhv26/summarize) , 需要gemini key
5. Self improment：[https://clawhub.ai/pskoett/self-improving-agent](https://clawhub.ai/pskoett/self-improving-agent) ，如果提示问题，强制要求安装，自行解决相关问题
6. Skills Vetter：[https://clawhub.ai/spclaudehome/skill-vetter](https://clawhub.ai/spclaudehome/skill-vetter)，安全评估和测评
7. **Youtube watcher**：[使用说明](./skills/youtube-watcher.md) | [ClawHub](https://clawhub.ai/Michaelgathara/youtube-watcher) — 获取 YouTube 视频字幕/文稿（yt-dlp），用于摘要、问答与信息提取；需安装 yt-dlp。
8. **Automation workflows**：[使用说明](./skills/automation-workflows.md) | [ClawHub](https://clawhub.ai/JK-0001/automation-workflows) — 设计与落地跨工具自动化工作流（Zapier、Make、n8n）。
9. **Nano Banana Pro**：[使用说明](./skills/nano-banana-pro.md) | [ClawHub](https://clawhub.ai/steipete/nano-banana-pro) — 详见 ClawHub 与本地 SKILL.md。

---

## 相关链接

- [OpenClaw Skills 文档](https://docs.openclaw.ai/tools/skills)
- [Skills 配置参考](https://docs.openclaw.ai/tools/skills-config)
- 腾讯云 SkillHub / 腾讯云开发者社区（安装与镜像说明）
- ClawdHub 中文社区（飞书/钉钉/企微等国内技能）
- 百度智能云千帆（百度搜索等官方 Skills）

---

[← 返回首页](./index.md) | [企业应用](./11-企业应用.md) | [安全](./10-安全.md)