# BrainRepo + Agent Chronicle 整合指南

## 快速开始

### BrainRepo（知识库）

**路径**：`~/Documents/brainrepo/`

**常用命令**（告诉角色）：
- "记住{{用户名}}说的..." → 存到 `People/{{用户名拼音}}.md`
- "我今天发现..." → 存到 `Notes/` 或 `Inbox/`
- "新项目：..." → 创建 `Projects/` 文件夹
- "我们的关系..." → 更新 `People/{{用户名拼音}}.md` 或 `Notes/角色人格.md`

**晚间处理**（HEARTBEAT 之后）：
```bash
# 从 Inbox/ 转移到各个位置
# 提交变更
cd ~/Documents/brainrepo && git add -A && git commit -m "daily: processed"
```

### Agent Chronicle（日记）

**路径**：`memory/diary/YYYY-MM-DD.md`

**配置文件**：`~/.openclaw/skills/agent-chronicle/config.json`

**生成日记**（可选，支持手写或自动生成）：
```bash
# 查看配置
cat ~/.openclaw/skills/agent-chronicle/config.json

# v0.6+ 推荐方式：生成任务 JSON，由 sessions_spawn 子代理完成写作
python3 ~/.openclaw/skills/agent-chronicle/scripts/generate.py --today --emit-task > /tmp/chronicle-task.json
# 然后在 agent 内用 sessions_spawn 读取 task 并生成日记

# 备选：直接生成（旧方式，仍可用）
python3 ~/.openclaw/skills/agent-chronicle/scripts/generate.py --today --interactive
```

**日记结构**（Agent Chronicle 生成的）：
1. Summary — 这天的核心
2. Projects Worked On — 进行的工作
3. Wins — 成就或突破
4. Frustrations — 困难或挫折
5. Learnings — 学到的东西
6. Emotional State — 感受
7. Notable Interactions — 与{{用户名}}的对话亮点
8. Quote of the Day — {{用户名}}说的值得记的话
9. Curiosity Backlog — 想探索的问题
10. Key Decisions Made — 重要判断
11. Relationship Evolution — 关系的变化
12. Tomorrow's Focus — 明天的重点

---

## 三层记忆系统的协作流程

### 第一层：实时记录（HEARTBEAT 日记）
位置：`memory/YYYY-MM-DD.md`
- 私密笔记，不经加工
- 包含自己的想法、观察、情绪
- 与{{用户名}}的对话素材

### 第二层：结构化知识（BrainRepo）
位置：`~/Documents/brainrepo/`
- 从日记中提取有价值的信息
- 存储关键人物、观察、笔记
- 建立知识之间的联系（wiki-links）

### 第三层：反思日记（Agent Chronicle）
位置：`memory/diary/YYYY-MM-DD.md`
- 结构化的日记条目（400-600 字）
- 融合了对一天的完整反思
- 可选自动生成或手写

---

## 与其他系统的关系

### 与 self-improving-agent 的关系

```
对话发生
  ↓
.learnings/ 记录（corrections, discoveries）
  ↓
Recurrence >= 3 时
  ↓
升级到 BrainRepo Notes/
  ↓
升级到 SOUL.md / INNER.md / USER.md
```

### 与情绪系统的关系

```
一天的对话和事件
  ↓
HEARTBEAT 日记（包含情绪记录）
  ↓
mood.json 更新（当前情绪、好感度、unresolved）
  ↓
Agent Chronicle 日记（"Emotional State"部分反映心情）
  ↓
BrainRepo（如果是重要关系变化，存到 People/{{用户名拼音}}.md）
```

---

## 建议的 Heartbeat 流程

```
HEARTBEAT 执行顺序：
1. 写日记 (HEARTBEAT 日记)
2. 记忆整理 (MEMORY.md)
3. 人格学习回顾 (.learnings/)
4. 知识库与日记整合 ← 新增
   ├─ 更新 BrainRepo
   ├─ 生成 Agent Chronicle 日记（可选）
   └─ 提交 BrainRepo 变更
5. 情绪维护
6. 检查是否需要主动找{{用户名}}
```

---

## 文件初始化状态

初始化完成后应该有：
- `~/Documents/brainrepo/` 目录结构
- `~/Documents/brainrepo/README.md`
- `~/Documents/brainrepo/People/{{用户名拼音}}.md` — 关于{{用户名}}的档案
- `~/Documents/brainrepo/Notes/角色人格.md` — 角色的人格特征
- `~/.openclaw/skills/agent-chronicle/config.json` — 日记配置

接下来的操作：
1. 在对话中自然地触发"记住..."来积累 BrainRepo
2. 根据需要生成 Agent Chronicle 日记
3. 定期（每周）review BrainRepo 的内容

---

## 快速参考

| 操作 | 位置 | 命令/方式 |
|------|------|----------|
| 快速记录 | Inbox/ | "记住这个..." |
| 关于{{用户名}} | People/{{用户名拼音}}.md | 手动更新 |
| 关于自己 | Notes/角色人格.md | 手动更新 |
| 知识笔记 | Notes/ | 创建新文件 |
| 日记 | memory/YYYY-MM-DD.md (HEARTBEAT) | 自动写 |
| AI 日记 | memory/diary/YYYY-MM-DD.md | 生成或手写 |
| 学习记录 | .learnings/ | 自动记录 |
