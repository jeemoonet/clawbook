# Self-improving Agent：从错误与纠正中持续学习

[← 返回技能列表](./index.md)

---

## 一句话用途

把**命令失败、用户纠正、知识缺口、最佳实践、功能建议**记录到工作区的 `.learnings/` 下（LEARNINGS.md、ERRORS.md、FEATURE_REQUESTS.md），并可提升到 CLAUDE.md、AGENTS.md、TOOLS.md、SOUL.md；重要学习在会话开始前可被提醒回顾，实现持续改进。

> 来源：[ClawHub - self-improving-agent](https://clawhub.ai/pskoett/self-improving-agent)、[OpenClaw Skills 官方 SKILL.md](https://github.com/openclaw/skills/tree/main/skills/pskoett/self-improving-agent)

---

## 适用场景（官方 Quick Reference）

| 情况 | 动作 |
|------|------|
| 命令/操作失败 | 记入 `.learnings/ERRORS.md` |
| 用户纠正你（"No, that's wrong...", "Actually..."） | 记入 `.learnings/LEARNINGS.md`，category：correction |
| 用户提出当前没有的能力 | 记入 `.learnings/FEATURE_REQUESTS.md` |
| 外部 API/工具失败 | 记入 `.learnings/ERRORS.md`，附集成信息 |
| 发现知识过时/错误 | 记入 `.learnings/LEARNINGS.md`，category：knowledge_gap |
| 发现更好做法 | 记入 `.learnings/LEARNINGS.md`，category：best_practice |
| 重大任务前 | 建议先回顾 `.learnings/` 中相关内容 |

---

## 安装

```bash
clawdhub install self-improving-agent
# 或
clawhub install self-improving-agent
```

手动：`git clone https://github.com/peterskoett/self-improving-agent.git ~/.openclaw/skills/self-improving-agent`

创建学习目录与文件：

```bash
mkdir -p ~/.openclaw/workspace/.learnings
# 从技能 assets/ 复制或新建：LEARNINGS.md、ERRORS.md、FEATURE_REQUESTS.md
```

---

## 配置

- 学习文件位于 **OpenClaw 工作区**（如 `~/.openclaw/workspace/.learnings/`），需可写。
- 可选：启用 hook，在每次 UserPromptSubmit 或 PostToolUse(Bash) 后提醒记录错误/学习（见 SKILL.md 的 Hook Integration）。

---

## 怎么用

### 示例 1：命令失败 → 记入 ERRORS.md（真实对话与文件内容）

**用户输入：**
> 帮我在项目里执行 git push origin main。

**Agent 实现：**  
执行 `git push origin main`，因未配置远程认证或分支保护导致失败。

**Agent 返回给用户：**
> 推送失败了，错误信息是：`remote: Permission denied (publickey). fatal: Could not read from remote repository.` 我已把这次失败记到 `.learnings/ERRORS.md`，下次会先提醒你确认本机已配置 SSH 或 HTTPS 凭证再执行推送。

**ERRORS.md 中实际追加的内容（格式来自 SKILL.md）：**

```markdown
## [ERR-20250314-001] git_push

**Logged**: 2025-03-14T10:30:00Z
**Priority**: high
**Status**: pending
**Area**: config

### Summary
git push origin main 失败，远程拒绝（权限/认证）

### Error
```
remote: Permission denied (publickey).
fatal: Could not read from remote repository.
```

### Context
- 命令：git push origin main
- 环境：本机 Git，未配置 SSH key 或 HTTPS token

### Suggested Fix
在仓库配置 SSH key 或使用 HTTPS + 个人访问令牌后再执行 push。

### Metadata
- Reproducible: yes
- Related Files: .git/config

---
```

---

### 示例 2：用户纠正 → 记入 LEARNINGS.md（真实对话与文件内容）

**用户输入：**
> 你刚才说的不对，我们公司请假审批是在 OA 里提交，不是飞书审批。

**Agent 实现：**  
识别为「用户纠正」→ 写入 `.learnings/LEARNINGS.md`，category：correction。

**Agent 返回给用户：**
> 已记下：请假审批在 OA 提交，不用飞书审批。之后有人问请假流程我会按这个回答。

**LEARNINGS.md 中实际追加的内容：**

```markdown
## [LRN-20250314-002] correction

**Logged**: 2025-03-14T10:35:00Z
**Priority**: medium
**Status**: pending
**Area**: docs

### Summary
请假审批流程：在本公司走 OA，不是飞书审批。

### Details
用户纠正：公司请假审批在 OA 里提交，不是飞书审批。此前回答错误。

### Suggested Action
回答「请假怎么走」时，统一说「在 OA 提交请假审批」。

### Metadata
- Source: user_feedback
- Tags: 审批, OA, 飞书

---
```

---

### 示例 3：功能建议 → 记入 FEATURE_REQUESTS.md（真实对话与文件内容）

**用户输入：**
> 希望你在回复里自动附带相关文档链接，方便我们点开看。

**Agent 实现：**  
识别为功能需求 → 写入 `.learnings/FEATURE_REQUESTS.md`。

**Agent 返回给用户：**
> 已记录到功能需求：回复时自动附带相关文档链接。后续版本会考虑。

**FEATURE_REQUESTS.md 中实际追加的内容：**

```markdown
## [FEAT-20250314-001] 回复附带文档链接

**Logged**: 2025-03-14T10:40:00Z
**Priority**: medium
**Status**: pending
**Area**: frontend

### Requested Capability
在回复中自动附带相关文档链接，便于用户点击查看。

### User Context
希望不用再单独要链接，直接在答案里可点开。

### Complexity Estimate
medium

### Suggested Implementation
在生成回复时识别引用的文档/页面，在文末或句中插入可点击链接（如飞书文档链接）。

### Metadata
- Frequency: first_time

---
```

---

## 提升到项目记忆（Promotion）

当某条学习具有普遍适用性时，可提升到：

- **CLAUDE.md**：项目事实与约定  
- **AGENTS.md**：工作流与自动化（OpenClaw 工作区）  
- **TOOLS.md**：工具用法与坑点（OpenClaw 工作区）  
- **SOUL.md**：行为与原则（OpenClaw 工作区）

并在原条目中把 `**Status**: pending` 改为 `**Status**: promoted`，并注明 `**Promoted**: CLAUDE.md` 等。

---

## 注意事项

- 若安装或运行报错，请按提示安装依赖、检查工作区路径与权限；必要时查看仓库 README / SKILL.md、Issues。
- 学习文件可能包含对话片段，企业环境注意不写敏感信息，并限制 `.learnings/` 访问。

---

[← 返回技能列表](./index.md) | [ClawHub: self-improving-agent](https://clawhub.ai/pskoett/self-improving-agent) | [Ontology](./ontology.md)
