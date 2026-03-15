# Automation workflows：自动化工作流设计与落地

[← 返回技能列表](./index.md)

---

## 一句话用途

帮助**识别可自动化的事务**、**设计跨工具工作流**并**选型与落地**（Zapier、Make、n8n），用无代码/低代码减少重复劳动；输出为可执行的步骤、工具选型与设计模板（触发 → 条件 → 动作 → 错误处理），而非代你在平台上点鼠标。

> 来源：[ClawHub - automation-workflows](https://clawhub.ai/JK-0001/automation-workflows)、[automation-workflows SKILL](https://clawhub.ai/JK-0001/automation-workflows)

---

## 适用场景

- **发现自动化机会**：梳理日常重复任务（报表、表单汇总、通知、数据同步），判断是否适合触发器+动作。
- **设计与选型**：在 Zapier、Make、n8n 间选择，并设计完整流程。
- **实施与维护**：按步骤搭建、测试、排错，并设错误通知与简单文档。

与 **cron** 区别：cron 按时间执行；本技能侧重**事件驱动**（表单提交、支付到账、新邮件）和**多工具串联**。

---

## 安装

```bash
clawhub install automation-workflows
# 或
skillhub install automation-workflows
```

具体 slug 以 [ClawHub 页面](https://clawhub.ai/JK-0001/automation-workflows) 为准。

---

## 配置

- 本技能为**方法指导**，通常无需在 `openclaw.json` 配置 API Key。
- 实际使用的 Zapier/Make/n8n 需在各自平台注册并配置 OAuth、Webhook 等；n8n 自建需部署与鉴权。

---

## 怎么用

### 示例 1：表单提交自动入表并通知（真实对话与返回）

**用户输入：**
> 我们有个报名表单，希望每次有人提交就自动加到一张表里，并往飞书群发一条提醒。

**Agent 实现：**  
按技能中的工作流设计模板，给出：TRIGGER、CONDITIONS、ACTIONS、ERROR HANDLING，并推荐工具与字段映射要点。

**返回给用户（示例，基于 SKILL.md 设计模板）：**

> **工作流设计：报名表单 → 表格 + 飞书提醒**
>
> **TRIGGER**：表单新提交（金数据 / 腾讯问卷 / Google Forms 等，选你正在用的）。
>
> **CONDITIONS（可选）**：仅当「邮箱」或「手机」不为空时执行，避免无效提交。
>
> **ACTIONS**：  
> 1. 新增一行到表格：飞书多维表格 或 Google Sheets，把表单字段映射到列（姓名、邮箱、提交时间等）。  
> 2. 发飞书群消息：用飞书机器人发到指定群，内容如「新报名：{姓名}，{邮箱}」，可带链接到表格行。
>
> **ERROR HANDLING**：若步骤 1 或 2 失败，发告警到你的邮箱或飞书运维群，便于及时排查。
>
> **工具选型**：简单 2～3 步用 **Zapier** 即可；若表单/飞书连接器在 **Make** 更全可用 Make；需要自建或复杂分支可用 **n8n**。  
> 接下来你可以在选定平台里按上述步骤创建并测试；需要我帮你细化某一端的配置再说。

---

### 示例 2：支付到账后自动开发票与更新 CRM（真实对话与返回）

**用户输入：**
> 客户在 Stripe 付款成功后，要自动标记发票已付、发收据邮件，并把 CRM 里该客户状态改成已付款。

**Agent 实现：**  
设计多步骤财务+CRM 流程，并说明去重与错误处理。

**返回给用户（示例）：**

> **工作流设计：Stripe 支付成功 → 发票 + 收据 + CRM**
>
> **TRIGGER**：Stripe Webhook，事件选 `invoice.paid` 或 `payment_intent.succeeded`（按你实际用的计费方式）。
>
> **CONDITIONS**：仅处理状态为 paid 的发票/支付，避免重复或退款单触发。
>
> **ACTIONS**：  
> 1. 在财务/开票系统把该发票标记为已付（若用 Stripe 开票则可能已自动，只需同步状态）。  
> 2. 发收据邮件：用 SendGrid / Mailchimp / 你现有邮件服务，模板里带客户名、金额、发票号。  
> 3. 调用 CRM API：根据客户邮箱或 Stripe customer_id 找到联系人，将状态或标签更新为「已付款」。
>
> **ERROR HANDLING**：任一步失败时发通知到运维/财务，并记录失败单号便于补跑。  
> **去重**：用 Stripe 的 `payment_intent.id` 或 `invoice.id` 做幂等，避免同笔支付触发多次。
>
> 在 Zapier/Make/n8n 中：Stripe 用 Webhook 或官方连接器；CRM 用对应 API 或连接器。需要我按你用的 CRM 列具体字段映射再说。

---

### 示例 3：每周一自动生成并发送周报（真实对话与返回）

**用户输入：**
> 每周一早上 9 点，从我们内部系统拉数据生成周报，并发到飞书群。

**Agent 实现：**  
设计定时触发 + 拉数 + 生成 + 发送，并说明与 cron 的配合方式。

**返回给用户（示例）：**

> **工作流设计：每周一 9:00 周报**
>
> **TRIGGER**：每周一 9:00（Zapier/Make 的 Schedule 或 n8n 的 Cron 节点）。
>
> **ACTIONS**：  
> 1. 从内部 API 或数据库拉取上周数据（HTTP 请求或 DB 节点，需你提供接口或查询方式）。  
> 2. 生成周报文案：用模板拼接，或调用 AI 接口做简短总结（若平台支持）。  
> 3. 发到飞书群：飞书机器人发消息或创建文档后分享链接到群。
>
> **ERROR HANDLING**：任一步失败发告警到固定群/邮箱；建议在平台里打开运行日志，每周看一眼失败记录。
>
> 若内部系统只能在内网访问，可改为：**cron 在本机/网关** 到点调用 n8n Webhook 或内部 API，由 n8n 再执行拉数+生成+发飞书，这样数据不经过公网。

---

## 注意事项

- 技能输出**方案与步骤**，实际在 Zapier/Make/n8n 里创建流程需你本人在对应平台完成。
- 自动化会操作真实数据，注意**权限最小化**与**幂等/去重**；建议先小范围试点并设错误通知。
- Zapier/Make 为境外服务，企业需评估合规与网络；n8n 可自建在内网。

---

[← 返回技能列表](./index.md) | [ClawHub: automation-workflows](https://clawhub.ai/JK-0001/automation-workflows)
