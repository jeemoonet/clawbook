# Nano Banana Pro

[← 返回技能列表](./index.md)

---

## 一句话用途

**Nano Banana Pro** 为 ClawHub 上的社区技能，具体能力与触发场景以官方仓库与 SKILL.md 为准；安装后可通过 `openclaw skills info nano-banana-pro` 查看当前版本的说明与用法。

---

## 适用场景

- 以技能作者在 ClawHub 与仓库中的描述为准；常见可能包括轻量级工具、快捷指令或与 Banana 系列工作流相关的增强能力。
- 建议在 [ClawHub 页面](https://clawhub.ai/steipete/nano-banana-pro) 查看最新介绍、适用场景与依赖后再决定是否安装。

---

## 安装

```bash
clawhub install nano-banana-pro
# 或
skillhub install nano-banana-pro
```

具体 slug 以 ClawHub / SkillHub 搜索「nano banana pro」或「nano-banana-pro」为准。

---

## 配置

- 以该技能仓库中的 **SKILL.md** 及 OpenClaw 文档为准；若需要 API Key、环境变量或 openclaw.json 中的 `skills.entries["nano-banana-pro"]` 配置，安装后查看 `openclaw skills info nano-banana-pro` 或本地 `~/.openclaw/skills/nano-banana-pro/` 下的说明。

---

### 方案1：配置 API易 Key

1. 去 [https://apiyi.com](https://api.apiyi.com/register/?aff_code=jE4U) 注册账号

2. 新增令牌，选择分组“NanoBanana企业”，保存后获取 API Key
   
   <img src="file:///C:/Users/James/AppData/Roaming/marktext/images/2026-03-15-10-47-55-image.png" title="" alt="" width="402">

3. 设置环境变量 `APIYI_API_KEY`
   
   ![](C:\Users\James\AppData\Roaming\marktext\images\2026-03-15-10-50-27-image.png)

## 怎么用

### 在对话中使用

根据技能的触发词与能力描述，在对话中提出相关需求；Agent 在技能已启用且条件匹配时会调用该技能。

### 示例：如何查看本技能用法（真实对话与返回）

**用户输入：**

> nano-banana-pro 这个技能能做什么？怎么用？

**Agent 实现：**  
未安装时提示安装后查看；已安装则读取本地 SKILL.md 或执行 `openclaw skills info nano-banana-pro`，按其中描述回答。

**返回给用户（示例）：**

> Nano Banana Pro 是 ClawHub 上的社区技能，具体能力和触发词以官方说明为准。你可以：  
> 
> 1. 打开 [ClawHub 页面](https://clawhub.ai/steipete/nano-banana-pro) 看介绍与更新；
> 2. 安装后在本机执行 `openclaw skills info nano-banana-pro` 查看当前版本说明；
> 3. 直接在工作区或 `~/.openclaw/skills/nano-banana-pro/` 下打开 `SKILL.md` 看用法与示例。
> 
> 如果你已经装了，我可以根据你当前环境里的 SKILL.md 帮你总结用法。

---

## 注意事项

- 本说明为占位与索引用途；**详细能力、触发条件与示例请以 [ClawHub 页面](https://clawhub.ai/steipete/nano-banana-pro) 及本地 SKILL.md 为准**。
- 安装前建议用 [skill-vetter](./skill-vetter.md) 做一次安全与合规扫描；企业环境请在受控环境中试用后再推广。

---

[← 返回技能列表](./index.md) | [ClawHub: nano-banana-pro](https://clawhub.ai/steipete/nano-banana-pro)