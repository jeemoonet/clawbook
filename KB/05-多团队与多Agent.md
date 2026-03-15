# 多团队与多 Agent

[← 返回目录](./index.md)

> 按部门或场景将飞书用户/群路由到不同 Agent（不同工作区或模型），实现多团队隔离与差异化能力。

---

## 适用场景

- **支持群** → 客服/问答 Agent（固定话术或知识库）
- **技术群** → 编程/代码 Agent（更强代码能力）
- **某部门** → 专用工作区与模型，数据与提示词隔离

同一网关下可配置多个 Agent，通过 **bindings** 将飞书用户（open_id）或群（chat_id）绑定到指定 Agent。

---

## 配置示例

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

---

## 获取 open_id / chat_id

- **用户 open_id**：私聊机器人后看 `openclaw logs --follow`，或 `openclaw pairing list feishu`。
- **群 chat_id**：在目标群 @ 机器人后，在日志中查看 `chat_id`。

---

## 多账号（多飞书应用）

若企业内有多套飞书应用（如不同子公司或环境），可在同一网关下配置多个飞书账号，并通过 `defaultAccount` 或 bindings 指定路由。详见 [配置参考](./10-配置参考.md)。

下一步：[安全与合规](./06-安全与合规.md)
