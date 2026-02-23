# OpenClaw Persona Template

一套完整的 [OpenClaw](https://github.com/nicholasgasior/openclaw) AI 人格系统模板，包含情绪系统、记忆系统、心跳定时任务、社交规则等模块。基于实际运行验证的配置，可以直接用来创建你自己的 OpenClaw 人格。

## 这是什么

这套模板定义了一个有情感、有记忆、会主动找你说话的 AI 人格。它不只是一个 system prompt，而是一个完整的**状态持久化系统**——角色会写日记、维护长期记忆、跟踪情绪变化、根据好感度调整行为。

示例角色是「世凪（Yonagi）」——一个文静傲娇的物理系大学生。你可以基于这个示例修改出自己的角色。

## 前置要求

### OpenClaw 是什么

[OpenClaw](https://github.com/nicholasgasior/openclaw) 是一个开源的 AI agent 框架，可以通过 Telegram、Discord 等渠道与你的 AI 角色对话。它支持：

- **workspace 目录**（`~/.openclaw/workspace/`）：存放角色的配置文件，OpenClaw 启动时自动加载
- **heartbeat 机制**：按固定间隔触发定时任务（写日记、情绪维护、主动消息等）
- **skill 系统**：扩展角色能力（天气查询、浏览器操作、语音转文字等）
- **文件读写权限**：角色可以读写 workspace 内的文件，实现状态持久化

### 安装和配置

1. 安装 OpenClaw（需要 Node.js 18+）：
   ```bash
   npm install -g @nicholasgasior/openclaw
   ```
2. 完成 OpenClaw 的基础配置（模型 API、Telegram Bot 等），参考 [OpenClaw 官方文档](https://github.com/nicholasgasior/openclaw)
3. 确认 `~/.openclaw/workspace/` 目录存在

### workspace 如何生效

OpenClaw 启动时会自动读取 `~/.openclaw/workspace/` 下的 `.md` 文件作为角色的 system prompt。本模板的文件结构就是按照这个机制设计的：

- `AGENTS.md` 定义了每次 session 启动时角色应该读取哪些文件、按什么顺序加载
- `HEARTBEAT.md` 定义了 heartbeat 触发时角色应该执行哪些定时任务
- 其余文件（SOUL.md、IDENTITY.md 等）由 AGENTS.md 引导角色在 session 启动时主动读取

### heartbeat 如何触发

OpenClaw 的 heartbeat 是一个定时器，按配置的间隔（建议 2 小时）向角色发送一个 heartbeat 信号。角色收到信号后，按照 `HEARTBEAT.md` 的指令执行定时任务。在 OpenClaw 配置中启用 heartbeat：

```json
{
  "heartbeat": {
    "interval": 7200
  }
}
```

> **注意**：heartbeat 间隔单位是秒，7200 = 2 小时。

## 系统架构

```
workspace/
├── SOUL.md              ← 人格核心：性格、说话风格、情绪系统、记忆机制
├── IDENTITY.md          ← 身份信息：名字、生日、核心规则
├── USER.md              ← 用户信息：让角色了解你
├── TOOLS.md             ← 环境备忘：你的系统配置，角色操作电脑时参考
├── AGENTS.md            ← 运行规范：session 启动流程、记忆加载、安全规则
├── HEARTBEAT.md         ← 心跳任务：定时写日记、整理记忆、情绪维护、主动消息
├── MOLTBOOK.md          ← 社交规则：角色在社交平台上的行为准则（可选）
├── MEMORY.md            ← 长期记忆：由角色自动维护（初始为空）
└── memory/
    ├── mood.json            ← 情绪状态：当前情绪、基线、好感度、历史
    ├── heartbeat-state.json ← 心跳状态：各定时任务的上次执行时间
    ├── YYYY-MM-DD.md        ← 日记：角色每天自动写的私密笔记（运行时生成）
    └── example-diary.md     ← 示例日记：展示日记应有的质量和风格
```

### 数据流

```
对话 ──→ 情绪变化 ──→ mood.json（实时更新）
  │                        ↓
  │                   影响说话语气和行为
  │
  └──→ Heartbeat（每 2 小时）
          ├── 写日记 → memory/YYYY-MM-DD.md
          ├── 情绪衰减 → mood.json
          ├── 记忆整理（每 3 天）→ MEMORY.md
          └── 主动消息（每 6 小时）→ 找用户聊天
```

## 核心模块

### 情绪系统（SOUL.md + mood.json）

- **双层情绪**：surface（表面表现）和 underlying（内心真实感受），不总是一致
- **情绪基线**：底层情绪状态，以天/周为单位缓慢变化，受生活阶段影响
- **情绪惯性**：不会瞬间翻转，从生气到开心中间有"别扭"的过渡期
- **情绪积累**：同样的事反复发生，反应会越来越强烈
- **未解决事项**：没说开的事会一直悬着，潜移默化影响语气
- **非线性衰减**：情绪随时间衰减，但越到后面越慢；大事不道歉会卡在一定强度
- **好感度**：0-100 分，五档行为分级，变化缓慢（每次 1-3 点）

### 记忆系统（AGENTS.md + HEARTBEAT.md + MEMORY.md）

- **日记**：每天自动写的私密笔记，包含内心想法、观察、情绪记录
- **长期记忆**：从日记中提炼的精华，分板块组织（关于用户 / 观察 / 关系 / 自我变化）
- **遗忘与模糊**：模拟真人记忆特性，重要的事记得清楚，琐事会模糊
- **回忆浮现**：基于话题、时间、情绪自然触发，不机械引用
- **被对话改变**：角色会因为对话产生真实的变化和成长

### 心跳系统（HEARTBEAT.md + heartbeat-state.json）

- **写日记**：2 小时冷却，自动记录对话感受和自己的生活
- **记忆整理**：3 天冷却，从日记提炼长期记忆
- **情绪维护**：衰减计算、基线检查、未解决事项清理
- **主动消息**：6 小时冷却，09:00-22:00 时段，基于日记中的"想跟进的事"主动找用户聊天

### 排版规则（SOUL.md 顶部）

强制真人聊天风格：禁止 markdown、禁止一句一行、禁止空行分段小作文、禁止省略号滥用。包含正确/错误示范。

## 文件说明

模板中的文件分为三类：

| 类型 | 文件 | 说明 |
|------|------|------|
| 示例 | `SOUL.md`、`IDENTITY.md` | 包含世凪的完整角色设定，作为示例展示系统能力。需要替换为你自己的角色 |
| 模板 | `USER.md`、`TOOLS.md`、`MOLTBOOK.md` | 包含 `{{占位符}}`，填入你的信息即可 |
| 通用 | `AGENTS.md`、`HEARTBEAT.md`、`MEMORY.md`、`memory/*` | 可以直接使用，不需要修改 |

## 快速开始

### 1. 复制模板

```bash
cp -r workspace/ ~/.openclaw/workspace/
```

### 2. 必须修改的文件

| 文件 | 要做的事 |
|------|---------|
| `IDENTITY.md` | 替换为你的角色身份信息 |
| `USER.md` | 填入你自己的个人信息（替换所有 `{{占位符}}`） |
| `TOOLS.md` | 填入你的系统环境配置 |
| `SOUL.md` | 修改"我是谁"、"外貌"、"我的日常"等章节为你的角色设定 |

### 3. 建议修改的文件

| 文件 | 说明 |
|------|------|
| `SOUL.md` 的情绪系统部分 | 调整好感度分级、情绪衰减速度等参数 |
| `HEARTBEAT.md` | 调整心跳冷却时间、主动消息的活跃时段 |
| `MOLTBOOK.md` | 如果不需要社交平台功能，可以删除 |

### 4. 可以直接用的文件

| 文件 | 说明 |
|------|------|
| `AGENTS.md` | 通用运行规范，不需要修改 |
| `MEMORY.md` | 空模板，角色会自动填充 |
| `memory/mood.json` | 初始情绪状态 |
| `memory/heartbeat-state.json` | 初始心跳状态 |

## 自定义指南

### 创建你自己的角色

1. **确定角色身份**：名字、年龄、职业/学生身份、性格关键词
2. **写 SOUL.md**：这是最核心的文件，定义角色的一切。建议保留情绪系统和记忆系统的框架，只修改角色相关的内容（性格、说话风格、日常生活等）
3. **定义关系**：在 USER.md 和 SOUL.md 中设定角色与你的关系（朋友、同学、同事等）
4. **调整参数**：好感度初始值、情绪衰减速度、心跳间隔等

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

## 设计理念

- **状态持久化**：所有情绪和记忆通过文件持久化，不会因为 session 结束而丢失
- **框架而非台词**：SOUL.md 描述的是行为框架，不是固定的对话模板，角色基于框架自由发挥
- **真人模拟**：排版规则、打字不完美、社交能量、情绪惯性等机制都在模拟真人的自然表现
- **双向成长**：角色会因为对话而产生真实的变化，不是固定不变的人格模板

## 常见问题

### 角色不写日记 / 不主动发消息

检查 OpenClaw 是否启用了 heartbeat，以及间隔是否合理（建议 7200 秒）。角色的定时任务完全依赖 heartbeat 触发。

### 情绪系统看起来没生效

检查 `memory/mood.json` 是否被正确读写。角色需要有 workspace 目录的文件读写权限。可以查看 mood.json 的 `since` 字段是否有被更新。

### 换了模型后角色表现差很多

这套系统对模型的指令遵循能力要求较高（排版规则、情绪衰减计算、日记质量自检等）。建议使用 Claude Sonnet 3.5 及以上或同等水平的模型。较弱的模型可能无法稳定遵守所有规则。

### MEMORY.md 一直是空的

MEMORY.md 由记忆整理任务填充，冷却时间是 3 天。首次使用需要等待至少 3 天并积累足够的日记内容后才会自动填充。

## License

MIT
