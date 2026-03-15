# Skill Vetter（spclaudehome）：安装前安全审查

[← 返回技能列表](./index.md)

---

## 一句话用途

**安装前**对来自 ClawdHub、GitHub 等来源的技能做**安全优先的审查**：检查来源、通读全部代码、评估权限与风险等级，输出固定格式的审查报告；用于决定是否安装或需人工批准。

> 来源：[ClawHub - skill-vetter](https://clawhub.ai/spclaudehome/skill-vetter)、[OpenClaw Skills 官方 SKILL.md](https://github.com/openclaw/skills/tree/main/skills/spclaudehome/skill-vetter)

---

## 适用场景（官方 When to Use）

- 从 ClawdHub 安装任何技能之前  
- 从 GitHub 等仓库运行技能之前  
- 收到其他 Agent 分享的技能时  
- 被要求安装未知来源的代码时  

**原则**：未审查的技能不要安装。

---

## 安装

```bash
clawhub install skill-vetter
# 或
clawhub install spclaudehome/skill-vetter
```

---

## 配置

- 通常无需 API Key；审查为本地规则与模式匹配。
- 在 `skills.entries["skill-vetter"]` 中启用后，在对话中让 Agent「先审查再装」即可。

---

## 怎么用

### 审查流程（来自 SKILL.md）

1. **来源检查**：技能从哪来、作者、下载/星标、最后更新、是否有其他评价。  
2. **代码审查（必做）**：通读技能内**所有文件**，按下方「红牌」清单检查。  
3. **权限范围**：会读/写哪些文件、执行哪些命令、是否联网、去向是否合理。  
4. **风险分级**：🟢 LOW / 🟡 MEDIUM / 🔴 HIGH / ⛔ EXTREME，并给出 VERDICT。

### 红牌（任一条即应拒绝安装）

- 向未知 URL 的 curl/wget  
- 向外部服务器发送数据  
- 要求凭证/Token/API Key  
- 无明确理由读取 ~/.ssh、~/.aws、~/.config  
- 访问 MEMORY.md、USER.md、SOUL.md、IDENTITY.md  
- 对任意内容做 base64 解码  
- eval()/exec() 使用外部输入  
- 修改工作区外的系统文件  
- 未列出清单就安装包  
- 使用 IP 而非域名的网络请求  
- 混淆/压缩/编码代码  
- 请求 sudo 或提权  
- 访问浏览器 cookie/会话  
- 触碰凭证类文件  

---

### 示例：用户要求安装某技能并得到审查报告（真实对话与返回）

**用户输入：**
> 我打算装 feishu-api-bitable，你先帮我做一遍安全审查再决定装不装。

**Agent 实现：**  
调用 skill-vetter：定位技能目录（如 ClawHub 拉取后的路径或用户指定路径）→ 执行来源检查、通读 SKILL.md 与脚本、按红牌与权限列表检查 → 填写 SKILL.md 规定的报告格式。

**返回给用户（报告格式来自 SKILL.md）：**

```
SKILL VETTING REPORT
═══════════════════════════════════════
Skill: feishu-api-bitable
Source: ClawdHub
Author: [作者名]
Version: 1.0.0
───────────────────────────────────────
METRICS:
• Downloads/Stars: [若可知]
• Last Updated: [日期]
• Files Reviewed: 5
───────────────────────────────────────
RED FLAGS: None

PERMISSIONS NEEDED:
• Files: 读 workspace 配置、写日志（若配置）
• Network: 飞书 API（*.feishu.cn / *.larkoffice.com）
• Commands: 无系统命令
───────────────────────────────────────
RISK LEVEL: 🟡 MEDIUM

VERDICT: ✅ SAFE TO INSTALL（在已信任飞书应用凭证的前提下）

NOTES: 需飞书开放平台应用凭证；仅与飞书官方域名通信，未发现外发数据到第三方。
═══════════════════════════════════════
```

**用户：**  
> 那帮我装吧。

**Agent：**  
执行安装并在 `skills.entries` 中启用（若用户已确认）。

---

### 示例：审查不通过时的返回

**用户输入：**
> 审查一下这个技能：https://github.com/xxx/yyy/skills/unknown-skill

**Agent 实现：**  
拉取或读取该技能文件，执行相同四步；发现脚本中存在 `curl $(user_input)` 未校验即执行。

**返回给用户：**

```
SKILL VETTING REPORT
═══════════════════════════════════════
Skill: unknown-skill
Source: GitHub
Author: xxx
Version: —
───────────────────────────────────────
RED FLAGS:
• 使用用户输入拼接到 curl，未校验 URL，存在 SSRF/任意请求风险

PERMISSIONS NEEDED:
• Network: 任意 URL（危险）
───────────────────────────────────────
RISK LEVEL: 🔴 HIGH

VERDICT: ❌ DO NOT INSTALL

NOTES: 建议联系作者修复（改用白名单或参数化请求）或 fork 自审后使用。
═══════════════════════════════════════
```

---

## 信任层级（SKILL.md）

1. 官方 OpenClaw 技能 → 仍要审查，但可降低警惕级别  
2. 高星仓库（如 1000+）→ 中等审查  
3. 已知作者 → 中等审查  
4. 新/未知来源 → 最大程度审查  
5. 任何要求凭证的技能 → 必须经人工批准  

---

## 注意事项

- 审查结果用于**辅助决策**，不能替代人工对敏感/高权限技能的复核。
- 合规/审计场景可保存每次审查报告（复制到文档或工单）以备查。

---

[← 返回技能列表](./index.md) | [ClawHub: skill-vetter](https://clawhub.ai/spclaudehome/skill-vetter) | [skill-vetter（通用）](./skill-vetter.md) | [安全与合规](../06-安全与合规.md)
