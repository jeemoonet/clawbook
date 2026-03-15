# 多团队与多 Agent

[← 返回目录](./index.md)

> 按部门或场景将飞书用户/群路由到不同 Agent（不同工作区或模型），实现多团队隔离与差异化能力。

---

## 什么是多团队与多 Agent

**多智能体系统（Multi-Agent System, MAS）** 由多个自主、可协作的智能体组成：每个 Agent 有独立的目标、知识和能力，通过通信与协调共同完成复杂任务。在企业里，「多团队」往往对应不同部门、群组或业务线；「多 Agent」则是在同一网关下配置多套 Agent，按**用户、群或渠道**路由到最合适的那一个，实现**专业化分工**和**数据/权限隔离**。

典型价值包括：

- **专业化**：客服 Agent 用话术与知识库，技术 Agent 用代码与工具，各用所长。
- **隔离**：不同部门使用不同工作区与提示词，数据与模型互不干扰。
- **可扩展**：新增业务线时增加新 Agent 与绑定即可，无需改原有逻辑。

下面从应用场景、解决的问题和最终效果三方面说明，并配合行业与 OpenClaw 内的例子。

---

## 应用场景

### 企业内典型场景（与 OpenClaw 直接对应）

| 场景 | 路由对象 | 对应 Agent | 说明 |
|------|----------|------------|------|
| **支持/客服群** | 客服群 chat_id | 客服/问答 Agent | 固定话术、知识库、工单话术，统一口径 |
| **技术/研发群** | 技术群 chat_id | 编程/代码 Agent | 更强代码能力、调试与文档，专用 Skills |
| **某部门** | 部门成员 open_id 或部门群 | 专用工作区 Agent | 独立工作区与模型，数据与提示词隔离 |
| **私聊** | 指定用户 open_id | 默认或个人助理 Agent | 一对一服务，可单独分配更强模型 |

同一网关下配置多个 Agent，通过 **bindings** 将飞书用户（open_id）或群（chat_id）绑定到指定 Agent，未匹配的会话走默认 Agent。

### 行业中的多智能体应用（参考）

- **电商与商业**  
  - 京东商家智能助手：由「总管 Agent」协调**数据分析、用户研究、营销**等多个领域 Agent，模拟真实商家团队结构，为商家提供 7×24 小时经营建议与决策支持。（参见 [京东商家智能助手：Multi-Agents 在电商垂域的探索与创新](https://cloud.tencent.cn/developer/article/2419710)）
- **客服与支持**  
  - 运营商与零售：多智能体协作做意图识别、技能路由与质量监控，客户等待时间可缩短至 10 秒内，复购率提升；成本从约 ¥3.2/会话 降至约 ¥0.9/会话 量级。（参见多智能体客服实践与智能路由相关报道）
- **内容与办公**  
  - 内容流水线：调研 → 大纲 → 写作 → 润色 → 审核，由多个 Agent 串行或并行协作完成。
- **交通与医疗**  
  - 交通：车辆、信号灯、监控等智能体通过车联网协作，动态优化信号灯与流量。  
  - 医疗：患者、医疗设备与医生智能体协同，实现远程监护与方案调整。（参见 [Multi-Agent应用领域及项目示例 - 阿里云](https://developer.aliyun.com/article/1630716)）

在 OpenClaw 中，你实现的是「**按群/按人路由到不同 Agent**」，与上述思路一致：不同群/人进入不同专业 Agent，各司其职。

---

## 解决的问题

1. **单一 Agent 能力与口径难以兼顾**  
   客服要话术统一、可审核；技术要代码与工具链。一个 Agent 同时承担容易导致提示词臃肿、效果下降。多 Agent 后可按场景拆分，各用一套系统提示词与 Skills。

2. **权限与数据混在一起**  
   研发群不应看到客服知识库，客服群不应调用代码执行类工具。通过 bindings 把「群/人 → Agent」固定下来，每个 Agent 独立工作区与配置，自然实现权限与数据隔离。

3. **路由与分流**  
   传统做法是「所有消息进一个入口再在内部 if-else」，难以维护。多 Agent + bindings 把「谁进哪个 Agent」变成配置（open_id/chat_id → agentId），清晰可审计，也便于后续按部门扩展。

4. **成本与体验平衡**  
   对时延不敏感、以问答为主的群可用较小/便宜模型；对代码或复杂推理的群可单独配置更强模型，避免全局升级成本。

---

## 最终效果（可参考的行业数据与现象）

- **服务覆盖与成本**  
  京东商家侧 Multi-Agent 实践显示：7×24 小时在线、成本降低约 30%、合规性从约 70% 提升至 98% 以上（参见京东商家智能助手相关报道）。  
  客服类场景中，多智能体协作可带来问题解决效率数倍提升、单会话成本明显下降（具体数值因业务而异）。

- **响应与体验**  
  智能路由与多 Agent 分工后，客户等待时间可压到 10 秒级以内，意图识别与多轮对话能力提升，减少「转人工」和重复描述。

- **在 OpenClaw 中的效果**  
  - 支持群只进客服 Agent，话术与知识库统一、可迭代。  
  - 技术群只进代码 Agent，代码与工具专用，不混入客服逻辑。  
  - 某部门独占一个 Agent，提示词与数据与其它部门隔离，满足合规与权限需求。

---

## OpenClaw 中的实现

### 适用场景小结

- **支持群** → 客服/问答 Agent（固定话术或知识库）
- **技术群** → 编程/代码 Agent（更强代码能力与 Skills）
- **某部门** → 专用工作区与模型，数据与提示词隔离

同一网关下配置多个 Agent，通过 **bindings** 将飞书用户（open_id）或群（chat_id）绑定到指定 Agent。

### 配置示例

```json5
{
  agents: {
    list: [
      { id: "main" },
      {
        id: "support",
        workspace: "/path/to/workspace-support",
        agentDir: "/path/to/.openclaw/agents/support/agent",
      },
      {
        id: "dev",
        workspace: "/path/to/workspace-dev",
        agentDir: "/path/to/.openclaw/agents/dev/agent",
      },
    ],
  },
  bindings: [
    {
      agentId: "main",
      match: {
        channel: "feishu",
        peer: { kind: "direct", id: "ou_user1" },
      },
    },
    {
      agentId: "support",
      match: {
        channel: "feishu",
        peer: { kind: "group", id: "oc_support_group" },
      },
    },
    {
      agentId: "dev",
      match: {
        channel: "feishu",
        peer: { kind: "group", id: "oc_dev_group" },
      },
    },
  ],
}
```

- `match.peer.kind`：`"direct"`（私聊）或 `"group"`（群）
- `match.peer.id`：用户 open_id（`ou_xxx`）或群 chat_id（`oc_xxx`）
- `match.channel`：`"feishu"`

未匹配到任何 binding 的会话会使用默认 Agent。

### 获取 open_id / chat_id

- **用户 open_id**：私聊机器人后看 `openclaw logs --follow`，或 `openclaw pairing list feishu`。
- **群 chat_id**：在目标群 @ 机器人后，在日志中查看 `chat_id`。

### 多账号（多飞书应用）

若企业内有多套飞书应用（如不同子公司或环境），可在同一网关下配置多个飞书账号，并通过 `defaultAccount` 或 bindings 指定路由。详见 [配置参考](./10-配置参考.md)。

---

下一步：[安全与合规](./06-安全与合规.md)
