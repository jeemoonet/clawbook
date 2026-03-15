# Youtube watcher：YouTube 视频文稿获取

[← 返回技能列表](./index.md)

---

## 一句话用途

**获取并读取 YouTube 视频的字幕/文稿**（通过 `yt-dlp`），用于对视频做摘要、根据内容回答问题或提取信息。技能在本地执行，安全等级 L1。

> 来源：[ClawHub - youtube-watcher](https://clawhub.ai/Michaelgathara/youtube-watcher)、[OpenClaw Skills 官方 SKILL.md](https://github.com/openclaw/skills/tree/main/skills/michaelgathara/youtube-watcher)

---

## 适用场景

- **视频摘要**：拉取视频字幕后，由 Agent 或 summarize 技能做总结。
- **根据视频内容问答**：用户问「这个视频里讲了什么」「第 10 分钟在说什么」等，先取文稿再回答。
- **信息提取**：从字幕中查找关键词、时间点或具体表述。

仅适用于**有字幕**的视频（含 CC 或自动生成字幕）；无字幕视频会报错。

---

## 安装

```bash
openclaw add @michaelgathara/youtube-watcher
```

或通过 ClawHub / SkillHub（以实际 slug 为准）：

```bash
clawhub install youtube-watcher
# 或
skillhub install youtube-watcher
```

---

## 配置

### 依赖：安装 yt-dlp

技能依赖 **yt-dlp**，且需在系统 PATH 中可用。

- **macOS (Homebrew)**：`brew install yt-dlp`
- **pip**：`pip install yt-dlp`

安装后可用 `yt-dlp --version` 验证。

---

## 怎么用

### 触发词（官方 SKILL.md）

- "watch youtube"
- "summarize video"
- "video transcript"
- "youtube summary"
- "analyze video"

用户说上述或类似意思时，Agent 会调用本技能获取视频文稿并据此作答。

### 获取文稿（技能内部逻辑）

技能通过以下方式获取字幕文本：

```bash
python3 {baseDir}/scripts/get_transcript.py "https://www.youtube.com/watch?v=VIDEO_ID"
```

Agent 会解析用户提供的视频链接、调用该脚本、读取输出，再执行摘要、问答或检索。

### 示例 1：摘要视频（真实对话与返回）

**用户输入：**
> 帮我把这个视频总结一下：https://www.youtube.com/watch?v=dQw4w9WgXcQ

**Agent 实现：**  
调用 youtube-watcher，执行 `get_transcript.py` 获取该视频字幕 → 读取脚本输出的文稿文本 → 用模型或 summarize 做摘要。

**返回给用户（示例）：**
> **视频摘要：**  
> 该视频约 3 分钟，为一段带歌词的歌曲 MV。歌词大意包括「我们认识很久了」「你从没让我失望」等，配合画面与音乐。若你需要其中某一段的具体内容或时间点，可以说出时间段或关键词，我可以从文稿里帮你找。

（实际摘要以字幕内容为准；无字幕时 Agent 会返回脚本报错信息并说明需有 CC 或自动字幕。）

---

### 示例 2：在视频中查找信息（真实对话与返回）

**用户输入：**
> 这个技术分享视频里有没有讲「如何配置 OAuth」？有的话在第几分钟？  
> （附视频链接）

**Agent 实现：**  
获取该视频文稿 → 在文本中搜索「OAuth」「配置」等关键词 → 若文稿带时间戳则标注分钟数，否则按前后文估计位置。

**返回给用户（示例）：**
> 在文稿里找到了与「配置 OAuth」相关的内容，大致在 **第 12 分钟** 附近。原句类似：「接下来讲 OAuth 的配置，首先在控制台创建应用…」。如需整段原文我可以贴出。

---

### 示例 3：无字幕时的返回

**用户输入：**
> 总结一下 https://youtube.com/watch?v=某无字幕视频

**Agent 实现：**  
执行 `get_transcript.py`，该视频无字幕，脚本返回错误。

**返回给用户（示例）：**
> 这个视频没有可用的字幕（无 CC 且无自动生成字幕），当前技能无法拉取文稿，所以做不了摘要。你可以：换一个带字幕的视频，或者用其他支持语音转写的工具先得到文稿再发给我总结。

---

## 注意事项

- **必须有字幕**：仅支持有关闭字幕（CC）或自动生成字幕的视频；若视频无字幕，脚本会报错。
- **yt-dlp 必须可用**：未安装或不在 PATH 中会导致执行失败。
- 技能为 **L1 低风险**，仅读取公开字幕，不涉及登录或 cookie；若你需要在「仅登录可见」的视频上使用，需自行查阅该技能或 yt-dlp 的 cookie 配置方式（非官方 SKILL.md 所述）。

---

[← 返回技能列表](./index.md) | [ClawHub: youtube-watcher](https://clawhub.ai/Michaelgathara/youtube-watcher) | [OpenClaw Skills 仓库](https://github.com/openclaw/skills/tree/main/skills/michaelgathara/youtube-watcher)
