# Ontology：类型化知识图谱

[← 返回技能列表](./index.md)

---

## 一句话用途

**类型化知识图谱**：用实体（Person、Project、Task、Event、Document 等）和关系在本地维护结构化记忆，支持创建、查询、关联与图遍历；适合「记住并查询」「依赖追踪」「多步规划」及跨技能共享状态。

> 来源：[ClawHub - ontology](https://clawhub.ai/oswalpalash/ontology)、[OpenClaw Skills 官方 SKILL.md](https://github.com/openclaw/skills/tree/main/skills/oswalpalash/ontology)

---

## 适用场景（官方 When to Use）

| 触发/场景 | 动作 |
|-----------|------|
| "Remember that..." | 创建/更新实体 |
| "What do I know about X?" | 图查询 |
| "Link X to Y" | 创建关系 |
| "Show all tasks for project Z" | 图遍历 |
| "What depends on X?" | 依赖查询 |
| 多步规划 | 建模为图变换 |
| 技能需共享状态 | 读写 ontology 对象 |

---

## 安装

```bash
clawhub install ontology
# 或
clawhub install oswalpalash/ontology
```

---

## 配置

- **存储**：默认 `memory/ontology/graph.jsonl`（追加写入）。
- **Schema**：可选 `memory/ontology/schema.yaml` 定义类型约束与关系（required、status_enum、acyclic 等）。
- **环境**：技能通过 `scripts/ontology.py` 做 CRUD，需 Python 与工作区可写。

初始化：

```bash
mkdir -p memory/ontology
touch memory/ontology/graph.jsonl
```

---

## 怎么用

### 核心命令（来自 SKILL.md）

- **创建实体**：`python3 scripts/ontology.py create --type Person --props '{"name":"Alice"}'`
- **查询**：`python3 scripts/ontology.py query --type Task --where '{"status":"open"}'`、`get --id task_001`、`related --id proj_001 --rel has_task`
- **建立关系**：`python3 scripts/ontology.py relate --from proj_001 --rel has_task --to task_001`
- **校验**：`python3 scripts/ontology.py validate`

### 示例 1：记住人并查谁负责某项目（真实对话与返回）

**用户输入：**
> 记住：Alice 负责网站改版项目，Bob 负责 API 重构。

**Agent 实现：**  
调用 ontology：创建 Person 实体（Alice、Bob）、Project 实体（网站改版、API 重构），再 `relate --from <project_id> --rel has_owner --to <person_id>`。

**返回给用户：**
> 已记住：Alice 负责「网站改版项目」，Bob 负责「API 重构」。之后你可以问「谁负责网站改版？」我会查图回答。

**图数据示例（graph.jsonl 中追加）：**
```jsonl
{"op":"create","entity":{"id":"p_001","type":"Person","properties":{"name":"Alice"}}}
{"op":"create","entity":{"id":"proj_001","type":"Project","properties":{"name":"网站改版项目","status":"active"}}}
{"op":"relate","from":"proj_001","rel":"has_owner","to":"p_001"}
```

---

### 示例 2：查「项目 Z 下所有任务」（真实对话与返回）

**用户输入：**
> 项目「网站改版」下有哪些任务？都谁在做？

**Agent 实现：**  
`ontology.py related --id proj_001 --rel has_task`（或 query Task 再按关系过滤）。

**返回给用户：**
> 网站改版项目下目前有 3 个任务：
> - 「完成需求文档」— 负责人：Alice，状态：open  
> - 「需求评审」— 负责人：Alice，状态：in_progress  
> - 「前端首页改版」— 负责人：Bob，状态：open  

（若技能返回为 JSON，Agent 会整理成上述自然语言。）

---

### 示例 3：建立任务依赖并查「依赖 X 的项」

**用户输入：**
> 把「完成需求文档」设成「需求评审」的前置任务。

**Agent 实现：**  
`ontology.py relate --from task_完成需求文档 --rel blocks --to task_需求评审`（或技能约定的关系名，如 `blocks` 在 schema 中为 Task→Task 且 acyclic）。

**返回给用户：**
> 已设置：「完成需求文档」是「需求评审」的前置任务。之后问「需求评审之前要完成什么？」我会回答「完成需求文档」。

**用户追问：**  
> 需求评审之前要完成什么？

**Agent 实现：**  
图遍历：从「需求评审」沿 `blocks` 反向或「前置」关系查。

**返回给用户：**
> 需要先完成「完成需求文档」。

---

## 核心类型与约束（SKILL.md 摘要）

- **Person / Organization**：人、组织。  
- **Project / Task / Goal**：项目、任务、目标（Task 可有 status、due、assignee、blockers）。  
- **Event / Location**：事件、地点。  
- **Document / Message / Note**：文档、消息、笔记。  
- **Credential**：禁止直接存 password/secret，用 secret_ref 间接引用。

关系可在 schema 中约束：如 `has_owner` 为 Project/Task → Person，`blocks` 为 Task → Task 且 `acyclic: true`。

---

## 注意事项

- **追加写入**：对已有 ontology 数据或 schema 做**追加/合并**，不要整文件覆盖，以免丢失历史。
- **数据位置**：图谱在 `memory/ontology/`，备份与迁移需包含该目录。
- 与 [self-improving-agent](./self-improving-agent.md) 配合时，可将经验、纠错沉淀为实体或关系。

---

[← 返回技能列表](./index.md) | [ClawHub: ontology](https://clawhub.ai/oswalpalash/ontology) | [OpenClaw Skills 仓库](https://github.com/openclaw/skills/tree/main/skills/oswalpalash/ontology)
