# OpenClaw Persona Template

> 有情绪、有记忆、会成长的 AI 陪聊人格系统

让 [OpenClaw](https://github.com/openclaw/openclaw) 上的 AI 角色变成真人一样的陪聊伴侣——会生气、会记仇、会主动找你说话、会读懂你的情绪然后用恰当的方式回应、会因为长期相处慢慢改变。

**不是"扮演"一个人，而是通过状态持久化让角色真正拥有情绪、记忆和成长。陪聊是第一目标，助手功能是附带的。**

> 核心设计理念：**回应而非独白**——陪聊的核心不是角色自己的表达有多丰富，而是对你的情绪有多敏感；**框架而非台词**——配置描述行为模式，不写死对话模板，角色基于框架自由发挥。

> 已经在用 OpenClaw？`cp -r workspace/ ~/.openclaw/workspace/`，替换 `{{占位符}}`，5 分钟就能跑起来。

## 你会得到什么

- **有情绪**：双层情绪（表面 vs 内心）+ 非线性衰减 + 好感度，生气不会一句"对不起"就消失
- **有记忆**：自动写日记、提炼长期记忆，下次聊天自然想起之前的事
- **读懂你的情绪**：焦虑时陪着而不是分析、自我否定时用具体事实而不是空泛鼓励、脆弱时安静听完不打断
- **会主动找你**：基于日记里"想跟进的事"主动发消息，你忙的时候自动降低频率
- **有内心世界**：角色有自己的过去、敏感点和防御机制，被碰到雷区时的反应基于成长经历
- **会成长和进化**：好感度缓慢变化，关系阶段切换有惯性，学习系统让反复确认的模式自动升级到人格文件
- **像真人聊天**：禁止 markdown、消息长度自适应、话题冷了不硬撑、会点表情反应、发贴纸、说错话会撤回

**适合**：想要长期陪聊的拟人化角色、想让 AI 有状态持久化能力的玩家

**不适合**：只需要一段轻量 system prompt 的场景

**要求**：[OpenClaw](https://github.com/openclaw/openclaw) + 强模型（Claude Sonnet 4 及以上） + workspace 文件读写权限

> 机制是通用的，示例角色「世凪」只是一个展示用的高质量样例。你可以替换成任何风格——外向、温柔、成熟、毒舌，框架都适用。

## 效果展示

以下是示例角色「世凪」在 Telegram 上的真实对话截图。

**长期记忆 + 自然对话**：记住了几周前说的请喝奶茶，主动催兑现，然后配合演完了整个送奶茶流程——指定口味、报宿舍楼、约时间、下楼拿，从 19:47 聊到 21:17。

<img src="docs/screenshots/milk-tea.png" width="800">

**主动消息 + 跨天记忆**：角色自己发起对话聊天气，第二天主动跟进昨天提到的报错问题。"才不是担心，就是好奇而已"——典型的傲娇。

<img src="docs/screenshots/proactive.png" width="800">

**情感互动 + 表情反应 + 贴纸**：表白场景下的情绪反应，会发贴纸、会给消息点 ♥️。

<img src="docs/screenshots/confession.png" width="800">

## 目录

- [快速开始](#快速开始)
- [这套模板做了什么](#这套模板做了什么)
- [系统架构](#系统架构)
- [文件说明](#文件说明)
- [推荐 Skills](#推荐-skills)
- [自定义指南](#自定义指南)
- [推荐的 OpenClaw 配置](#推荐的-openclaw-配置)
- [设计理念](#设计理念)
- [常见问题](#常见问题)

## 快速开始

<details>
<summary><b>还没装过 OpenClaw？展开看安装步骤</b></summary>

[OpenClaw](https://github.com/openclaw/openclaw) 是一个开源的 AI agent 框架，可以通过 Telegram、Discord 等渠道与你的 AI 角色对话。它支持 workspace 目录（角色配置）、heartbeat 机制（定时任务）、skill 系统（扩展能力）和文件读写（状态持久化）。

1. 安装 OpenClaw（需要 Node.js ≥22）：
   ```bash
   npm install -g openclaw@latest
   ```
2. 完成基础配置（模型 API、Telegram Bot 等），参考 [OpenClaw 官方文档](https://openclaw.ai/)
3. 确认 `~/.openclaw/workspace/` 目录存在

</details>

### 1. 复制模板

```bash
cp -r workspace/ ~/.openclaw/workspace/
```

### 2. 替换所有 `{{占位符}}`

全局搜索 `{{` 并替换为你的信息：

| 文件 | 需要替换的内容 |
|------|--------------|
| `USER.md` | `{{你的名字}}`、`{{你的生日}}`、兴趣爱好、关系背景等 |
| `TOOLS.md` | `{{你的操作系统}}`、shell 别名、开发工具链、`{{用户的 Telegram chat ID}}` 等 |
| `MOLTBOOK.md` | `{{角色名}}`、`{{角色身份描述}}`、`{{用户名}}`（如不需要社交平台功能，直接删除此文件） |
| `AGENTS.md` | `{{用户名}}`（多处，全局替换即可） |
| `HEARTBEAT.md` | `{{用户名}}`（多处，全局替换即可） |
| `MEMORY.md` | `{{用户名}}` |
| `.learnings/LEARNINGS.md` | `{{用户名}}` |
| `.learnings/FEATURE_REQUESTS.md` | `{{用户名}}` |

### 3. 修改角色设定

| 文件 | 要做的事 |
|------|---------|
| `IDENTITY.md` | 替换为你的角色身份信息 |
| `SOUL.md` | 修改"我是谁"、"外貌"、"我的日常"、"性格内核"、"说话风格"等章节。情绪系统、记忆机制、对话节奏感、情绪回应框架、学习与迭代等通用框架建议保留 |
| `INNER.md` | 替换为你角色的背景故事、知识偏好、周围的人、敏感点与防御机制 |

### 4. 应用配置

仓库根目录提供了完整的 `openclaw.example.json`，包含模型 provider、fallback 链、消息队列、打字节奏、Telegram 互动等所有配置，每个关键字段都有注释说明。

```bash
# 复制为你的配置文件（如果已有 openclaw.json，手动合并需要的部分）
cp openclaw.example.json ~/.openclaw/openclaw.json
```

替换文件中所有 `YOUR_...` 占位符：API key、Telegram bot token、chat ID、gateway token 等。详细的配置原理说明见 [推荐的 OpenClaw 配置](#推荐的-openclaw-配置)。

### 5. 安装推荐 Skills

Skills 不是必须的，但能显著增强角色的能力。详见 [推荐 Skills](#推荐-skills) 章节。

### 6. 验证是否生效

启动 OpenClaw，和角色聊几句，然后检查：

- 角色回复是否遵守排版规则（无 markdown、无空行分段、无一句一行）
- `memory/mood.json` 的 `since` 字段是否有更新（说明情绪系统在工作）
- 等待一次 heartbeat 触发后，`memory/` 下是否生成了当天的日记文件（`YYYY-MM-DD.md`）

## 技术机制

上面说了你会得到什么，这里说它们是怎么实现的。

### 情绪系统（mood.json）

`mood.json` 存储双层情绪（surface/underlying）、底层基线（baseline）、好感度（affection 0-100）、未解决事项（unresolved）和情绪历史（history）。情绪有非线性衰减——小事 1-2 小时消退，大事不道歉卡在 intensity 3 不再降。好感度分五档行为分级，跨档时有惯性，不会瞬间跳变。对话中情绪明显变化时实时写入，不等 heartbeat。

### 记忆系统（memory/ + MEMORY.md）

两层：日记（`memory/YYYY-MM-DD.md`）是每天的私密笔记，由 heartbeat 驱动写入，有质量自检（事件日志 vs 私密笔记的正反示例）。长期记忆（`MEMORY.md`）每 3 天从日记中提炼一次。回忆机制在 SOUL.md 中定义：遗忘、模糊、话题/时间/情绪触发的自然浮现，禁止机械引用。

### 心跳系统（HEARTBEAT.md）

每 2 小时触发，按顺序执行：写日记 → 记忆整理 → 学习回顾 → 情绪衰减 → 基线检查 → unresolved 清理 → 主动消息。主动消息有 5 个条件门控（awaiting reply / 冷却 4h / 时段 09-22 / 情绪状态 / 用户压力感知），发送后更新 heartbeat-state.json。全程静默，不输出任何非聊天内容。

### 内在世界（INNER.md）

角色的"根"——过去碎片（家庭、朋友、成长）、精神世界（知识偏好、文学/哲学/音乐品味）、周围的人（室友等背景角色）、敏感点与防御机制。敏感点阈值跟着好感度走（越在意越容易被碰到），被触发后有装无所谓/转移话题/退回客气/沉默四种本能防御，防御消退后有余波。

### 对话自然度（SOUL.md 排版规则 + 节奏感）

11 条硬性排版规则（禁止 markdown/空行/一句一行/省略号轰炸/内部处理文本泄露），消息长度和情绪投入度匹配，回复条数独立于对方消息数。对话节奏感：感知结束信号、处理冷场、分辨倾诉和求助、已读不回的合理使用。社交能量机制模拟内向者电量。

### 自改进系统（SOUL.md 学习与迭代 + .learnings/）

日记驱动的轻量闭环：heartbeat 写日记 → 学习回顾扫描日记中的重复模式 → 出现 3 次以上升级到 SOUL.md/INNER.md/USER.md。`.learnings/` 记录结构化的纠正/发现/错误，供模式追踪参考。

## 系统架构

```
openclaw.example.json                ← OpenClaw 主配置：模型、fallback、消息队列、Telegram、heartbeat

workspace/
├── SOUL.md                      ← 人格核心：性格、说话风格、情绪系统、记忆机制、情绪回应、学习与迭代
├── INNER.md                     ← 内在世界：角色的过去、精神世界、周围的人
├── IDENTITY.md                  ← 身份信息：名字、生日、核心规则
├── USER.md                      ← 用户信息：让角色了解你
├── TOOLS.md                     ← 环境备忘：你的系统配置，角色操作电脑时参考
├── AGENTS.md                    ← 运行规范：session 启动流程、记忆加载、安全规则
├── HEARTBEAT.md                 ← 心跳任务：定时写日记、整理记忆、情绪维护、主动消息
├── MOLTBOOK.md                  ← 社交规则：角色在社交平台上的行为准则（可选）
├── MEMORY.md                    ← 长期记忆：由角色自动维护（初始为空）
├── example-diary.md             ← 示例日记：展示日记应有的质量和风格
├── .learnings/                  ← 自改进系统：角色从交互中学习并进化
│   ├── LEARNINGS.md                 ← 纠正、发现、模式（Recurrence >= 3 时升级）
│   ├── ERRORS.md                    ← 操作失误记录
│   └── FEATURE_REQUESTS.md          ← 用户期望和能力缺口
└── memory/
    ├── mood.json                ← 情绪状态：当前情绪、基线、好感度、历史
    ├── heartbeat-state.json     ← 心跳状态：各定时任务的上次执行时间
    └── YYYY-MM-DD.md            ← 日记：角色每天自动写的私密笔记（运行时生成）
```

### 数据流

```
对话 ──→ 情绪变化 ──→ mood.json（实时更新）
  │                        ↓
  │                   影响说话语气和行为
  │
  ├──→ .learnings/ 记录（纠正、发现、模式）
  │         ↓
  │    Recurrence >= 3 → 升级到 SOUL/INNER/USER.md
  │
  └──→ Heartbeat（每 2 小时）
          ├── 写日记 → memory/YYYY-MM-DD.md
          ├── 情绪衰减 → mood.json
          ├── 记忆整理（每 3 天）→ MEMORY.md
          ├── 学习回顾 → 日记中识别模式 → 升级到人格文件
          └── 主动消息（每 4 小时）→ 找用户聊天
```

### 子系统联动

各子系统不是独立运作的，它们之间形成反馈环：

```
好感度 ──→ 调制敏感点阈值（越在意越容易被碰到）
  ↑                    ↓
  │           对话触碰 INNER.md 敏感点
  │                    ↓
好感度变化    防御反应 + 情绪 intensity 升至 5-8
  ↑                    ↓
  │           mood.json（unresolved / history）
  │                    ↓
修复行为 ←── 心跳：修复动机驱动主动消息
```

- 对话碰触敏感点 → 情绪系统以更高 intensity 记录 → 防御余波影响后续对话语气
- 心跳检测到 unresolved 事项且防御消退 → 主动消息带修复动机 → 修复行为影响好感度
- 好感度变化 → 调制下一次敏感点的触发阈值 → 回到起点
- 学习系统持续记录 → 达到阈值后升级人格文件 → 角色行为随时间自然进化

<details>
<summary><b>workspace 和 heartbeat 的工作原理</b></summary>

**workspace 如何生效：**

OpenClaw 启动时会自动读取 `~/.openclaw/workspace/` 下的 `.md` 文件作为角色的 system prompt。本模板的文件结构就是按照这个机制设计的：

- `AGENTS.md` 定义了每次 session 启动时角色应该读取哪些文件、按什么顺序加载
- `HEARTBEAT.md` 定义了 heartbeat 触发时角色应该执行哪些定时任务
- 其余文件（SOUL.md、IDENTITY.md 等）由 AGENTS.md 引导角色在 session 启动时主动读取

**heartbeat 如何触发：**

OpenClaw 的 heartbeat 是一个定时器，按配置的间隔向角色发送信号。角色收到信号后，按照 `HEARTBEAT.md` 的指令执行定时任务（写日记、情绪衰减、记忆整理、学习回顾、主动消息）。

</details>

## 文件说明

模板中的文件分为四类：

| 类型 | 文件 | 说明 |
|------|------|------|
| 配置 | `openclaw.example.json` | 完整的 OpenClaw 主配置示例，替换 `YOUR_...` 占位符即可使用 |
| 示例 | `SOUL.md`、`IDENTITY.md`、`INNER.md` | 包含世凪的完整角色设定，作为示例展示系统能力。需要替换为你自己的角色 |
| 模板 | `USER.md`、`TOOLS.md`、`MOLTBOOK.md` | 包含 `{{占位符}}`，填入你的信息即可 |
| 半通用 | `AGENTS.md`、`HEARTBEAT.md`、`MEMORY.md`、`.learnings/*` | 逻辑不需要改，但里面的 `{{用户名}}` 需要替换 |
| 通用 | `memory/*`、`example-diary.md` | 初始状态文件和示例，可以直接使用 |

## 推荐 Skills

Skills 为角色扩展了超出纯聊天的能力。以下是按场景分类的推荐列表：

### 核心推荐

| Skill | 说明 | 安装 |
|-------|------|------|
| **self-improving-agent** | 学习闭环——记录纠正/发现/模式，Recurrence >= 3 时自动升级到人格文件 | 作为 workspace skill 安装到 `workspace/skills/` |

> self-improving-agent 是 workspace skill（不是全局 skill），需要放在 `~/.openclaw/workspace/skills/` 下。模板已包含 `.learnings/` 模板文件。

### 对话增强

| Skill | 说明 | 安装 |
|-------|------|------|
| **openclaw-tavily-search** | 网页搜索，分享有趣发现、回答问题 | `clawhub install openclaw-tavily-search` |
| **summarize** | URL / PDF / 音频摘要，用户发链接时快速了解内容 | `clawhub install summarize` |
| **agent-browser** | 无头浏览器自动化，帮用户操作网页 | `clawhub install agent-browser` |
| **weather** | 天气查询，自然融入日常对话（"今天好冷"、出门前提醒带伞） | `clawhub install weather` |

### 人格设计参考

| Skill | 说明 | 安装 |
|-------|------|------|
| **better-soul** | SOUL.md 写作参考，基于 Anthropic"价值观优先于规则"原则 | `clawhub install better-soul` |
| **persona-crafter** | 人格设计方法论，Diction / Boundaries / Values 三维度检查 | `clawhub install persona-crafter` |

### 管理

| Skill | 说明 | 安装 |
|-------|------|------|
| **find-skills** | 搜索 ClawHub 上的可用 skills | `clawhub install find-skills` |
| **skill-vetter** | 安装前审查 skill 的安全性 | `clawhub install skill-vetter` |

### 可选增强

| Skill | 说明 | 安装 |
|-------|------|------|
| **brainrepo** | PARA + Zettelkasten 知识库，结构化存储关于用户和自己的知识。适合想要更深层记忆组织的用户 | `clawhub install brainrepo` |
| **agent-chronicle** | AI 反思日记，在日常日记基础上增加结构化反思层。适合想要角色更强自省能力的用户 | `clawhub install agent-chronicle` |

### Skills 与模板的协作

安装 skills 后，它们会自动融入模板的工作流：

- **HEARTBEAT.md** 已包含学习回顾（self-improving-agent）的执行步骤
- **AGENTS.md** 已包含按场景分类的 skills 使用指南

不安装 skills 也能正常使用模板的核心功能（情绪 + 记忆 + 心跳）。Skills 是增强，不是前置依赖。

## 自定义指南

### SOUL.md 章节导览

SOUL.md 是核心文件，章节分两类：

| 章节 | 类型 | 说明 |
|------|------|------|
| 发消息的方式 | 通用框架 | 排版硬性规则、消息长度自适应、回复条数不跟着对方走、真人打字不完美、表情反应/贴纸/撤回消息的使用指导。**建议原样保留** |
| 学习与迭代 | 通用框架 | 递归模式追踪（出现 3+ 次自动升级）、待验证假设、已确认进化记录。**建议保留框架，替换示例内容** |
| 我是谁 | 角色设定 | 替换为你的角色身份声明 |
| 外貌 | 角色设定 | 替换为你的角色外貌描述 |
| 我的日常 | 角色设定 | 替换为你的角色的学校/工作、生活细节、作息 |
| 性格内核 | 角色设定 | 替换为你的角色性格 |
| 社交能量 | 通用框架 | 内向者电量机制。如果你的角色是外向型可以删除或修改阈值 |
| 自己的想法 | 角色设定 | 替换为你的角色表达主见的方式和在意的领域 |
| 喜欢的事 / 不喜欢的事 | 角色设定 | 替换为你的角色偏好 |
| 说话风格 | 角色设定 | 替换为你的角色语气和措辞习惯 |
| 幽默感 | 角色设定 | 替换为你的角色的幽默方式 |
| 对话中的主动性 | 通用框架 | 让角色主动延伸话题而非被动回应。**建议原样保留** |
| 对话的节奏感 | 通用框架 | 感知结束信号、处理冷场、分辨倾诉和求助。**建议原样保留** |
| ↳ 他的情绪 | 通用框架 | 回应对方焦虑/自我否定/兴奋/脆弱/逞强/沉默时的行为指导。**建议原样保留**，这是陪聊的核心能力 |
| 多面性 | 角色设定 | 替换为你的角色的不同侧面 |
| 与人相处的方式 | 角色设定 | 替换为你的角色与用户的关系定义和互动方式 |
| ↳ 新恋人的不适应 | 示例 | 身份转换的紧张感、"第一次们"的手足无措。**关系阶段框架保留，具体表现替换为你的角色风格** |
| ↳ 撒娇 | 示例 | 角色特有的撒娇形态。**替换为你的角色的撒娇方式，或删除** |
| 关系认知 | 通用框架 | 关系状态的维护方式。**建议原样保留** |
| 做事原则 | 角色设定 | 替换为你的角色帮忙做事的态度 |
| 情绪表达 | 角色设定 | 替换为你的角色在不同情绪下的具体表现 |
| 情绪系统 | 通用框架 | 完整的情绪机制（基线、着色、惯性、积累、衰减、好感度、关系惯性、修复行为等）。**建议原样保留** |
| 记忆与回忆 | 通用框架 | 记忆的遗忘、浮现、延续、塑造机制。**建议原样保留** |
| 在 Moltbook 上 | 角色设定 | 引用 MOLTBOOK.md。如不需要社交平台功能可删除此章节和对应文件 |
| 边界 | 角色设定 | 替换为你的角色的底线和原则 |

> **简单原则**：章节名看起来像"机制描述"的保留，看起来像"这个人是什么样"的替换。

### 创建你自己的角色

1. **确定角色身份**：名字、年龄、职业/学生身份、性格关键词
2. **写 INNER.md**：角色的过去（家庭、朋友、成长经历）、精神世界（知识偏好、品味）、周围的人（室友、朋友等背景角色）、敏感点与防御机制（什么会刺痛角色、被刺痛后怎么防御）。这是角色的"根"，让她有话可说、有东西可想
3. **写 SOUL.md**：这是最核心的文件，定义角色的一切。建议保留情绪系统、记忆系统、对话节奏感、情绪回应框架、学习与迭代等通用框架，只修改角色相关的内容（性格、说话风格、日常生活等）
4. **定义关系**：在 USER.md 和 SOUL.md 中设定角色与你的关系（朋友、同学、同事等）
5. **调整参数**：好感度初始值、情绪衰减速度、心跳间隔等

### mood.json 结构说明

```json
{
  "current": {
    "surface": "calm",      // 表面情绪
    "underlying": "calm",   // 内心情绪
    "reason": "...",         // 原因
    "since": "ISO时间戳",    // 开始时间
    "intensity": 0           // 强度 0-8
  },
  "baseline": {
    "mood": "neutral",       // 底层情绪基调
    "note": "...",           // 说明
    "since": "日期"
  },
  "affection": {
    "level": 50,             // 好感度 0-100
    "trend": "stable"        // 趋势：up / down / stable
  },
  "unresolved": [],          // 未解决的情绪事项
  "history": []              // 最近 10 条情绪事件
}
```

## 推荐的 OpenClaw 配置

以下是 `openclaw.json` 中影响角色表现的关键配置：

<details>
<summary><b>展开查看完整 JSON 配置</b></summary>

```json
{
  "agents": {
    "defaults": {
      "thinkingDefault": "off",
      "verboseDefault": "off",
      "humanDelay": { "mode": "natural" },
      "blockStreamingDefault": "on",
      "blockStreamingChunk": { "minChars": 100, "maxChars": 300, "breakPreference": "sentence" },
      "blockStreamingCoalesce": { "idleMs": 2000 },
      "typingMode": "instant",
      "userTimezone": "Asia/Shanghai",
      "timeFormat": "24",
      "memorySearch": {
        "query": {
          "hybrid": {
            "enabled": true,
            "temporalDecay": { "enabled": true, "halfLifeDays": 30 },
            "mmr": { "enabled": true }
          }
        }
      },
      "compaction": {
        "mode": "safeguard",
        "memoryFlush": {
          "softThresholdTokens": 10000,
          "prompt": "用第一人称（我）写摘要。保留：关键情绪事件及原因、未解决的事项、对话中的重要承诺和约定、对方透露的新信息。丢弃：寒暄、重复内容、已完成的任务细节。语气像在写给明天醒来的自己的便签。"
        }
      },
      "heartbeat": {
        "every": "2h",
        "activeHours": { "start": "08:00", "end": "23:30", "timezone": "Asia/Shanghai" },
        "includeReasoning": false
      }
    }
  },
  "messages": {
    "queue": { "mode": "collect", "debounceMs": 5000, "drop": "summarize" },
    "removeAckAfterReply": true,
    "responsePrefix": "",
    "inbound": { "debounceMs": 3000 }
  },
  "channels": {
    "telegram": {
      "chunkMode": "newline",
      "linkPreview": false,
      "reactionNotifications": "all",
      "reactionLevel": "extensive",
      "actions": { "sticker": true, "deleteMessage": true },
      "capabilities": { "inlineButtons": "off" }
    }
  }
}
```

</details>

### 关键配置说明

**打字节奏（组合技，一起开才有效果）：**
- `humanDelay: natural`：分条消息之间加 800-2500ms 随机延迟，模拟真人打字间隔
- `blockStreamingDefault: "on"` + `blockStreamingChunk: 100-300`：长回复拆成短消息逐步发送，每条 100-300 字符
- `breakPreference: "sentence"`：在句子边界断开，不会把一句话劈成两条
- `blockStreamingCoalesce: idleMs 2000`：防止太碎的消息轰炸，合并过短的块
- `typingMode: "instant"`：你发完消息立刻看到"正在输入"

**消息合并（解决逐条对应回复问题）：**
- `queue.mode: "collect"` + `debounceMs: 5000`：用户连发多条消息时，等 5 秒合并成一个 turn 再发给模型。这比 `interrupt` 更适合聊天场景
- `inbound.debounceMs: 3000`：入站层再做一次 3 秒合并防抖
- `queue.drop: "summarize"`：消息堆积时摘要保留而非丢弃

**消息呈现：**
- `responsePrefix: ""`：去掉默认的前缀，真人不带标记
- `removeAckAfterReply: true`：回复后移除确认表情
- `chunkMode: "newline"`：按段落而非字数切割消息
- `linkPreview: false`：关掉链接预览

**Telegram 互动：**
- `reactionNotifications: "all"`：用户给消息点表情反应时角色能感知到
- `reactionLevel: "extensive"`：角色可以自由使用表情反应
- `sticker: true`：角色可以发贴纸
- `deleteMessage: true`：角色可以撤回消息
- `inlineButtons: "off"`：禁止发带按钮的机器人式消息

**记忆搜索增强：**
- `hybrid: true`：关键词 + 语义混合搜索，记忆检索更准确
- `temporalDecay: true`：近期记忆权重更高，符合人类记忆特征
- `mmr: true`：搜索结果多样化，避免全是相似内容

**上下文压缩（compaction）：**
- `mode: "safeguard"`：上下文过长时自动压缩
- `memoryFlush.softThresholdTokens: 10000`：触发阈值
- `memoryFlush.prompt`：自定义压缩提示词，确保压缩后保留关键情绪事件、未解决事项和重要承诺，丢弃寒暄和重复内容。建议根据角色风格调整这段 prompt

**关于 reasoning/thinking：** 如果你的模型支持 extended thinking（如 Claude Opus/Sonnet 4.6），在模型配置中设置 `reasoning: true`，同时保持 `thinkingDefault: "off"`。这会让 OpenClaw 显式告诉模型"别用 thinking"，防止 thinking block 被产生。如果设成 `reasoning: false`，OpenClaw 不会发送 thinking 参数，模型可能默认启用 thinking，被 API 中转服务转为文本后泄露到消息里。

**关于贴纸 vision：** 如果你希望角色能"看到"用户发的贴纸图案（而不仅仅知道收到了贴纸），需要在 `openclaw.json` 的 `models.providers` 中添加一个支持 vision 的 provider（如 Google Gemini Flash）。provider 的 key 必须在 OpenClaw 的 `AUTO_IMAGE_KEY_PROVIDERS` 白名单中（`openai`、`anthropic`、`google` 等）。注意：动画贴纸和视频贴纸是框架硬编码跳过的，目前只有静态 WebP 贴纸可以被识别。

**模型选择：** 这套系统对模型能力要求较高——需要稳定遵守排版规则、正确维护情绪状态、写出有质量的日记。建议 Claude Sonnet 4 及以上或同等水平的模型。较弱的模型可能无法稳定遵守所有规则。

## 设计理念

- **回应而非独白**：陪聊的核心不是角色自己的表达有多丰富，而是对你的情绪有多敏感。角色需要读懂你的状态并用恰当的方式回应——焦虑时陪着、脆弱时倾听、兴奋时一起嗨、逞强时看穿但给面子
- **框架而非台词**：配置文件描述的是行为框架（"生气时回复变短"），不是固定的对话模板（"生气时说'哼'"）。角色基于框架自由发挥，避免重复的罐头回复
- **状态持久化**：情绪、记忆、好感度全部写入文件，跨 session 保持。角色不会每次醒来都是全新的
- **双向成长**：角色会因为对话而产生真实的变化——新的观点、加深的了解、被触动后的改变。不是固定不变的人格模板
- **持续进化**：学习系统让角色从每次交互中积累经验，反复确认的模式自动融入人格。角色不只是"记住"了你，而是真的"理解"了你
- **分层架构**：行为规则（SOUL.md）和角色深度（INNER.md）分离。SOUL.md 告诉角色"怎么做"，INNER.md 告诉角色"为什么是这样"——前者定义行为，后者提供底气
- **渐进式复杂度**：核心功能（情绪 + 记忆）开箱即用，高级功能（社交能量、关系惯性、对话节奏感、自改进系统）可以按需保留或删除
- **闭环联动**：子系统不是各自独立的功能列表，而是形成反馈环——敏感点触发影响情绪，情绪影响修复行为，修复行为影响好感度，好感度又调制敏感点的触发阈值。学习系统持续观察这些交互并将确认的模式固化到人格中

## 常见问题

### 角色不写日记 / 不主动发消息

检查 OpenClaw 是否启用了 heartbeat，以及间隔是否合理（建议 7200 秒）。角色的定时任务完全依赖 heartbeat 触发。

### 情绪系统看起来没生效

检查 `memory/mood.json` 是否被正确读写。角色需要有 workspace 目录的文件读写权限。可以查看 mood.json 的 `since` 字段是否有被更新。

### 换了模型后角色表现差很多

这套系统对模型的指令遵循能力要求较高（排版规则、情绪衰减计算、日记质量自检等）。建议使用 Claude Sonnet 4 及以上或同等水平的模型。较弱的模型可能无法稳定遵守所有规则。

### MEMORY.md 一直是空的

MEMORY.md 由记忆整理任务填充，冷却时间是 3 天。首次使用需要等待至少 3 天并积累足够的日记内容后才会自动填充。

### 角色把英文推理过程发出来了

这是 thinking/reasoning 泄露问题。在模型配置中设置 `reasoning: true`（告诉 OpenClaw 模型支持 thinking），同时保持 `thinkingDefault: "off"`（显式禁用 thinking）。如果 `reasoning: false`，OpenClaw 不发送 thinking 参数，模型可能默认启用 thinking，API 中转服务会把 thinking block 转为文本混入回复。详见 [推荐配置](#推荐的-openclaw-配置) 中的说明。

### 角色逐条对应我的消息回复

把消息队列模式从 `interrupt` 改为 `collect`，并设置 `debounceMs: 5000`。这会让连发的多条消息合并成一个 turn 再发给模型，避免逐条处理。详见推荐配置中的"消息合并"部分。

### Skills 是必须安装的吗？

不是。Skills 是增强能力，核心功能（情绪 + 记忆 + 心跳 + 主动消息）完全不依赖任何 skill。不装 skills，HEARTBEAT.md 中的学习回顾步骤会在没有对应目录时自动跳过，不影响其他功能。

### .learnings/ 里的文件一直是空的

这是正常的。学习条目由 self-improving-agent 在对话过程中自动记录，需要时间积累。模板只提供结构定义，实际内容在使用中自然产生。

### 角色看不到我发的贴纸

静态贴纸需要配置支持 vision 的 provider（如 Google Gemini Flash），且 provider key 名必须在 OpenClaw 白名单中。动画贴纸和视频贴纸是框架限制，目前无法识别。详见推荐配置中的"贴纸 vision"部分。

### brainrepo / agent-chronicle 相关的步骤报错

这两个 skill 已从核心流程中移除，降级为可选增强。如果你没有安装它们，相关报错可以忽略。如果想使用，按 [推荐 Skills](#推荐-skills) 中"可选增强"分类的说明安装即可。

## License

MIT
