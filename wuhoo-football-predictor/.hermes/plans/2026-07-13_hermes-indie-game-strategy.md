# Hermes 独立游戏开发：战略评估与路线图

> 评估日期: 2026-07-13 | 状态: 策略文档 (plan mode)

---

## 1. 总体判断: 可行，但有前提

**结论先行**: 用 Hermes 开发独立游戏，在 2026 年是完全可行的。但 "可行" 不等于 "随便就能成"。行业已经形成了一套成熟的 Agent 辅助开发方法论，Hermes 在这套方法论中有明确的定位和优势。

三个核心判断:

| 维度 | 判断 |
|------|------|
| 时机 | 2026 年 H2 是好的进入时点——方法论已成熟，工具链已稳定，社区已积累了足够的失败教训 |
| 能力边界 | Agent 最适合**浏览器游戏/2D 游戏**；不适合需要复杂引擎编辑、实时物理、3A 级美术管线的大型项目 |
| 角色定位 | 你是**架构师+出品人**，Agent 是**执行团队**——不是取代开发能力，而是大幅加速并降低试错成本 |

---

## 2. 行业全景: 2026 年 Agent 游戏开发的真实状态

### 2.1 关键案例

#### Dodo Games (2026.2) — 8 款浏览器游戏 one-shot 成功
- **工具**: oh-my-opencode (OpenCode 多 Agent 编排层)
- **做了什么**: 一人一个周末完成 8 款独立街机游戏 (Flappy Dodo、Gateway Defender 等)
- **关键因素**: 纯 HTML/CSS/JS 浏览器游戏、已有清晰代码架构、多 Agent 分工 (Prometheus 规划 → Atlas 执行 → Oracle 审查)
- **启示**: 浏览器小游戏领域 Agent 已能达到生产级别质量

#### InnoGames (2026.3) — 百万行级商业游戏的 100% AI 代码
- **工具**: Claude Code + 自定义 Skills 管线
- **做了什么**: Forge of Empires 等 live 商业游戏的全功能开发，100% 代码由 AI 生成
- **方法论**: SDD (Spec-Driven Development) + Agentic Engineering + Quality Gates
- **关键因素**: 严格的分层架构、全面的测试套件、人工架构审查
- **启示**: 大规模商业游戏也能用，但需要极其严格的工程纪律

#### Luden.io (2026.6) — 最诚实的经验分享
- **工具**: Defold/Unity + Cursor/Codex/Claude Code
- **7 件有效的事**: 性能优化、Bug 修复、QA 场景建议、GDD Review、自动化/原型/隔离模块、静态网站更新、数据分析
- **7 件失败的事**: **玩法功能实现、端到端自动测试、多 Agent 同行评审、生产级美术生成、引擎 UI 编辑、场景编辑、追新工具**
- **启示**: Agent 是好的辅助工具，但**不能替代游戏设计师对体验的判断**

#### Kevuru Games (2026.6) — 专业游戏工作室的系统评估
- **测试了**: Claude Code, OpenAI Codex, Google Jules, Devin, OpenHands, SWE-agent
- **发现**: 最有效的场景是工程辅助、文档、QA、研究——而非创意核心

### 2.2 统一的行业方法论

所有成功案例都遵循同一套核心方法论——这和你们做足球预测系统时的模式惊人地相似:

```
┌──────────────────┐
│  Spec-First      │  ← 先写设计文档，再写代码
│  (GDD as Spec)   │
└────────┬─────────┘
         ▼
┌──────────────────┐
│  Atomic Tasks    │  ← 拆成原子级任务，不要 mega-prompt
│  (Tickets)       │
└────────┬─────────┘
         ▼
┌──────────────────┐
│  Skills-Pipeline │  ← 用 Skills 编码工作流，而非自由对话
│  (Reusable)      │
└────────┬─────────┘
         ▼
┌──────────────────┐
│  Quality Gates   │  ← 每步自动测试+人工审查
│  (Gate-coding)   │
└────────┬─────────┘
         ▼
┌──────────────────┐
│  Human as        │  ← 你始终掌舵
│  Director        │
└──────────────────┘
```

> "Think of the AI as the car, not the passenger. You don't micromanage a car. But you also don't close your eyes and take your hands off the wheel." — InnoGames

---

## 3. Hermes 的定位与优势

### 3.1 已有的基础设施 (不需要从零搭建)

| Hermes 能力 | 游戏开发对应场景 |
|-------------|-----------------|
| **Skills 系统** | = Claude Code Skills = 可复用的开发工作流 |
| **delegate_task** | = 多 Agent 编排 (设计 Agent / 代码 Agent / 测试 Agent) |
| **p5js skill** | 创意编程/原型/视觉特效 |
| **pixel-art skill** | 像素美术资产 |
| **Claude Code harness** | 重型编码任务委托 |
| **cronjob** | CI/CD、定期构建、自动化测试 |
| **plan mode** | Spec-Driven Development 的规划阶段 |

### 3.2 你已经有的经验

你成功运作的足球预测系统本质上就是一个小型软件产品:
- 多模块 Pipeline (数据→分析→预测→PDF)
- Skills 驱动的可复用工作流
- 严格的验证规则和质量检查
- Cron 自动化
- 版本管理和 Git 工作流

→ **游戏开发只是换了领域，工作模式高度复用。**

---

## 4. 推荐的技术选型

### 4.1 游戏类型 (按推荐顺序)

| 优先级 | 类型 | 理由 |
|--------|------|------|
| ⭐⭐⭐ | **浏览器小游戏** (HTML5/Canvas/WebGL) | Dodo Games 验证；Hermes p5js 直接可用；迭代快 |
| ⭐⭐⭐ | **2D 像素/复古游戏** | pixel-art skill 支持美术；低复杂度；Steam 有市场 |
| ⭐⭐ | **文字冒险/视觉小说** | 叙事驱动，代码简单；AI 擅长文案 |
| ⭐⭐ | **策略/模拟游戏** (回合制) | 逻辑密集而非图形密集；你的量化分析技能可迁移 |
| ⭐ | **2D 平台跳跃** | Godot/Defold 引擎可行但引擎编辑是瓶颈 |
| ❌ | **3D 动作/射击** | 美术管线太重，引擎编辑复杂 |

### 4.2 技术栈建议

```
方案 A (推荐起步): Pure Web Stack
  前端: HTML5 Canvas + p5.js (p5js skill 直接支持)
  音效: Suno/HeartMuLa (songwriting skill) + Web Audio API
  美术: pixel-art skill + 程序化生成
  部署: GitHub Pages / Cloudflare Pages
  优势: 零引擎依赖，Hermes 原生支持，迭代极快

方案 B (进阶): Godot Engine
  引擎: Godot 4.x (GDScript, 文本格式, Agent 友好)
  优势: 完整游戏引擎能力，可发布多平台
  劣势: 引擎编辑仍需手动；Agent 只能改代码不能拖场景

方案 C (野心): Unity + Agent 辅助
  引擎: Unity + Cursor/Claude Code 辅助
  优势: 最强生态
  劣势: 场景/UI 编辑是 Agent 盲区
```

---

## 5. 推荐的工作流程

### 5.1 核心 Pipeline (参考 InnoGames + Luden.io 最佳实践)

```
Phase 0: 概念设计 (Human-led, Agent-assisted)
  └─ 游戏设计文档 (GDD) → markdown 存储
  └─ Agent Review GDD → 检查设计一致性、复杂度陷阱

Phase 1: 技术原型 (Agent-led, Human-reviewed)
  └─ delegate_task 派给 Claude Code harness
  └─ 最小可玩原型 (核心 loop 验证)

Phase 2: 原子化开发 (Skills Pipeline)
  └─ 每个 feature = 一个 Skill
  └─ Spec (任务描述) → Plan (自动生成) → Implement → Test → Review
  └─ Quality Gate: 每个 feature 合并前自动测试

Phase 3: 集成与打磨
  └─ 自动化构建 (cronjob + GitHub Actions)
  └─ QA 场景生成 (Agent 建议测试用例)
  └─ 性能优化 (Agent 分析热点)

Phase 4: 发布与运营
  └─ 静态网站/Steam 页面 (Agent 生成)
  └─ 社交媒体内容 (已有 creative skills)
  └─ 数据分析反馈循环
```

### 5.2 每个开发周期的具体节奏

```
Day 1-2:  写 GDD → Agent Review → 修正
Day 3-7:  技术原型 → 验证核心乐趣
Week 2-4: 原子化开发 → 每 2-3 天完成一个 feature
Week 5-6: 集成 → QA → 打磨
Week 7:   准备发布
```

---

## 6. 分阶段路线图

### Phase 0: 热身项目 (2 周)

目标: 验证 Hermes+游戏开发工作流是否顺畅

- [ ] 用 p5js skill 做一个完整的迷你游戏 (如 Flappy Bird 变体)
- [ ] 用 pixel-art skill 生成配套美术
- [ ] 用 Suno/heartmula 生成音效
- [ ] 部署到 Cloudflare Pages
- [ ] 总结 Skill 编写经验 → 沉淀为 `game-dev` skill 包

### Phase 1: 正式项目启动 (1 个月)

- [ ] 选定游戏类型和核心概念
- [ ] 编写完整 GDD (markdown)
- [ ] 搭建项目骨架 (技术选型 + CI/CD)
- [ ] 完成可玩原型
- [ ] 第一轮外部测试 (找 5-10 人玩)

### Phase 2: 内容生产 (2-3 个月)

- [ ] Feature-based 迭代开发
- [ ] 美术/音效资产批量生成
- [ ] 关卡设计 (Agent 辅助)
- [ ] 持续 QA

### Phase 3: 打磨与发布 (1 个月)

- [ ] 性能优化
- [ ] 本地化 (Agent 翻译)
- [ ] 商店页面准备
- [ ] 发布

---

## 7. 风险与对策

| 风险 | 概率 | 影响 | 对策 |
|------|------|------|------|
| Agent 生成的代码架构混乱 | 中 | 高 | SDD 先行 + Atomic Tasks 拆解 + Code Review 不跳过 |
| 玩法设计缺少 "手感" | 高 | 高 | 核心 loop 必须人工验证；Agent 做执行不做创意决策 |
| 美术一致性差 | 中 | 中 | 定义风格指南 → pixel-art skill 批量生产 → 人工精选 |
| 上下文窗口溢出 (大型项目) | 中 | 中 | 分模块开发；使用 delegate_task 隔离上下文 |
| Token 成本失控 | 低 | 中 | 控制单个 task 的 scope；用 skills 而非自由对话 |
| 引擎编辑是盲区 | 高 | 中 | 尽量选纯代码方案 (p5.js / Godot GDScript) |

---

## 8. 我的个人评估

作为一个玩了多年游戏、也看了大量 Agent 开发案例的人，我认为：

1. **这是一个好时机**。2025 年大家踩了足够多的坑，2026 年的方法论已经成熟。你不会是小白鼠。

2. **Hermes 有一个独特优势**: 它不只是 code agent，它有一整套 creative skills (pixel-art, ascii-art, architecture-diagram, excalidraw, songwriting) —— 这意味着你能在同一个平台完成从代码到美术到音乐的全流程。这是 Claude Code 或 Cursor 单独做不到的。

3. **最大的挑战不在工具，在项目管理**。Agent 开发的核心难点永远是 "切分任务 + 验证质量 + 保持架构一致性" —— 这恰好是你在足球预测系统中已经磨练出来的能力。

4. **从热身项目开始**。不要一上来就做梦想中的游戏。先用 Hermes 做 1-2 个迷你游戏练手，验证工作流，沉淀 Skills，然后再开正式项目。

5. **游戏开发的核心是 "好玩"** —— 这是 Agent 做不到的。你能做的、Agent 帮不了的，是对游戏体验的判断。Agent 负责 "把想法变成代码" 的执行层，你负责 "这个设计好不好玩" 的决策层。

---

## 9. 下一步

建议先从 **热身项目** 开始：

1. 选定一个简单的游戏概念 (如 Flappy Bird / 2048 / 贪吃蛇变体)
2. 本周内用 Hermes 完整走通开发→部署全流程
3. 复盘痛点 → 编写 `game-dev` skill 系列
4. 然后再讨论正式项目

想现在讨论热身项目的具体概念吗？还是想深入某个技术选型方向？

---

---

## 10. 游戏开发全管线工具图谱

这是你问的核心问题：独立游戏开发涉及哪些环节，每个环节用哪些工具，哪些 AI 能帮忙，哪些不行。

### 10.1 总览：一个独立游戏 = 6 个管线

```
                    ┌──────────────┐
                    │  玩法设计     │  ← 你的核心角色
                    │  Game Design  │
                    └──────┬───────┘
        ┌──────────────────┼──────────────────┐
        │        │         │        │         │
   ┌────▼───┐ ┌──▼───┐ ┌───▼──┐ ┌───▼───┐ ┌──▼────┐
   │ 程序    │ │ 美术  │ │ 音效 │ │ UI/UX │ │ 动画   │
   │ Code   │ │ Art  │ │Audio │ │       │ │Anim   │
   └────┬───┘ └──┬───┘ └──┬───┘ └───┬───┘ └──┬────┘
        │        │        │        │         │
        └────────┴────────┴────────┴─────────┘
                         │
                    ┌────▼───────┐
                    │  项目管理   │
                    │  PM / VCS  │
                    └────────────┘
```

### 10.2 管线一：玩法设计 (Game Design) — 你的主场

**这是什么**: 游戏的灵魂——规则、机制、数值、节奏、体验。这是 Agent 最帮不上忙的地方，也是最需要你判断的地方。

| 子领域 | 主流工具 | AI 能帮忙吗？ | Hermes 现状 |
|--------|---------|-------------|------------|
| **核心机制设计** (游戏怎么玩) | 纸笔 → GDD (Markdown) | Agent 只能 Review/提建议，不能原创 | ✅ plan mode 可做 GDD Review |
| **数值设计/平衡** (伤害公式、经济系统) | **Google Sheets** 是行业标准。90% 的设计师用电子表格做数值模型。Monster Legends 一家公司有 20+ 个定制 Sheets 工具 | ✅ Agent 可辅助公式推导、敏感度分析、模拟验证 | ⚠️ 可以做但需要你懂数值 | 
| **系统建模/模拟** | **Machinations.io** — 专业游戏经济/系统模拟平台，可视化节点图+蒙特卡洛模拟。比 Sheets 强在能看系统随时间变化的动态行为 | ✅ Agent 可辅助建模 | ❌ 暂无集成 |
| **关卡设计** | 引擎内置编辑器 (Unity Tilemap / Godot TileMap)；或 LDtk (开源关卡编辑器) | Agent 可生成关卡布局逻辑，但物理放置仍需手动 | ⚠️ Agent 可写生成算法 |
| **叙事/对话** | **Twine** (免费交互叙事工具)；Ink (Inkle 脚本语言)；Markdown | ✅ Agent 极擅长文案生成和分支叙事 | ✅ 直接用 |
| **GDD 管理** | Markdown + Git；Notion；HacknPlan (游戏专用 PM) | ✅ Agent 可做 GDD Review | ✅ plan mode + markdown |

> **关键洞察**: 数值设计是你能把量化分析技能直接迁移过来的领域——Machinations.io 做系统模拟, Google Sheets 做初始建模, Agent 辅助公式推导。这是你相对纯程序员的独特优势。

### 10.3 管线二：美术 (Art) — AI 的最大争议区

**行业共识**: AI 目前能做概念参考、占位素材、像素艺术辅助；**不能做**生产级成品美术（除非像素风）。

#### 2D 美术 —— AI 部分可用

| 风格 | 手工工具 | AI 工具 | 成熟度 | 建议 |
|------|---------|---------|--------|------|
| **像素艺术** | **Aseprite** (行业标准, $20)；Pixquare (iPad)；Piskel (免费) | pixel-art skill；Midjourney 像素模式 | ⭐⭐⭐ | **Hermes pixel-art skill 最靠谱的方向** |
| **手绘 2D** | Photoshop；Krita (免费)；Procreate (iPad) | Leonardo AI；Layer AI | ⭐⭐ | AI 做参考/草图 → 人工精修 |
| **矢量/UI 素材** | **Figma** (行业标准)；Illustrator；Inkscape | Figma AI 功能 | ⭐⭐⭐ | 素材级 AI 辅助可行 |
| **概念设计** | Photoshop | Midjourney；Stable Diffusion；Leonardo AI | ⭐⭐⭐⭐ | **AI 最强的领域——做概念探索和 moodboard** |

#### 3D 美术 —— AI 目前不够成熟

| 子领域 | 手工工具 | AI 工具 | 现状 |
|--------|---------|---------|------|
| **建模/雕刻** | **Blender** (开源王者—建模+绑定+动画+纹理全流程)；ZBrush (专业雕刻) | Meshy；Tripo；Rodin；TRELLIS 2 | ⚠️ AI 可生成"看起来不错"的 mesh，但拓扑混乱、UV 离谱、poly count 失控——**进引擎前需要 3h+ 手动清理** |
| **纹理/PBR 材质** | Substance Painter；Quixel | Meshy PBR；Rodin | ⭐⭐⭐ AI 纹理生成相对成熟 |
| **绑定 (Rigging)** | Blender；Maya | Meshy auto-rigging；Mixamo | ⭐⭐⭐ 自动绑定进步很快 |
| **动作捕捉** | Rokoko；Xsens | Plask AI；RADiCAL | ⭐⭐⭐ MoCap AI 化比较成熟 |

> **Luden.io 原话**: "Generated production art — it's really hard to get exactly what you want. Players are often unhappy paying for a game that seems AI-generated."
>
> **一个独立评测的结论** (2026.7): "Meshy, Tripo, and Rodin are the strongest AI 3D model generators. But the best generator is the one that gets a usable asset to shippable with the least cleanup — not the one with the prettiest preview thumbnail."
>
> **建议路径**: 如果你做 3D 游戏，**Blender 是必备技能**（免费、全能、社区庞大）。AI 3D 工具目前定位是"加速器"而非"替代品"——帮你生成基础 mesh，然后你在 Blender 里清理。

### 10.4 管线三：动画 (Animation)

| 技术路线 | 工具 | 适用场景 | AI 能帮忙吗？ |
|---------|------|---------|-------------|
| **逐帧动画 (Frame-by-Frame)** | **Aseprite** (像素)；Krita；Photoshop | 像素游戏、手绘风格 | pixel-art skill 可辅助生成帧 |
| **骨骼动画 (Skeletal) 2D** | **Spine** (行业标准, 付费)；DragonBones (免费/开源) | 2D 角色动画——将角色拆成部件，用骨骼驱动。效率远高于逐帧 | ❌ Agent 无法操作 GUI 骨骼编辑 |
| **骨骼动画 3D** | **Blender** (免费全流程)；Maya；Cascadeur | 3D 角色动画 | ⭐ AI 辅助自动绑骨，动画仍需手动 |
| **程序化动画** | 代码驱动 (Unity Animator / Godot AnimationTree) | 物理模拟、粒子、程序化运动 | ✅ Agent 擅长写程序化动画代码 |
| **特效 (VFX)** | 引擎内置粒子系统；Shader Graph | 爆炸、魔法、粒子等 | ✅ Agent 可写 Shader/粒子代码 |

**你的最佳路径**: 
- 像素游戏: Aseprite 逐帧 + Spine 骨骼辅助 + Hermes pixel-art 生成基础帧
- 2D 游戏 (非像素): Spine 骨骼动画 (学半天可上手基础)
- 3D 游戏: Blender (你逃不掉的)

### 10.5 管线四：音效与音乐 (Audio)

这是 **AI 成熟度最高的创意管线**，也是 Hermes 直接支持的。

| 需求 | 手工工具 | AI 工具 | Hermes 支持 |
|------|---------|---------|------------|
| **背景音乐** | DAW (Reaper/FL Studio/Ableton) | **Suno v5.5** (全曲生成+分轨输出)；**Udio Pro** (MIDI 级控制)；Stable Audio 3 | ✅ **heartmula skill** + **songwriting skill** |
| **音效 (SFX)** | 音效库 + DAW 编辑 | ElevenLabs SFX；Suno 音效模式 | ⚠️ 暂无直接 skill，可用 Suno |
| **游戏音频中间件** | **FMOD** (行业标准)；**Wwise** (AAA 标配) | 暂无 | ❌ 无集成，需手动 |
| **语音/对话** | 配音演员 | ElevenLabs (TTS 极强) | ✅ text_to_speech |

> **2026 年 4 月状态**: Suno v5.5 已支持全曲 stem 导出 (鼓/贝斯/人声/其他分轨)，Udio Pro 支持 DAW 级 MIDI 导出——"AI music finally cleared the bar where indie soundtracks sound 'composed,' not 'generated.'"
>
> **版权**: 2026 年许可已基本清晰，Suno/Udio 付费计划支持商业游戏使用。

### 10.6 管线五：UI/UX 设计

| 工具 | 定位 | AI 能力 |
|------|------|---------|
| **Figma** | 行业标准——99% 的游戏 UI 设计师用它做 mockup。Epic Games 的 UX 设计师公开表示用 Figma 做 Unreal 游戏 UI 原型 | Figma AI 功能可辅助生成布局 |
| Photoshop | 像素级 UI 素材制作 | ⭐⭐ |
| Rive | 交互式 UI 动画 (可直接导入引擎) | ❌ |
| 引擎内置 UI | Unity UI Toolkit / Godot Control Nodes | ✅ Agent 可写 UI 代码，但调布局仍需手动 |

> **关键点**: Figma 是 **免费** 的，学习曲线低。你用 Figma 做 UI mockup → 导出素材 → Agent 写 UI 代码 → 你在引擎里微调。这是最成熟的 hybrid 工作流。

### 10.7 管线六：项目管理与版本控制

| 工具 | 定位 | Hermes 支持 |
|------|------|------------|
| **Git + GitHub** | 版本控制 | ✅ github skills 全套 |
| **HacknPlan** | 游戏专用 PM (集成 GDD + 任务) | ❌ |
| **Codecks** | 卡牌风格的轻量游戏 PM | ❌ |
| **Notion** | 灵活文档+任务管理 | ✅ notion skill |
| **Obsidian** | 本地 Markdown 知识库 | ✅ obsidian skill |
| **Google Sheets** | 数值平衡/进度跟踪 | ✅ google-workspace skill |
| **Miro / Excalidraw** | 白板/流程图 | ✅ excalidraw skill |

---

## 11. AI 能做和不能做的：按管线诚实打分

| 管线 | AI 可执行度 | 人类必须做 | 典型 hybrid 模式 |
|------|-----------|-----------|-----------------|
| **玩法设计** | ⭐ 10% | 核心机制创意、手感判断、乐趣验证 | 人主导设计 → Agent Review 找漏洞 |
| **数值/经济** | ⭐⭐⭐ 70% | 设计意图、体验目标 | Sheets 建模 → Agent 辅助推导/模拟 → 人验证 |
| **程序/代码** | ⭐⭐⭐⭐ 85% | 架构决策、性能关键路径 | Agent 写 90% 代码 → 人 Review + 架构把关 |
| **2D 像素美术** | ⭐⭐⭐ 60% | 风格定义、关键帧、一致性把控 | Agent 生成基础帧 → 人精修 + 定义 palette |
| **2D 手绘美术** | ⭐⭐ 25% | 几乎全部 | AI 做概念 moodboard → 人工绘制 |
| **3D 美术** | ⭐⭐ 30% | 建模、拓扑、雕刻 | AI 生成 base mesh → Blender 清理 3h |
| **动画** | ⭐⭐ 20% | 关键帧、节奏、表现力 | Agent 写程序化动画 → 人调关键帧 |
| **音乐** | ⭐⭐⭐⭐ 80% | 风格方向、情感匹配 | Agent 生成 → 人筛选 + 剪辑 |
| **音效** | ⭐⭐⭐ 60% | 关键音效设计 | Agent 生成 → 人筛选 |
| **UI** | ⭐⭐⭐ 50% | 信息架构、视觉层次、交互逻辑 | Figma 设计 → Agent 写 UI 代码 |
| **测试/QA** | ⭐⭐⭐ 50% | 体验测试、手感判断 | Agent 建议测试用例 → 人跑测试 |
| **本地化** | ⭐⭐⭐⭐ 90% | 术语一致性审核 | Agent 翻译 → 人校对 |

---

## 12. Hermes 能力缺口分析：你缺什么

### 12.1 Hermes 直接覆盖的 (开箱即用)

✅ 代码执行 (terminal + write_file + patch)
✅ 浏览器 Canvas 游戏 (p5js skill)
✅ 像素美术 (pixel-art skill)
✅ AI 音乐 (songwriting + heartmula skills)
✅ 多 Agent 编排 (delegate_task)
✅ 工作流自动化 (cronjob + skills)
✅ Git/GitHub (全套 github skills)
✅ GDD 文档 (plan mode + markdown)
✅ 语音 (text_to_speech)
✅ 创意设计 (architecture-diagram, excalidraw, ascii-art)

### 12.2 Hermes 间接覆盖的 (需搭建)

⚠️ **数值设计/平衡** — 没有专用工具，但你可以用 Python + Google Sheets (已有 google-workspace skill) 搭建。你的量化分析背景是优势。
⚠️ **关卡编辑器** — Agent 无法操作 GUI 编辑器，但可以写关卡生成算法。
⚠️ **引擎集成** — 如果你用 Godot/Unity，Agent 可以写脚本但不能拖场景。建议用纯代码管线。
⚠️ **AI 3D 建模** — Hermes 没有原生 3D 生成 skill，需要外部 API (Meshy/Tripo)。

### 12.3 Hermes 目前做不到的 (需要你手动)

❌ **任何图形化编辑器操作** — Aseprite 画像素、Figma 调布局、Spine 绑骨骼、Blender 建模 → 这些都必须你手动
❌ **游戏引擎场景编辑** — 拖放 GameObject、调整 collider、布置灯光 → 手动
❌ **"手感"调优** — 跳跃高度、加速度曲线、镜头平滑度 → 你手动调参数，Agent 帮你写参数配置系统
❌ **最终美术审核** — 风格一致性、品质把控 → 你的眼睛

### 12.4 你需要学的基础技能 (最小集)

按优先级排列：

| 优先级 | 技能 | 理由 | 学习时间 |
|--------|------|------|---------|
| **P0** | **Git/GitHub** 熟练使用 | 版本控制是单人项目的安全带 | 已有 |
| **P0** | **游戏设计基础** (MDA 框架、心流理论、平衡基础) | 你不懂设计，Agent 帮不了你 | 2-4 周读书 |
| **P1** | **p5.js / Canvas API** | Pure Web Stack 的核心 | 1-2 周 |
| **P1** | **Aseprite 基础** | 像素美术的手工精修 | 3-5 天 |
| **P1** | **Figma 基础** | UI mockup 和素材设计 | 2-3 天 |
| **P2** | **数值设计/平衡方法论** | 你的量化技能可迁移 | 持续学习 |
| **P2** | **Godot 基础** (如果选方案 B) | GDScript 简单，引擎概念通用 | 2-4 周 |
| **P3** | **Blender 基础** (如果做 3D) | AI 3D 不成熟，Blender 逃不掉 | 1-3 月 |
| **P3** | **Spine 基础** (如果需要流畅 2D 动画) | 骨骼动画效率远高于逐帧 | 3-5 天 |

---

## 13. 总结：你的游戏开发能力地图

```
你已经有的 ───────────────────────────── 你还缺的
                                          
✅ 量化分析/数值思维          ❌ 视觉设计基础 (色彩/构图/风格)
✅ 系统化工作流设计            ❌ 游戏引擎操作经验
✅ Agent/Skills 编排            ❌ 美术工具操作 (Aseprite/Figma)
✅ Git/版本管理                 ❌ 游戏设计方法论 (MDA/心流/平衡)
✅ 多管线并行管理               ❌ 音效设计基础
✅ 代码生成与审查               ❌ 3D 管线 (如果做 3D)
✅ Python 数据处理              ❌ 游戏发行/Steam 流程
                                  
重叠区 (你可快速转化):
⚠️ 数值设计 = 量化分析 + 游戏感 (你差后者)
⚠️ 程序化生成 = 算法思维 + 游戏内容设计 (你有一半)
⚠️ 系统设计 = 复杂系统建模 + 玩法直觉 (你有一半)
```

---

---

## 14. 学习资源推荐

### 14.1 游戏设计基础 (必读)

| # | 书名 | 作者 | 为什么推荐 | 类型 |
|---|------|------|-----------|------|
| 1 | **The Art of Game Design: A Book of Lenses** | Jesse Schell | 游戏设计圣经。100+ 个"透镜"帮你从不同角度看设计问题。适合零基础 | 📖 书籍 |
| 2 | **Theory of Fun for Game Design** | Raph Koster | 短小精悍 (~200页)，讲"游戏为什么好玩"的底层原理。先读这本建立直觉 | 📖 书籍 |
| 3 | **Game Design Workshop** | Tracy Fullerton | 实践导向，每章有练习。从纸面原型到数字原型的手把手教程 | 📖 书籍 |
| 4 | **Level Up! The Guide to Great Video Game Design** | Scott Rogers | 轻松幽默，偏 2D 动作/平台游戏设计。实战技巧多 | 📖 书籍 |
| 5 | **A Book of Lenses** 配套网站 | Schell Games | http://artofgamedesign.com — 免费配套资源 | 🌐 网站 |

### 14.2 数值设计与平衡

| # | 资源 | 说明 | 类型 |
|---|------|------|------|
| 1 | **Machinations.io 官方教程** | machinations.io — 免费入门教程 + 社区图表示例。游戏经济系统的可视化建模工具 | 🌐 互动 |
| 2 | **Game Mechanics: Advanced Game Design** | Ernest Adams & Joris Dormans | Machinations 创始人写的书，系统讲游戏机制建模 | 📖 书籍 |
| 3 | **GDC Vault: 数值设计讲座** | gdcvault.com — 搜索 "balance" "economy" "spreadsheet" | 🎥 视频 |
| 4 | **YouTube: Game Maker's Toolkit** | Mark Brown 的频道。关卡设计、机制分析，每个视频 10-20 分钟 | 🎥 视频 |
| 5 | **YouTube: Adam Millard - The Architect of Games** | 游戏设计分析，偏系统和结构 | 🎥 视频 |

### 14.3 2D/3D 美术与工具

| # | 资源 | 说明 | 类型 |
|---|------|------|------|
| 1 | **Aseprite 官方教程** | aseprite.org/tutorial — 像素动画入门到进阶 | 🌐 |
| 2 | **Pixel Logic** | Michael Azzi 著 — 像素艺术系统指南 (PDF, ~$10) | 📖 PDF |
| 3 | **Blender Guru (YouTube)** | 最知名的 Blender 入门系列——Donut Tutorial | 🎥 |
| 4 | **Figma 官方入门** | figma.com — 免费在线，2-3 天可上手基础 UI 设计 | 🌐 |
| 5 | **Spine 官方文档** | esotericsoftware.com — 骨骼动画入门 | 🌐 |
| 6 | **Learn 2D Game Art** (YouTube) | 搜索 "pixel art tutorial for beginners" / "game UI design Figma" | 🎥 |

### 14.4 关卡设计

| # | 资源 | 说明 | 类型 |
|---|------|------|------|
| 1 | **Game Maker's Toolkit: Boss Keys** | Mark Brown 的关卡设计分析系列 (塞尔达/银河城) | 🎥 |
| 2 | **An Architectural Approach to Level Design** | Christopher Totten — 用建筑学原理讲关卡设计 | 📖 书籍 |
| 3 | **LDtk** | ldtk.io — 免费开源的 2D 关卡编辑器，独立于引擎 | 🔧 工具 |

### 14.5 游戏开发全流程

| # | 资源 | 说明 |
|---|------|------|
| 1 | **GameDev.tv 课程 (Udemy)** | Unity/Godot/Unreal 完整课程，经常 $15 打折。推荐 Ben Tristem 的 Unity 系列 |
| 2 | **Brackeys (YouTube)** | 已停更但存档经典——Unity/C# 入门最佳免费资源 |
| 3 | **HeartBeast (YouTube)** | Godot 教程，适合 2D 动作游戏 |
| 4 | **《游戏设计艺术》中文版** | Schell 的书有中文译本 |
| 5 | **indienova.com** | 中文独立游戏社区，大量开发日志和案例分析 |

---

## 15. 算力需求评估

### 15.1 你的两台设备

| 设备 | CPU | GPU | RAM | 定位 |
|------|-----|-----|-----|------|
| 当前运行环境 (云端) | — | — | — | Hermes Agent 运行。无需算力，纯 API 调用 |
| 家用主机 | Intel 12代 | **RTX 4070 Ti** (12GB VRAM) | ? | 游戏开发工作站 + 本地 AI |

### 15.2 各开发环节的算力需求

| 环节 | 最低配置 | 推荐配置 | 你的 4070 Ti |
|------|---------|---------|-------------|
| **写代码 / 脚本** | 任何能开 VS Code 的电脑 | 16GB RAM | ✅ 绰绰有余 |
| **Godot 引擎开发** | 集成显卡 | 4GB VRAM | ✅ 绰绰有余 |
| **Unity 引擎开发** | GTX 1050 | RTX 2060+ | ✅ 绰绰有余 |
| **Unreal 5 开发** | RTX 2060, 32GB RAM | RTX 3070+, 64GB RAM | ⚠️ 可行但 12GB VRAM 稍吃紧 |
| **Blender 建模/渲染** | GTX 1060 | RTX 3060+ | ✅ 良好 |
| **Aseprite 像素画** | 任何电脑 | 任何电脑 | ✅ 无需 GPU |
| **本地 AI 推理 (SD/Meshy)** | RTX 3060 12GB | RTX 4080+ 16GB | ⚠️ 12GB 够用但偏紧 |
| **本地 LLM (如 Qwen 7B)** | RTX 3060 12GB | RTX 4090 24GB | ✅ 7B 模型没问题 |
| **ComfyUI / Stable Diffusion** | RTX 3060 12GB | RTX 4080+ 16GB | ✅ 12GB 够跑 SDXL |
| **Suno / Udio (云端)** | 任何电脑 | 任何电脑 | ✅ 纯云端 |

### 15.3 算力总结

**你的 4070 Ti (12GB VRAM) 是非常好的独立游戏开发卡。**关键限制：

- ✅ 所有 2D 开发：完全无压力
- ✅ Godot/Unity 中小型 3D 项目：无压力
- ✅ 本地跑 7B-13B 参数 LLM：可以
- ✅ 本地 Stable Diffusion 生成概念图/纹理：可以
- ⚠️ Unreal 5 大型项目：12GB VRAM 偏紧，但独立游戏规模通常不需要
- ❌ 本地训练大模型：12GB 不够，但独立开发者不需要自己训练

**建议**: 如果还没配 RAM，上到 **32GB** (DDR5 优先)。如果预算允许，64GB 更从容（Blender + Unity + VS Code + Chrome 同时开）。

---

## 16. 本地可运行的工具与平台

### 16.1 Nvidia 生态 (利用你的 4070 Ti)

| 工具 | 做什么 | 状态 |
|------|--------|------|
| **Nvidia G-Assist** | AI 系统助手 (Qwen 4B 本地运行) — 自动调优性能/散热/灯光。非游戏开发工具，但提升工作站体验 | CES 2026 发布 |
| **RTX Remix** | AI 重制经典游戏的工具套件——纹理升级、光线追踪注入。对你做新游戏用处不大 | 已发布 |
| **Nvidia ACE** | 游戏内 AI NPC——对话/语音/面部动画。云端+本地混合。面向运行时，非开发工具 | 已发布 |
| **ComfyUI + RTX 加速** | 本地 Stable Diffusion 工作流。Nvidia 优化版在 RTX 上快 3x。做概念图/纹理有用 | ✅ 可装 |
| **Nvidia Canvas** | 涂鸦转照片级风景图。灵感参考用 | 免费 |
| **Nvidia Omniverse** | 3D 协作和模拟平台。对独立开发者偏重 | 免费 |

### 16.2 本地可跑的 AI 模型 (利用 12GB VRAM)

| 模型 | 用途 | VRAM 需求 |
|------|------|----------|
| **Stable Diffusion XL (SDXL)** | 概念图、纹理生成 | ~8GB |
| **Qwen 2.5 7B (GGUF)** | 本地编码助手 | ~6GB |
| **DeepSeek Coder 6.7B (GGUF)** | 本地代码生成 | ~6GB |
| **Stable Audio Open** | 本地音效生成 | ~4GB |
| **TRELLIS 2** | 开源 3D 模型生成 (Microsoft) | ~10GB |

### 16.3 你不需要本地跑的东西

- **Hermes Agent**: 已经是云端 LLM，不需要本地 GPU
- **Suno / Udio**: 云端服务，质量最好
- **Meshy / Tripo**: 云端 3D 生成，效果比开源模型好
- **GitHub / CI**: 云端

### 16.4 实用本地工具速查

```
你的 4070 Ti 主机建议装:
├── Blender (3D 建模/动画)              免费
├── Aseprite (像素画，$20)              付费
├── Figma (UI 设计)                     免费
├── Godot 4.x (游戏引擎)                免费
├── VS Code / Cursor (代码编辑器)        免费/付费
├── ComfyUI (SD 本地推理)               免费
├── OBS Studio (录屏/DevLog)            免费
├── Audacity (音频编辑)                  免费
└── Git + GitHub Desktop                免费
```

---

## 17. 引擎内置 AI 工具进展 (Unity / Unreal)

### 17.1 Unity AI (2026 年 5 月 Open Beta)

Unity 在 2026 年 5 月发布了全新的 **Unity AI** (废弃了旧版 Muse)，这是目前引擎内置 AI 最成熟的产品：

| 功能 | 做什么 | 成熟度 |
|------|--------|--------|
| **AI Assistant** | 内嵌在 Unity Editor 里的 Agent——理解场景、写 C# 脚本、创建 GameObject | ⭐⭐⭐⭐ |
| **Sprite Generator** | 文字/参考图 → 2D Sprite，支持 spritesheet 动画帧 | ⭐⭐⭐⭐ |
| **Texture Generator** | 文字 → PBR 纹理 (漫反射/法线/粗糙度) | ⭐⭐⭐⭐ |
| **UI Generator** | 文字描述 → UI 布局 + sprite，直接放到 Canvas | ⭐⭐⭐ |
| **AI Gateway** | 接入外部模型 (Gemini 等) 的中间层 | ⭐⭐⭐ |
| **MCP Server** | 让外部 Agent (如 Claude Code) 直接操作 Unity 场景 | ⭐⭐⭐ |

**评估**: Unity AI 是目前**唯一能在编辑器中直接生成 Sprite/纹理/UI 并即时使用的引擎 AI**。对 2D 游戏开发非常实用。但它生成的是**占位/原型级质量**，不能替代最终美术。

> 关键限制: 需要 Unity 6、需要联网 (通过 Unity Cloud)、默认使用外部模型 (Gemini 而非自研)、有 Token 成本。

### 17.2 Unreal Engine AI

| 功能 | 做什么 | 成熟度 |
|------|--------|--------|
| **NVIDIA RTX Branch (实验性)** | UE5 的 RTX 神经渲染分支——RTX Mega Geometry、RTX Hair、Neural Shaders | ⭐⭐⭐ |
| **Nvidia ACE 插件** | AI NPC——语音识别+对话生成+面部动画 (Audio2Face) | ⭐⭐⭐ |
| **PCG (程序化内容生成)** | 内置工具——不是 AI 但很重要。规则驱动的关卡/场景生成 | ⭐⭐⭐⭐⭐ |
| **MetaHuman** | 照片级角色生成器 (非 AI，是参数化) | ⭐⭐⭐⭐⭐ |

**评估**: Unreal 目前**没有类似 Unity AI 的内置生成式 AI Agent**。它的重点在渲染端 (RTX Neural Shaders) 和 AI NPC (ACE)，而非辅助开发流程。UE 的 PCG 工具很强，但那是规则驱动而非 AI 驱动。

### 17.3 实话：引擎 AI 对你的影响

| 引擎 | 对你的价值 |
|------|-----------|
| **Unity AI** | 如果你选 Unity 方案，Sprite Generator + Texture Generator 可以直接加速 2D 原型阶段。但**最终美术仍需人工** |
| **Unreal** | 如果你不做 3A 级别的 3D 游戏，UE 的 AI 功能基本用不上。MetaHuman + PCG 是好工具但不是 AI |
| **Godot** | 没有官方 AI 集成。但有社区插件 (Summer Engine 等) 在做 AI Agent。Godot 的纯文本场景文件格式对 Hermes Agent 非常友好 |

> **结论**: 引擎 AI 是加分项不是决定项。选引擎首先看游戏类型和你的技术栈，AI 功能是锦上添花。

---

## 18. 你关心的游戏：制作团队与成本案例分析

### 18.1 2D 像素/叙事游戏

| 游戏 | 团队 | 开发时间 | 引擎/工具 | 预算 | 销量 | 关键启示 |
|------|------|---------|----------|------|------|---------|
| **Terraria** | Re-Logic — 起始 1 人 (Andrew Spinks)，后扩展到~12 人 | 2011 年首发，持续更新至今 | **XNA (C#)** — 非标准引擎，自己写的框架 | 极低 (个人项目起步) | **7000 万+** | 用你自己最顺手的工具。1 人也能起步。核心 loop 好 > 画面好 |
| **Stardew Valley** | **1 人** — Eric Barone (ConcernedApe) | **4.5 年**，每天 10-12h | **XNA/MonoGame + C#** | ~$0 (女友养家+兼职) | **3000 万+**，收入 $130M+ | 单人可以做一切——代码+美术+音乐+设计。但代价是 4.5 年没有收入 |
| **To the Moon** | Freebird Games — **核心 1 人** (Kan Gao)，少数外包 | ~1.5 年 | **RPG Maker XP** | 极低 | 数百万 | 工具不是重点——RPG Maker 都能做出神作。叙事和情感是第一生产力 |
| **Dave the Diver** | Mintrocket — 起始 **5 人**，顶峰 **26 人** | ~3 年 | **Unity** (2D+3D 混合) | "接近独立游戏规模" (非纯 indie，背后有 Nexon) | 400 万+ | 真正的好游戏是"独立精神+中等预算"。小团队但高质量 |

### 18.2 3D 游戏

| 游戏 | 团队 | 开发时间 | 引擎 | 预算 | 销量 | 关键启示 |
|------|------|---------|------|------|------|---------|
| **Dyson Sphere Program** | Youthcat Studio — **5 人** (中国) | ~2 年 (EA 发布) | **Unity** | 极低 | 数百万 (EA) | 5 个中国开发者可以做太空工厂游戏。算法能力 > 美术能力 |
| **Frostpunk** | 11 bit studios — **~50 人** | ~3-4 年 | 自研引擎 (Liquid Engine) | 中等 (~PLN 千万级) | 500 万+ | 中等团队，但这是有成熟管线的工作室。不是独立开发者能达到的规模 |
| **Human Fall Flat** | No Brakes Games — **1 人** (Tomas Sakalauskas) | ~2 年 | **Unity** | $0 (个人最后一搏) | **5500 万+** | 单人 3D 游戏的巅峰案例。物理+沙盒+搞笑=病毒传播。美术简单但玩法出色 |

### 18.3 模式总结

```
你想要的游戏，可以分为三种开发模式：

模式 A: Stardew Valley 模式 (1 人，4+ 年)
  特点: 一个人做所有事
  前提: 有 3-5 年生活费储蓄，或有人养
  风险: 极高，99% 无法完成
  收益: 如果成了——改变人生
  Hermes 加速: ⭐⭐⭐⭐⭐ (Agent 帮你写 90% 代码，省 2-3 年)

模式 B: Dave the Diver / Dyson Sphere 模式 (5-10 人，2-3 年)
  特点: 小团队，但每个人都有专长
  前提: 有预算雇佣或找到合伙人
  风险: 中——团队协作本身就是挑战
  Hermes 加速: ⭐⭐⭐⭐ (Agent 替代 2-3 名初级程序员)

模式 C: Terraria / Human Fall Flat 模式 (1 人起步→扩展)
  特点: 先发 MVP → 根据市场反馈迭代 → 慢慢加人
  前提: 核心 loop 够好，能 EA 就有人买
  风险: 中——EA 给了现金流，但也给了压力
  Hermes 加速: ⭐⭐⭐⭐⭐ (快速出 MVP，快速迭代)
```

**我的建议**: 你最适合 **模式 C**——用 Hermes 快速出最小可玩版本，验证核心乐趣，然后决定是 solo 继续做还是找帮手。

### 18.4 他们的技术栈对比

| 游戏 | 引擎 | 为什么选它 |
|------|------|-----------|
| Terraria | XNA (C#) | 开发者之前就用 XNA 做 Mario 同人 |
| Stardew Valley | XNA/MonoGame | 和 Terraria 一样——开发者熟悉 C# |
| To the Moon | RPG Maker XP | 最低门槛，专注叙事 |
| Dave the Diver | Unity | 2D+3D 混合能力 + 多平台支持 |
| Dyson Sphere | Unity | 中国开发者最熟悉的商业引擎 |
| Human Fall Flat | Unity | 物理引擎成熟 + 多平台 |
| Frostpunk | 自研引擎 | 11 bit 有技术积累 |

**规律**: 用什么引擎取决于你自己最熟悉什么，而不是什么引擎最好。Terraria 和 Stardew Valley 两个史上最成功的独立游戏，用的都不是主流引擎。

---

## 19. 深度案例：Dave the Diver 开发全剖析

> 数据来源: Game Developer 专访 (2023.8), Software Engineering Daily 技术访谈 (2024.4), Unity 官方案例研究 (2023.12), Automaton 专访 (2026.6), Naavik 深度分析

### 19.1 基本信息

| 项目 | 数据 |
|------|------|
| 游戏 | Dave the Diver (潜水员戴夫) |
| 开发商 | Mintrocket (原 Nexon Korea 内部子品牌，现已独立为子公司) |
| 发行商 | Nexon |
| 引擎 | **Unity** (Universal Render Pipeline / URP) |
| 开发周期 | 约 3 年 (2020 原型 → 2022.10 EA → 2023.6 正式发布) |
| 首发销量 | **100 万份 / 10 天** |
| 累计销量 | **800 万+** (截至 2026.6) |
| Steam 评价 | **97% 好评** (峰值在线 98,480 人) |
| 平台 | PC → Switch → PS4/5 → Xbox → Switch 2 |
| 定价 | $19.99 (DLC $9.99, ~10h 新内容) |

### 19.2 团队规模与结构

| 阶段 | 人数 | 说明 |
|------|------|------|
| 原型期 | **5 人** | 核心概念验证 |
| 开发期 | 逐步扩展 | 保持小团队 |
| 顶峰期 | **~27 人** (另说 26 人) | 正式发布前后 |
| 当前 (2024+) | 更大规模，同时开发 3 个项目 | 公司独立后 |

**团队组织方式** — "Area-Focused" 分区负责制：

```
Jaeho Hwang (Game Director + CEO)
│
├── 海洋区 (Marine Section)
│   ├── 专属程序员——负责水下探索、捕鱼、战斗
│   └── 美术——3D 海底场景 + 鱼类建模
│
├── 寿司吧区 (Sushi Bar Section)
│   ├── 专属程序员——餐厅管理、上菜、倒酒
│   └── 美术——UI、顾客、菜品展示
│
├── 农场区 (Farm Section)
│   ├── 专属程序员——种植系统
│   └── 美术——植物、鱼塘
│
├── 剧情/任务组
│   └── Hwang 亲自写所有剧本
│
└── 通用职能 (音效、QA、跨平台移植)
```

> Hwang 原话: "好处是，你要问某个内容找谁，非常清楚。每个区域的内容可以无缝集成，责任明确。"

### 19.3 开发成本

Dave the Diver **没有一个公开的精确预算数字**，但可以从多方信息拼出范围：

| 维度 | 估算 |
|------|------|
| 团队规模 | 平均 ~15 人 × 3 年 (逐步从 5 扩展到 27) |
| 人均年成本 (韩国) | $60K-$100K (含薪资+福利+办公) |
| **总人力成本估算** | **$2.5M - $4.5M** |
| 引擎授权 | Unity 免费层 (年收入 < $200K 时) → 升级到 Pro |
| 服务器/基础设施 | 极低 (近乎纯单机，早期甚至砍掉了社交功能) |
| 市场推广 | Nexon 内部资源 (非独立开发者的外部采购) |
| **总开发成本估算** | **$3M - $5M** |

**关键背景**: Mintrocket 是 Nexon 内部团队，**不需要为资金发愁**。但 Hwang 强调，他们刻意保持 "独立游戏的预算规模"——Nexon 给了他们自由度和安全感，但没有给 AAA 级的无限预算。

> Hwang: "我们从来没觉得自己是独立团队。我们在大公司内部，有资金支持，开发环境比真正的独立开发者好得多。但我们做的确实是小规模、高质量的游戏。"
>
> 他把这类游戏称为 **"Single-A" 或 "Triple-I (iii)"** ——介于 AAA 和 Indie 之间的新品种：有大公司的稳定资金，但保持独立游戏的创意自由和小团队敏捷性。

### 19.4 收入与回报

| 指标 | 数据 |
|------|------|
| 首发 10 天 | 100 万份 × $19.99 ≈ **$20M 流水** |
| 截至 2026.6 | 800 万份 × $19.99 ≈ **$160M 流水** (不含折扣和 DLC) |
| DLC (2026.6) | $9.99，内容量 ~10h |
| 投入产出比 | ~$3-5M 投入 → ~$160M+ 流水 = **30-50x ROI** |

> 这是 Nexon 历史上 "最成功的 PC/主机游戏之一"。游戏成功后，Nexon 将 Mintrocket 从内部子品牌**升级为独立子公司**，Hwang 出任 CEO。目前 Mintrocket 同时开发 3 个项目。

### 19.5 技术细节

#### 图形管线：2D 像素 + 3D 低多边形混合

```
渲染栈：
┌─────────────────────────────┐
│ Unity URP (通用渲染管线)      │  ← 跨平台统一画质
├─────────────────────────────┤
│ 前景层: 2D Sprite (像素人物) │  ← Dave、NPC、鱼（小型）
│ 中景层: 3D Mesh (低多边形)   │  ← 大型海洋生物、礁石
│ 背景层: 3D 场景              │  ← 海底地形、光影
├─────────────────────────────┤
│ 工具链:                      │
│  Cinemachine → 摄像机控制    │  ← "像拍电影一样设置机位"
│  Shader Graph → 可视化着色器  │  ← 美术师可以直接调，不需要程序员
│  Input System → 跨平台输入    │  ← PC/Switch 共用同一套代码
│  Unity Profiler → 性能分析    │  ← 找瓶颈
└─────────────────────────────┘
```

**为什么选 2D+3D 混合？**

1. 团队擅长 2D 像素 (前作 Evil Factory 是纯 2D 像素 Boss 战游戏)
2. 纯 2D 海底 "缺乏纵深和空间真实感" (Hwang 原话)
3. 3D 背景 "不贵也不难"——比预期容易
4. **3D 鲨鱼 vs 2D Dave 是最大的技术挑战**——经过 "多次研发" 才让它们看起来自然

#### 性能与跨平台

```
策略: URP + 平台专属 Render Pipeline Asset
  ├── PC: 高质量预设
  ├── Switch: 降低画质但保持 60fps
  └── 同一套代码 + 同一套资产 → 零转换成本

工具:
  - Unity Profiler → 性能热点定位
  - Unity 工程师直接对接 → 帮查引擎内部代码
```

#### 音效

访谈中未详细展开音效管线，但游戏使用了大量定制音效和授权音乐。从工作量推测：至少 1-2 名音效设计师 + 外包作曲。

### 19.6 设计哲学

#### 核心理念："Fun First" (Nexon 内部品牌口号)

```
设计原则 (从 Hwang 的多篇访谈中提炼):

1. 核心 loop 先做好
   └→ 先搞定 "捕鱼+拉杆的手感"，确定这个好玩才往下做

2. 日夜分离 = 专注体验
   └→ 白天潜水捕鱼 (探索/动作)，晚上开寿司店 (经营/策略)
   └→ 原本寿司吧是全自动的，后来改为手动——"让玩家有参与感"

3. 叙事绑定系统
   └→ 不直接扔给玩家一堆功能，而是通过角色和剧情自然引入
   └→ VIP 客人来访 → 需要特定食材 → 引导玩家探索新区域

4. 从小开始，像灾难片一样展开
   └→ 一开始只是 "捕鱼+开餐厅"
   └→ 慢慢加入农场、Boss 战、深海考古、赛海马、音游...
   └→ "像灾难电影从平凡日常开始，逐渐升级"

5. 每天的变化带来新鲜感
   └→ 海底地图随机变化 → 团队不够大做不了开放世界 → "小而多变"
   └→ 天气、洋流、鱼群位置都随机

6. "Dogfooding" = 导演自己每天玩
   └→ Hwang 自己玩开发版本，从不同玩家视角做笔记
   └→ "今天我是经营模拟玩家，明天我是 Subnautica 粉丝"

7. 一切围绕 Dave
   └→ 不同的小游戏/机制看起来随机，但因为都是 Dave 做的
   └→ "就像我们现实生活中一天里开车、做饭、修厕所"
```

#### 被砍掉的功能

**社交照片墙**: 原本计划让玩家的照片获得 "其他真实玩家的点赞"，但 "因为服务器复杂性和管理开销" 放弃。目前点赞只影响本地。Hwang 说 "这仍然是我们团队的遗憾"。

### 19.7 开发流程与节奏

```
时间线：
2020      原型期 (5人)
          └→ 核心捕鱼机制 + 寿司吧自动化原型
          └→ 发现 "不能专注在任一任务上" → 日夜分离

2021      全力开发
          └→ 加入 3D 背景和大型海洋生物
          └→ 加入农场、Boss 战等扩展系统
          └→ 团队逐步扩充到 ~20 人

2022.10   Early Access 上线
          └→ 社区反馈驱动调整
          └→ "EA 帮助我们修正了很多设计错误"

2023.6    正式发布
          └→ 1.0 版本
          └→ 10 天破百万

2023-24   持续更新 + Switch 移植
          └→ 与 Unity 工程师合作优化移动端

2024.9    Mintrocket 独立为子公司

2026.6    DLC "In the Jungle" 发布
          └→ 淡水湖泊新生态 + 新餐厅 + ~10h 内容
```

### 19.8 对 Hermes 独立游戏开发的关键启示

| 启示 | 具体 |
|------|------|
| **小团队可做大游戏** | 27 人能做到 800 万销量。Hermes+Agent 可以把等效团队降到 1-3 人 |
| **核心 loop 第一** | 先用手工原型验证 "捕鱼是否好玩"，再加系统。Hermes 适合快速出原型 |
| **日夜循环是天才设计** | 把两个不同类型的游戏 (动作+经营) 用时间线分离 → 各自专注。Agent 开发中可以借鉴：分模块分阶段，互不干扰 |
| **叙事是最好的教程** | 不写 Tutorial，用角色和剧情自然引入新系统。Agent 可以帮助生成叙事脚本 |
| **区域分工可复制** | "海洋程序员/寿司吧程序员/农场程序员" —— 对应 Hermes 的 delegate_task：把不同区域分配给不同 Agent |
| **砍功能是常态** | 社交功能因为 "太复杂" 砍了。独立开发者更需要这种判断——完不成的功能就别做 |
| **2D+3D 混合是降本增效** | 不需要全 3A 画质，也不需要全手工像素。混合管线降低了美术成本 |
| **"Single-A" 模式可复制** | 有安全网 (资金) + 无上限压力 (不追求 AAA) + 创意自由。Hermes+Agency 模式天然接近这个状态 |

### 19.9 如果用 Hermes 做类似的游戏，需要什么？

```
团队配置 (Hermes 加速版):
  1 人 = 你 (游戏设计 + 方向把控 + 美术审核)
  + Hermes Agent (代码主力)
  + delegate_task (多 Agent 并行开发不同区域)
  + pixel-art skill (2D 像素角色)
  + Suno/heartmula (音乐)
  + Aseprite (你手动精修关键帧)
  + Blender (你手动做简单 3D 模型)
  + Unity 6 (引擎) 或 Pure Web Stack

等效传统团队: 5-8 人

需要的技能 (你):
  - Unity 基础 (2-3 周学习)
  - 像素画基础 (3-5 天 Aseprite)
  - 3D 基础 (1-2 周 Blender 简单建模)
  - 游戏设计感 (持续学习)
  - 项目管理 (已有)

总开发时间估算: 1.5-2.5 年 (vs 传统 3 年 27 人)
```

---

## 20. 深度案例：Stardew Valley 开发全剖析

> 数据来源: GQ 长篇专访 (2018), Game Developer AMA 报道 (2017), Wikipedia, 销量追踪网站, Eric Barone 多次 Reddit AMA

### 20.1 基本信息

| 项目 | 数据 |
|------|------|
| 游戏 | Stardew Valley (星露谷物语) |
| 开发者 | **1 人** — Eric Barone (ConcernedApe) |
| 最初发行商 | Chucklefish (2016-2018)，后来自主发行 |
| 引擎 | **XNA 4.0 → MonoGame** (C#) |
| 开发周期 | **4.5 年** (2011 末 → 2016.2.26) |
| 工作时长 | 每天 10-12 小时，每周 7 天 (自述) |
| 首发平台 | Windows (2016.2.26) |
| 累积平台 | Win/Mac/Linux → PS4/Xbox (2016.12) → Switch (2017.10) → iOS/Android (2018-19) → Switch 2 (2025.12) |
| 定价 | **$14.99** |
| 首发销量 | 42.5 万 / 2 周，100 万 / 2 个月 |
| 累计销量 | **5000 万+** (2026.2) |
| Steam 评价 | **98% 好评** (150 万+ 评测) |

### 20.2 一个人的战争

```
Eric Barone 背景:
├── 年龄: 开发时 24-28 岁 (生于 1987)
├── 学历: 华盛顿大学计算机科学 (未毕业)
├── 开发前职业: 电影院晚班引座员 (part-time)
├── 游戏开发经验: ZERO
│   └── 只做过几个未完成的小项目 + 一个给乐队做的搞笑小游戏
├── 动机: "一直想玩一个更好的 Harvest Moon"
│   └── 等了很久没人做 → "Why not me?"
└── 生活状态:
    ├── 和女友 Amber 同居
    ├── Amber 打两份工养家
    ├── 几乎不出门: "只去买菜、散步、送 Amber 上学"
    └── 地下室里 3 天一次独自锻炼
```

**他做了一切**：

```
一个人负责:
├── 编程: 全部游戏逻辑、引擎集成、存档系统、多平台移植
├── 美术: 所有像素画——角色、地形、建筑、物品、UI、动画帧
│   └── "我从不会像素画开始，几百个小时的练习后才慢慢变好"
├── 音乐: 全部原声音乐——3 小时+ 原创配乐
│   └── 后来办了两次交响乐巡演 (全球售罄)
├── 音效: 所有环境音、交互音
├── 剧本: 所有 NPC 对话 (~30 个角色，每人有完整故事线)
├── 设计: 全部玩法系统——种田、采矿、战斗、钓鱼、社交、收集...
├── 测试: 独自 QA
└── 社区运营: 早期亲自回复每封邮件
```

> Barone 原话: "I wanted to do all the music, the art. I think it makes sense that I worked entirely alone."
>
> "I told myself 'Why not me?' I adopted a mindset of tenacity and optimism. And when it came to pixel art or other things that I had little experience in, I just dove in and did my best."

### 20.3 开发成本

| 维度 | 数据 |
|------|------|
| 人力成本 | $0 (自己) |
| 生活成本 | 女友 Amber 打两份工支撑 4.5 年 |
| 软件/工具 | 几乎 $0 (免费工具链) |
| 外包 | $0 (什么都没外包) |
| 发行支持 | Chucklefish 提供发行+Switch 移植协助 (不收费) |
| **总现金支出** | **~$0 - 数千美元** |
| **机会成本** | 4.5 年青春 + 女友的两份工 |

### 20.4 收入与回报

| 指标 | 数据 |
|------|------|
| 首发 2 周 | 42.5 万份 × $14.99 ≈ **$6.4M** (Steam 70% 分成后 ~$4.5M) |
| 2017 年 | ~350 万份 |
| 2020 年 | 1000 万份 |
| 2022 年 | 2000 万份 |
| 2024 年底 | 4100 万份 (PC 2600 万 + Switch 790 万 + 其他) |
| 2026 年 | **5000 万份+** |
| 平台分成后收入估算 | $14.99 × 5000 万 × ~65% = **~$5 亿** |
| 周边收入 | 官方食谱书、交响乐巡演、周边商品 |
| 投入产出比 | ~$0 现金 → ~$5 亿 = **天文级** |

> Forbes "30 Under 30: Games" 2017 年入选。《Stardew Valley》被普遍认为是**史上销量最高的完全 solo 开发游戏**。

### 20.5 技术细节

#### 引擎选择：XNA → MonoGame

```
技术栈:
├── 框架: XNA 4.0 (微软游戏开发框架)
│   └── 2011 年选的技术，2013 年微软停止维护 XNA
│   └── 后来迁移到 MonoGame (开源的 XNA 兼容实现)
│   └── 1.5.5 更新 (2021): 32位 XNA → 64位 MonoGame
├── 语言: C#
├── 地图格式: xTile (tIDE tilemap editor) → Tiled 兼容
│   └── mod 社区基于此蓬勃发展
├── 渲染: SpriteBatch (XNA 2D 渲染)
├── 音频: XNA SoundEffect + Song API
├── 存档: XML 格式 (可编辑)
└── 跨平台: MonoGame 原生支持 → Chucklefish 协助 Switch 移植
```

**为什么选 XNA？**

- 2011 年 Barone 只会 C#，"XNA 是当时最简单的 C# 游戏框架"
- 没有引擎 UI —— **纯代码驱动**。这和 Hermes Agent 的工作方式完美对应
- Terraria 也用 XNA —— 有先例证明可行
- 他不需要 3D、不需要物理引擎、不需要可视化编辑器 —— XNA 就够了

#### 内容规模 (单人制作！)

```
1.0 版本 (2016) 包含:
├── ~30 个 NPC，每人有完整对话树和好感度系统
├── 5 种技能: 耕种/采矿/采集/钓鱼/战斗
├── 40+ 种作物，多种果树
├── 60+ 种鱼类
├── 80+ 种食谱
├── 100+ 种矿物和文物
├── 多个节日活动 (每个有独特玩法)
├── 40+ 小时主线内容
├── 100+ 小时完成度内容
├── 3+ 小时原创配乐 (70+ 首曲目)
├── 数万行 C# 代码
└── 全部像素画 tilemap —— 几十个地图区域
```

> "他不是一个天才程序员——他是靠 4.5 年每天 10 小时的堆积做出来的。" — Reddit 社区共识

### 20.6 开发过程

```
2011 年末   开始。目标是 "改善 C# 编程技能，充实简历"
           └→ 第一个原型: 可导航的农场区域
           └→ 第一个系统: 耕种机制 (研究真实农业周期)

2012       "每天就是: 起床 → 写代码/画像素/做音乐 → 睡觉"
           └→ 像素画从零学起，每天进步一点点
           └→ 音乐用 Propellerhead Reason 制作

2013       开始有 "完整游戏" 的雏形
           └→ 在 Chucklefish 论坛上分享 → Starbound 社区开始关注
           └→ Chucklefish 主动联系 → 签约发行 (不插手开发)

2014       Steam Greenlight 上线 → 社区投票通过
           └→ "Greenlight 期间的社区支持是我最大的动力"

2015       全力冲刺
           └→ Barone 说这是 "最难的时期"——看不到尽头

2016.2.26  正式发布
           └→ "我以为最多卖 10 万份就很成功了"
           └→ 2 周卖了 42.5 万 → "完全超出预期"

2016-2018  一个人持续更新 (1.1 → 1.3)
           └→ 加入多人模式 (这是最大的技术挑战)

2019       首次招募帮手 (1.4 更新)
           └→ Arthur Lee (Mr. Podunkian) + Alex Erlandson

2020-2021  小团队更新 (1.5)
           └→ 加入姜岛 (Ginger Island) ——相当于半个新游戏

2024       1.6 大更新 (5 人团队)
           └→ 新节日、新物品、大量 mod 支持改进

2021.10    宣布 Haunted Chocolatier (下一款游戏)
           └→ 再次 solo 开发中
```

### 20.7 设计哲学

```
从访谈和 AMA 中提炼的核心原则:

1. "做我自己想玩的游戏"
   └→ 目标不是市场，是自己
   └→ "I'm just making the game I want to make, in the style that I want"

2. 内容海战术
   └→ 不是深度模拟，而是 "足够多的东西让你总有新发现"
   └→ 60 种鱼、80 种食谱、30 个 NPC 各有故事

3. 节奏控制
   └→ 一天 = 约 13 分钟 (实时)
   └→ "刚好够你做几件事，但又让你想 '明天再玩一天'"

4. 黑暗与现实感
   └→ NPC 不只是 "理想化的符号"——有人酗酒、有人抑郁、有人无家可归
   └→ Joja 公司批判资本主义 → 和 Grandpa 的信形成对照

5. 不做 PR Bullshit
   └→ 不搞预告片轰炸、不请网红、不做 hype cycle
   └→ "游戏自己说话"

6. Mod 友好
   └→ 开放地图格式 (xTile/Tiled)
   └→ SMAPI (社区做的 mod loader)
   └→ 1.6 更新专门改进了 mod 支持
   └→ 社区 content mod 可能有数千个

7. 永不言弃
   └→ "I would've felt ashamed if I had gotten so far and then quit."
   └→ "I set a huge goal and then adopting a mindset of never giving up"
```

### 20.8 发布后的演变

```
2016: Barone solo → 1.0 发布
2019: +2 人 → 1.4 更新
2021: +3 人 → 1.5 (姜岛)
2024: +5 人 → 1.6 大更新
2026: 5000 万份 → "我可能会做一辈子"

现在的团队 (~5 人):
  Eric Barone = 项目灵魂 + 设计方向
  Arthur Lee = 主程序员
  Jesse Plamondon-Willard (Pathoschild) = mod 支持
  其他人 = 移植 + 辅助
```

> Barone: "Stardew Valley 已经离开了我的怀抱。它现在有自己的生命——来自所有玩过它的人的解读。就像孩子长大后离开家一样。"

---

## 21. Dave the Diver vs Stardew Valley：终极对比

### 21.1 核心数据对照

| 维度 | Dave the Diver | Stardew Valley |
|------|---------------|----------------|
| **团队** | 5→27 人 | **1 人** |
| **时间** | ~3 年 | **4.5 年** |
| **预算** | ~$3-5M (Nexon) | **~$0 (女友养)** |
| **引擎** | Unity (URP) | XNA/MonoGame (C#) |
| **技术复杂度** | 2D+3D 混合管线 | 纯 2D tilemap |
| **首月销量** | 100 万 | 100 万 |
| **累计销量** | 800 万+ | **5000 万+** |
| **定价** | $19.99 | $14.99 |
| **累计收入** | ~$160M | **~$500M** |
| **ROI** | 30-50x | **天文级 (∞)** |
| **更新方式** | DLC 付费 ($9.99) | **全部免费更新** |
| **开发模式** | 公司内部小团队 | 个人 solo → 小团队 |
| **第二款游戏** | Bancho the Chef, 等 | Haunted Chocolatier |
| **美术风格** | 2D 像素 + 3D 低多边形 | **纯 2D 像素** |
| **游戏类型** | 动作冒险 + 经营模拟 | 农场模拟 RPG |

### 21.2 根本性差异

```
Dave the Diver:
  "有安全网的创意冒险"
   └→ Nexon 出钱，团队做创意
   └→ 失败 = 团队解散，但个人没破产风险
   └→ 成功 = 品牌升级为独立子公司
   └→ 本质: 大公司内部的 "假独立" (Single-A)

Stardew Valley:
  "孤注一掷的人生豪赌"
   └→ 自己出生活成本 (女友出两份工)
   └→ 失败 = 4.5 年青春 + 零职业经验 + 女友的付出打水漂
   └→ 成功 = 从电影院引座员变成千万富翁
   └→ 本质: 真正的独立，真正的 all-in
```

### 21.3 两者共同的成功基因

| 共同点 | Dave the Diver | Stardew Valley |
|--------|---------------|----------------|
| **"做自己想玩的"** | Hwang 想做捕鱼+寿司的游戏 | Barone 想做更好的 Harvest Moon |
| **核心 loop 打磨到极致** | 捕鱼手感先做好再扩展 | 种田 loop 贯穿始终 |
| **内容海战术** | 鱼种、菜品、minigame、剧情分支 | 作物、鱼、矿物、NPC、节日 |
| **节奏感精确** | 日夜分离——白天动作/晚上经营 | 一天 13 分钟——"再玩一天" |
| **意外的深度** | 一开始以为只是捕鱼+开餐厅，后来有 Boss 战/深海考古/赛海马 | 一开始以为只是种田，后来有采矿/战斗/社交/神秘剧情 |
| **不做 PR bullshit** | Hwang 自己玩测试版做笔记 | Barone 亲自回复每封邮件 |
| **社区驱动迭代** | EA 期间社区反馈修正设计错误 | Reddit 社区从 Greenlight 时期就是核心力量 |
| **一个人掌舵** | Hwang 是唯一导演+编剧 | Barone 是唯一开发者 (1.0) |

### 21.4 对你的启示：两条路对照

```
如果走 Dave the Diver 路线:
  ├── 需要: 稳定资金来源 (储蓄/投资人/大公司)
  ├── 需要: 小团队 (即使 Hermes 替代一部分)
  ├── 风险: 中等 (钱烧完了就停)
  ├── 周期: 1.5-2.5 年
  ├── 技术: Unity/Godot (2D+3D 混合可行)
  └── 适合: 有一个已验证的概念 + 需要多人协作的复杂度

如果走 Stardew Valley 路线:
  ├── 需要: 3-5 年生活费储蓄 (或有人养)
  ├── 需要: 极大的自律和心理承受力
  ├── 风险: 极高 (99% 无法完成)
  ├── 周期: 3-5 年
  ├── 技术: 纯代码框架 (Pure Web / MonoGame 类)
  └── Hermes 加速: ⭐⭐⭐⭐⭐ (最需要 Agent 帮忙的场景)

Hermes Hybrid (你的路线):
  ├── 1 人 + Hermes Agent = 等效 3-8 人团队
  ├── 介于两者之间:
  │   ├── 像 SV: 你一个人做所有决策
  │   └── 像 DtD: Agent 帮你做执行 (像分区程序员)
  ├── 周期: 1-2 年 (Hermes 加速 2-3x)
  ├── 风险: 低于纯 solo (快 = 早验证 = 早发现不行就调整)
  └── 这是前 AI 时代不存在的第三种路线
```

### 21.5 最狠的 5 个真相

1. **Stardew Valley 的代码架构并不好** —— Barone 自己承认 "乱"。但不重要——玩家看不到代码。好游戏 ≠ 好代码。对 Agent 开发这是好消息：Agent 生成的代码不需要漂亮，只要能跑。

2. **两人都不是技术天才** —— Barone 不会 3D，Hwang 团队选 URP 是 "因为够用"。他们选自己**最熟悉的工具**，然后把内容做到极致。

3. **"内容海" 是最大壁垒** —— 不是引擎、不是美术、不是音乐。是 60 种鱼 + 80 种食谱 + 30 个 NPC × 完整故事。这就是 Agent 最能帮你的地方：批量生成内容。

4. **EA 是平衡的救星** —— Dave the Diver 的 EA 修正了 "很多设计错误"。Stardew 没有正式的 EA，但 Barone 持续看 Reddit 反馈。独立开发者的最佳 QA 是社区。

5. **最简单的工具做最大的事** —— RPG Maker (To the Moon) + XNA (Terraria, Stardew) + Unity URP (Dave the Diver)。都不是什么先进技术。工具不重要，**你的坚持 + 内容密度** 才是。

```

---

## 22. 技术实施：CI/CD 与 Dogfooding 每日构建

### 22.1 目标

每天自动构建 → 自动部署到你的设备 → 你醒来就能玩最新版本 → 发现问题直接在设备上截图/录屏 → Hermes 分析反馈修 bug。

### 22.2 整体架构

```
┌─────────────────────────────────────────────────────┐
│                  GitHub Repository                    │
│  ┌──────────┐  ┌──────────┐  ┌──────────────────┐   │
│  │  GDD.md  │  │  Assets  │  │  .github/        │   │
│  │  (设计文档)│  │  (游戏资产)│  │  workflows/     │   │
│  └──────────┘  └──────────┘  │  build.yml      │   │
│                               │  test.yml       │   │
│                               │  deploy.yml     │   │
│                               └──────────────────┘   │
└──────────────────────┬──────────────────────────────┘
                       │ git push (或 cron 触发)
                       ▼
┌─────────────────────────────────────────────────────┐
│              GitHub Actions Runner                    │
│                                                      │
│  ┌──────────────────────────────────────────────┐   │
│  │ 1. Checkout + LFS pull (拉代码+大文件)        │   │
│  │ 2. Unity License Activation (激活许可证)       │   │
│  │ 3. Cache Library/ (缓存加速)                  │   │
│  │ 4. Run Tests (EditMode + PlayMode)            │   │
│  │ 5. Build ─┬─ Windows x64 (.exe)              │   │
│  │           ├─ Android (.apk)                   │   │
│  │           └─ iOS (Xcode project → TestFlight) │   │
│  │ 6. Upload Artifacts (构建产物存档)             │   │
│  │ 7. Notify (Hermes cron → WeChat 通知你)        │   │
│  └──────────────────────────────────────────────┘   │
└──────────────────────┬──────────────────────────────┘
                       │
          ┌────────────┼────────────┐
          ▼            ▼            ▼
    Windows 端    Android 端     iOS 端
    直接下载      扫二维码       TestFlight
    安装测试      ADB 安装       自动推送
```

### 22.3 核心：GameCI + GitHub Actions Workflow

```yaml
# .github/workflows/nightly-build.yml
name: Nightly Multi-Platform Build

on:
  schedule:
    - cron: '0 6 * * *'        # 每天 UTC 6:00 (北京时间 14:00)
  push:
    branches: [main]            # main 分支推送也触发
  workflow_dispatch:            # 手动触发

jobs:
  # ── 测试 ──
  test:
    name: Run Tests
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with: { lfs: true }
      - uses: actions/cache@v3
        with:
          path: Library
          key: Library-${{ hashFiles('Assets/**', 'Packages/**', 'ProjectSettings/**') }}
      - uses: game-ci/unity-test-runner@v4
        env:
          UNITY_LICENSE: ${{ secrets.UNITY_LICENSE }}
        with:
          projectPath: .
          testMode: all         # EditMode + PlayMode

  # ── 构建 (多平台矩阵) ──
  build:
    name: Build ${{ matrix.targetPlatform }}
    needs: test                # 测试通过才构建
    runs-on: ubuntu-latest
    strategy:
      fail-fast: false
      matrix:
        targetPlatform:
          - StandaloneWindows64
          - Android
          - iOS
    steps:
      - uses: actions/checkout@v4
        with: { lfs: true }
      - uses: actions/cache@v3
        with:
          path: Library
          key: Library-${{ hashFiles('Assets/**', 'Packages/**', 'ProjectSettings/**') }}
      - uses: game-ci/unity-builder@v4
        env:
          UNITY_LICENSE: ${{ secrets.UNITY_LICENSE }}
        with:
          targetPlatform: ${{ matrix.targetPlatform }}
          buildName: MyGame
          versioning: Semantic   # 自动语义版本号
      - uses: actions/upload-artifact@v4
        with:
          name: Build-${{ matrix.targetPlatform }}
          path: build/${{ matrix.targetPlatform }}

  # ── 部署 Android 到设备 (ADB + webhook) ──
  deploy-android:
    name: Deploy to Android
    needs: build
    runs-on: ubuntu-latest
    steps:
      - uses: actions/download-artifact@v4
        with:
          name: Build-Android
      - name: Upload to web server (供设备下载)
        run: |
          # 上传 apk 到你的 Web 服务器
          # 或者用 Firebase App Distribution
          # 然后发 Webhook 通知 Hermes
          curl -X POST ${{ secrets.HERMES_WEBHOOK }} \
            -d '{"message":"Android build ready: https://your-server.com/builds/latest.apk"}'

  # ── 部署 iOS 到 TestFlight ──
  deploy-ios:
    name: Deploy to TestFlight
    needs: build
    runs-on: macos-latest         # iOS 构建需要 macOS runner
    steps:
      - uses: actions/download-artifact@v4
        with:
          name: Build-iOS
      - name: Upload to TestFlight
        uses: Apple-Actions/upload-testflight-build@v1
        with:
          app-path: build/iOS/MyGame.ipa
          app-store-connect-key: ${{ secrets.APPSTORE_CONNECT_KEY }}
```

### 22.4 关键配置

| 配置项 | 说明 |
|--------|------|
| `UNITY_LICENSE` | GitHub Secret — 存储 Unity 个人版许可证 (免费, 年收入<$200K) |
| `Library/` 缓存 | Unity 的 Library 文件夹 5-20GB。缓存后增量构建从 60 分钟降到 10-15 分钟 |
| `versioning: Semantic` | 自动生成 MyGame_v1.2.3.apk 格式版本号 |
| `fail-fast: false` | 一个平台失败不影响其他平台 |
| macOS runner (iOS) | iOS 构建必须用 GitHub macOS runner，每月免费额度有限 (2000 分钟) |
| Git LFS | 大型二进制文件 (纹理/模型/音频) 必须用 Git LFS，否则 repo 爆炸 |

### 22.5 Dogfooding 闭环

```
每日自动流水线:
  ┌─────────────────────────────────────────────┐
  │ 23:00  Hermes cron 收到一天的代码改动         │
  │        → Agent 写代码、修 bug、加功能          │
  │        → git commit + push                  │
  ├─────────────────────────────────────────────┤
  │ 06:00  GitHub Actions 自动触发               │
  │        → 测试 → 构建 Win/Android/iOS         │
  │        → 上传产物到 artifacts                │
  ├─────────────────────────────────────────────┤
  │ 07:00  Hermes cron 检测到新构建               │
  │        → 发送 WeChat 消息给你:               │
  │        "Nightly build v1.2.3 ready"          │
  │        "Win: download-link"                  │
  │        "Android: scan-qr-code"               │
  │        "iOS: TestFlight auto-push"           │
  ├─────────────────────────────────────────────┤
  │ 08:00  你起床 → 设备上安装                     │
  │        → 玩 30 分钟                           │
  │        → 截图/录屏 发现问题                    │
  │        → 通过 WeChat 发给 Hermes              │
  ├─────────────────────────────────────────────┤
  │ 09:00  Hermes 收到反馈                        │
  │        → 分析截图/描述                         │
  │        → 写 bug ticket                        │
  │        → 修 bug → commit → push               │
  │        → 晚上再构建                            │
  └─────────────────────────────────────────────┘
```

### 22.6 成本估算

| 项目 | 费用 |
|------|------|
| GitHub Actions (公开仓库) | **免费** (2000 min/月) |
| Unity Personal | **免费** (年收入 < $200K) |
| GameCI | **免费** (MIT 开源) |
| Git LFS | 免费 1GB 存储 + 1GB 带宽/月, 超出 $5/50GB |
| macOS runner (iOS 构建) | 2000 min/月 免费额度, 超出后 ~$0.08/min |
| Firebase App Distribution (Android) | **免费** (500 测试者) |
| TestFlight (iOS) | **免费** (10000 测试者, 需 $99/年 Apple Developer) |

> 独立开发者日常 CI/CD 可以做到 **$0/月**。只在需要 iOS 构建且超出免费额度时才产生费用。

---

## 23. 技术实施：GDD → 任务分解系统 (SDD for Game)

### 23.1 核心理念

SDD (Spec-Driven Development) 在游戏开发中的适配：

```
传统 SDD:                       游戏 SDD:
Spec.md (API 契约)      →       GDD.md (游戏设计文档)
Implement agent          →       机制编码 Agent + 美术 Agent + 音乐 Agent
Verify agent             →       Playtest + 自动化测试 + 人工体验
Quality gate             →       可玩性门禁 (好不好玩、跑不跑得通)
```

### 23.2 GDD 结构与 Agent 输出映射

```
GDD.md (你写, Hermes plan mode 辅助)
│
├── Chapter 1: 游戏概述
│   ├── 一句话描述 → Agent 理解整体方向
│   ├── 目标平台 → 构建目标
│   └── 参考游戏 → Agent 搜索了解类似游戏
│
├── Chapter 2: 核心 Loop
│   ├── 流程图 (Excalidraw) → Agent 理解玩法流程
│   └── 每个节点描述 → Agent 知道要做什么
│
├── Chapter 3: 系统设计 (按模块)
│   ├── 系统 A: 战斗系统
│   │   ├── 机制说明 (文字+图表)
│   │   ├── 数值参数表 (CSV/JSON)
│   │   ├── Agent 任务: "实现 SwordAttack.cs"
│   │   ├── 美术任务: "生成攻击帧动画 spritesheet"
│   │   └── 音效任务: "生成挥剑音效 3 个变体"
│   │
│   ├── 系统 B: 背包系统
│   ├── 系统 C: 对话系统
│   └── ...
│
├── Chapter 4: 内容规格
│   ├── 物品表 (CSV: 名称/属性/描述/图标路径)
│   ├── NPC 表 (CSV: 名字/对话树/好感度/日程表)
│   └── 关卡/地图规格 (tilemap 布局)
│
├── Chapter 5: 美术规格
│   ├── 风格指南 (调色板/分辨率/视觉参考)
│   └── 资产清单 (需要哪些 sprite/动画/UI 元素)
│
└── Chapter 6: 技术规格
    ├── 引擎/渲染管线
    ├── 目标帧率
    └── 包体大小限制
```

### 23.3 Agent 任务分解管道

```
你写 GDD.md
    │
    ▼
Hermes plan mode: 读取 GDD → 生成 tasks.json
    │
    │  tasks.json 结构:
    │  {
    │    "tasks": [
    │      {
    │        "id": "T001",
    │        "system": "战斗系统",
    │        "type": "code",
    │        "spec": "实现近战攻击: 按 X 键播放攻击动画 + 碰撞检测 + 伤害计算",
    │        "params": { "damage": 10, "range": 1.5, "cooldown": 0.5 },
    │        "output": "Scripts/Combat/MeleeAttack.cs",
    │        "test": "玩家按下 X 键 → 动画播放 → 前方 1.5m 内敌人受到 10 伤害",
    │        "depends_on": [],
    │        "delegate_to": "coding-agent"
    │      },
    │      {
    │        "id": "T002",
    │        "system": "战斗系统",
    │        "type": "art",
    │        "spec": "生成近战攻击动画: 4 帧, 64x64, 调色板 palette_01",
    │        "output": "Assets/Sprites/player_attack.png (spritesheet)",
    │        "depends_on": ["T001"],
    │        "delegate_to": "pixel-art-agent"
    │      },
    │      ...
    │    ]
    │  }
    │
    ▼
delegate_task 并行派发:
  ├── T001 → coding-agent (Claude Code harness) → review → merge
  ├── T002 → pixel-art-agent (Hermes pixel-art skill) → 你审核 → merge
  ├── T003 → music-agent (Hermes songwriting skill) → 你试听 → merge
  └── T004 → ui-agent (Figma 导出 + coding-agent 实现)
    │
    ▼
每日合并 → CI/CD 构建 → 你的设备上测试
```

### 23.4 数值设计集成

这是你最有优势的部分。GDD 中嵌入可执行的数值模型：

```
数值表示例 (放入 GDD/balance/weapons.csv):

weapon_id,name,damage,range,cooldown,speed,knockback,cost
W001,木剑,5,1.2,0.6,1.0,0.3,0
W002,铁剑,12,1.5,0.7,0.8,0.5,500
W003,火焰剑,18,1.8,0.9,0.7,0.8,2000
W004,圣剑,35,2.0,1.2,0.5,1.2,10000

Agent 自动:
  1. 读取 CSV → 生成 C# ScriptableObject 资产
  2. 运行模拟脚本验证 DPS 曲线
  3. 标记异常值 (如 "圣剑 cost=10000 但火剑 cost=2000 跳变太大")
  4. 建议调整 → 你确认 → Agent 应用
```

### 23.5 Hermes 技能设计建议

```
~/.hermes/skills/game-dev/
├── gdd-to-tasks.md         # GDD.md → tasks.json 转换
├── code-from-task.md       # 单个 task.spec → 代码实现
├── sprite-from-task.md     # 单个 task.spec → 像素画生成
├── music-from-task.md      # 单个 task.spec → 音乐生成
├── review-task.md          # Agent 产出的人工审核 checklist
├── balance-validate.md     # 读取 CSV 数值表 → 模拟 → 异常检测
└── daily-build.md          # 触发 CI/CD + 通知 + 设备部署
```

---

## 24. 技术实施：2D/2D-3D 混合美术管线

### 24.1 三条路线对比

| 路线 | 工具链 | AI 辅助点 | 难度 | 适合 |
|------|--------|----------|------|------|
| **A. 纯像素** | Aseprite → Unity Sprite Importer | pixel-art skill 生成基础帧 | ★★☆ | 像素风 RPG/平台游戏 |
| **B. 像素+3D 混合** | Aseprite(角色) + Blender(场景/大型怪物) → Unity URP | pixel-art + 低多边形 3D 可 Agent 辅助 | ★★★ | Dave the Diver 风格 |
| **C. 3D 渲染转 2D** | Blender 建模+动画 → 渲染成 spritesheet → Unity | 3D 建模需人工, 渲染可脚本自动化 | ★★★★ | Dead Cells 风格 |

### 24.2 路线 B：像素+3D 混合详解 (推荐)

这是 Dave the Diver 的方案，性价比最高：

```
资产管线:

角色/NPC/UI ────────────────────── 环境/大型怪物 ───────────
    │                                    │
    ▼                                    ▼
Aseprite (你 + pixel-art skill)    Blender (你手动 + AI 参考)
    │                                    │
    ├── 角色待机动画 (4 frame)            ├── 低多边形模型 (<1000 tris)
    ├── 角色攻击动画 (6 frame)            ├── 简单纹理 (256x256)
    ├── UI 图标 (64x64)                  └── 导出 .fbx
    └── 物品 sprite (32x32)
    │                                    │
    └────────────┬───────────────────────┘
                 ▼
          Unity URP
    ┌─────────────────────────┐
    │ 渲染层:                  │
    │  前景: Sprite Renderer  │ ← 2D 像素角色
    │  中景: 3D Mesh Renderer │ ← 低多边形场景元素
    │  背景: 3D 远景          │ ← 简单几何体 + 雾效
    │                         │
    │ 工具:                    │
    │  Cinemachine (摄像机)    │
    │  Shader Graph (材质)     │
    │  2D Light (URP 2D 光照)  │
    └─────────────────────────┘
```

### 24.3 路线 C：3D 渲染转 2D (Dead Cells 方案)

> 这是 Dead Cells 团队的精髓——一个人能用 3D 建模 + 渲染做到看起来像精心手绘的 2D 动画。

```
工作流:

1. 在 Blender 中建一个低多边形模型 (不用很精细, 因为最终渲染到 50px 高)
2. 给模型绑定骨骼, 做关键帧动画
3. 渲染脚本:
   - 用正交相机, 固定分辨率 (如 64x64)
   - 关闭抗锯齿 (保持像素锐利感)
   - 可选: 渲染法线贴图通道 (用于 Unity 2D 光照)
4. 导出为 spritesheet (.png 序列帧)
5. 导入 Unity → Sprite Editor → 切片 → 播放

重做时的优势:
  - 改动画时间: 拖动关键帧, 重新渲染 → 2 分钟
  - 给角色加装备: 把装备 mesh 绑到骨骼 → 自动渲染到所有动画帧
  - 换配色: 改材质 → 重新渲染 → 完成

传统 2D:
  - 改动画时间: 重画每一帧 → 2 小时
  - 加装备: 在每一帧上手动画 → 1 天
```

### 24.4 Agent 在美术管线中的角色

```
你的角色: 艺术总监 (Art Director)
  ├── 定义风格指南 (调色板、分辨率、视觉参考)
  ├── 审核所有 Agent 输出
  └── 手动精修关键资产 (主角、Boss、UI 主界面)

Agent 的角色: 量产工人
  ├── pixel-art skill → 生成 NPC 站立帧、物品图标、地板 tile
  ├── SD (本地 ComfyUI) → 概念图探索、纹理参考
  ├── Suno/heartmula → 背景音乐
  ├── coding-agent → 写 Unity Editor 工具脚本
  │   └── 例: "写一个 Unity Editor 脚本, 批量将 spritesheet 切片并命名"
  └── coding-agent → 写 Blender Python 脚本
      └── 例: "写一个 Blender 脚本, 批量渲染所有动画到 spritesheet"

你需要学的最小美术技能:
  ├── Aseprite: 切割/调色/关键帧精修 (3-5 天)
  ├── Blender: 简单建模+绑定+渲染 (2-4 周)
  └── 色彩理论 + 像素画基础 (持续学习)
```

---

## 25. 技术实施：多平台质量门禁 (Quality Gates)

### 25.1 门禁层级设计

```
Gate 0: 代码质量 (每次 commit)
  ├── Unity Console: 0 errors
  ├── C# 编译通过
  └── Git hooks: 禁止提交未解决的 merge conflict

Gate 1: 自动化测试 (每次 push)
  ├── EditMode Tests (纯逻辑测试, 秒级完成)
  │   ├── 伤害计算公式验证
  │   ├── 背包容量限制测试
  │   ├── 存档序列化/反序列化
  │   └── 对话树路径完整性
  │
  └── PlayMode Tests (需要游戏运行, 分钟级)
      ├── 场景加载不崩溃
      ├── 玩家生成 + 基础移动
      └── UI 按钮点击响应

Gate 2: 平台构建 (每日构建)
  ├── Windows: 构建成功 + 启动不崩溃
  ├── Android: APK 构建成功 + 安装成功
  └── iOS: Xcode 项目生成成功

Gate 3: 性能门禁 (每周)
  ├── 帧率: Windows > 60fps, Android > 30fps
  ├── 包体: APK < 200MB, IPA < 500MB
  ├── 内存: < 2GB (移动端)
  └── 启动时间: < 5 秒 (移动端冷启动)

Gate 4: 内容完整性 (里程碑)
  ├── 所有关卡可通关
  ├── 所有 NPC 对话无缺失
  ├── 所有物品图标不缺失
  └── 音效/音乐资源完整性
```

### 25.2 Unity Test Framework 实战

```csharp
// Assets/Tests/EditMode/CombatTests.cs
using NUnit.Framework;

public class CombatTests
{
    [Test]
    public void DamageCalculation_CriticalHit_DoubleDamage()
    {
        var weapon = new Weapon { baseDamage = 10 };
        var result = DamageCalculator.Calculate(weapon, isCritical: true);
        Assert.AreEqual(20, result);
    }

    [Test]
    public void DamageCalculation_Armor_ReducesDamage()
    {
        var weapon = new Weapon { baseDamage = 10 };
        var armor = new Armor { defense = 4 };
        var result = DamageCalculator.Calculate(weapon, armor, isCritical: false);
        Assert.AreEqual(6, result);
    }

    [Test]
    public void Health_NeverGoesBelowZero()
    {
        var entity = new GameEntity { health = 5 };
        entity.TakeDamage(100);
        Assert.AreEqual(0, entity.health);
    }
}
```

```csharp
// Assets/Tests/PlayMode/SceneLoadTests.cs
using UnityEngine.TestTools;
using System.Collections;
using NUnit.Framework;

public class SceneLoadTests
{
    [UnityTest]
    public IEnumerator MainMenu_LoadsWithoutError()
    {
        UnityEngine.SceneManagement.SceneManager.LoadScene("MainMenu");
        yield return null; // 等一帧
        // 不崩溃 = 通过
        Assert.IsTrue(true);
    }

    [UnityTest]
    public IEnumerator Player_SpawnAndMove_Works()
    {
        UnityEngine.SceneManagement.SceneManager.LoadScene("Gameplay");
        yield return new WaitForSeconds(1f);

        var player = GameObject.FindWithTag("Player");
        Assert.IsNotNull(player, "玩家未生成");

        var initialPos = player.transform.position;
        player.GetComponent<PlayerController>().Move(Vector3.right);
        yield return new WaitForSeconds(0.5f);

        Assert.AreNotEqual(initialPos, player.transform.position,
            "玩家没有移动");
    }
}
```

### 25.3 CI/CD 中的质量门禁实现

```yaml
# .github/workflows/quality-gates.yml
name: Quality Gates

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  # Gate 0+1: 编译 + 测试
  lint-and-test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with: { lfs: true }
      - uses: actions/cache@v3
        with:
          path: Library
          key: Library-${{ hashFiles('Assets/**', 'Packages/**', 'ProjectSettings/**') }}

      # Gate 1.1: EditMode tests
      - uses: game-ci/unity-test-runner@v4
        env:
          UNITY_LICENSE: ${{ secrets.UNITY_LICENSE }}
        with:
          testMode: editmode
          projectPath: .

      # Gate 1.2: PlayMode tests
      - uses: game-ci/unity-test-runner@v4
        env:
          UNITY_LICENSE: ${{ secrets.UNITY_LICENSE }}
        with:
          testMode: playmode
          projectPath: .

  # Gate 2: 全平台构建 (仅 main 分支)
  build-all-platforms:
    needs: lint-and-test
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    strategy:
      fail-fast: false
      matrix:
        targetPlatform:
          - StandaloneWindows64
          - Android
          - iOS
    steps:
      - uses: actions/checkout@v4
        with: { lfs: true }
      - uses: actions/cache@v3
        with:
          path: Library
          key: Library-${{ hashFiles('Assets/**', 'Packages/**', 'ProjectSettings/**') }}
      - uses: game-ci/unity-builder@v4
        env:
          UNITY_LICENSE: ${{ secrets.UNITY_LICENSE }}
        with:
          targetPlatform: ${{ matrix.targetPlatform }}

  # Gate 3: 包体大小检查
  size-check:
    needs: build-all-platforms
    runs-on: ubuntu-latest
    steps:
      - uses: actions/download-artifact@v4
      - name: Check APK size
        run: |
          apk_size=$(stat -c%s StandaloneWindows64/*.exe)
          if [ $apk_size -gt 209715200 ]; then  # 200MB
            echo "::error::Windows build exceeds 200MB: $apk_size bytes"
            exit 1
          fi
          echo "Windows build size: $apk_size bytes OK"
```

### 25.4 质量门禁检查清单 (你手动执行)

每周一次, 30 分钟:

```
手动测试清单 (记录在 GDD/checklists/weekly-qa.md):

□ 新玩家首次进入游戏 → 5 分钟内完成教程
□ 所有菜单按钮 → 点击有效果 (不能点了没反应)
□ 5 种分辨率测试 (1920x1080, 1366x768, 2560x1440, 720p, 1080x2340 竖屏)
□ 连续玩 30 分钟 → 无崩溃、无明显掉帧
□ 存档 → 退出 → 读档 → 状态完全一致
□ 切到后台再切回来 → 不崩溃
□ Android 低端机测试 (如有) → 30fps+
□ iOS TestFlight → 安装 → 启动 → 不闪退
□ 包体大小在目标范围内
□ 所有已知的 P0 bug 是否修复
```

---

## 26. 整体技术架构总览

```
你的技术栈 (推荐方案):

┌──────────────────────────────────────────────────┐
│                   开发层                           │
│                                                   │
│  GDD.md ──→ Hermes plan mode ──→ tasks.json      │
│                   │                               │
│   ┌───────────────┼───────────────┐              │
│   ▼               ▼               ▼              │
│ coding-agent  pixel-art    music-agent           │
│ (Claude Code) (p5js/pixel) (Suno/heartmula)     │
│   │               │               │              │
│   └───────────────┼───────────────┘              │
│                   ▼                               │
│            Unity 6 + URP                          │
│    ┌──────────────────────────┐                  │
│    │ 2D 像素角色 (Sprite)      │                  │
│    │ 3D 低多边形场景 (Mesh)    │                  │
│    │ Cinemachine 摄像机        │                  │
│    │ Shader Graph 材质         │                  │
│    │ Input System 跨平台输入    │                  │
│    └──────────────────────────┘                  │
└──────────────────────┬───────────────────────────┘
                       │
┌──────────────────────▼───────────────────────────┐
│                 CI/CD 层                           │
│                                                   │
│  GitHub + GameCI + GitHub Actions                  │
│  ┌─────────────────────────────────────────┐     │
│  │ push → test → build (Win/Android/iOS)    │     │
│  │ → artifact upload → notify Hermes       │     │
│  └─────────────────────────────────────────┘     │
└──────────────────────┬───────────────────────────┘
                       │
┌──────────────────────▼───────────────────────────┐
│                 分发层                             │
│                                                   │
│  Windows: 直链下载                                │
│  Android: Firebase App Distribution / ADB        │
│  iOS: TestFlight                                  │
│  Hermes → WeChat 通知你                            │
└──────────────────────────────────────────────────┘
```

---

## 27. 遗漏的技术领域

以下是目前讨论中尚未覆盖的 8 个重要技术领域。

### 27.1 存档系统 (Save/Load)

这是游戏的基础设施。存档坏了 = 玩家所有进度丢失 = 差评如潮。

```
三种方案:
  JSON (文本) — 可读可调试, 适合小游戏
  二进制 (MemoryPack) — 快且小, 适合大量数据
  ScriptableObject + Data Binding — Editor 友好

推荐: JSON + 版本号 + 自动备份 (保留最近 3 个存档)
Agent 可写: 序列化代码 + 存档验证测试 + 版本迁移脚本
```

### 27.2 分析/遥测 (Analytics)

你不知道玩家在哪卡住、在哪弃坑。数据 = 迭代的眼睛。

```
推荐: GameAnalytics (完全免费, 无数据量限制)
需追踪: 首次启动/关卡通过率/Boss战胜率/弃坑点/崩溃上报
Agent 可写: SDK 集成 + 事件定义 + 仪表板查询脚本
```

### 27.3 本地化 (Localization)

Steam 非英语玩家占 60%+。Unity Localization Package (官方免费) 是最佳选择。

```
工作流:
  代码永远不硬编码文本 → StringTable 管理
  Google Sheets 协作翻译 → CSV → 导入 Unity
  Agent 做: LLM 初译 11 种语言 → 你校对中英文

初期只做中文+英文, EA 后再加其他语言。
```

### 27.4 输入系统 (Input)

Win (键鼠) + Android/iOS (触屏) + 可能 Switch (手柄) = 需要 Unity Input System。

```
Action 驱动设计: Move/Attack/Interact → 平台自动绑定
Agent 可写: InputAction Asset 创建 + 虚拟摇杆 UI + 键位重映射
关键陷阱: 触屏和手柄 UI 完全不同, 需要上下文感知布局
```

### 27.5 Steam 集成

发布前必须接 Steamworks SDK。

```
清单:
  □ 成就系统 (20-50 个)
  □ 云存档 (几乎零代码)
  □ 排行榜 (可选)
  Agent 可写: API 封装 + 成就定义 + 触发逻辑
```

### 27.6 性能优化

移动端是硬约束。Dave the Diver 团队花大量时间在 Switch 优化。

```
移动端要点:
  渲染: URP 2D Renderer + Sprite Atlas + 限制实时光源
  内存: 纹理 < 1024x1024 + Addressables 按需加载 + 音频压缩
  CPU: Job System + Burst + 对象池
  工具: Unity Profiler 连接设备实时查看
  Agent 可帮: 分析 Profiler 数据 + 自动应用优化建议
```

### 27.7 资产管线自动化 (Asset Pipeline)

Agent 批量生成资产 → 手动导入调参是灾难。

```
AssetPostprocessor 脚本:
  自动设置像素纹理参数 (Point filter, 无压缩)
  自动切片 spritesheet
  Blender Python 脚本: 批量渲染动画
  Agent 全都可以写
```

### 27.8 Game Feel / Juice

好游戏 vs 能玩的游戏的分界线。

```
简单 (Agent 直接写):
  屏幕震动 / 命中停顿 / 伤害数字弹出 / UI 动画 / 道具收集飞行动画

需调参:
  挤压拉伸 / 粒子特效 / 音效分层 / 场景过渡

原则: 每个操作都要有反馈, 重要事件多层反馈。
      提供 "关闭屏幕震动" 选项 (防晕动症)。
```

### 27.9 遗漏度评估

| 领域 | 重要性 | Agent 能帮 | 不解决的后果 |
|------|--------|-----------|------------|
| 存档系统 | 🔴 致命 | 80% | 不可发布 |
| 输入系统 | 🔴 致命 | 60% | 跨平台不可行 |
| 本地化 | 🟡 高 | 90% | 限 60%+ 市场 |
| Steam 集成 | 🟡 高 | 80% | 不在 Steam 卖 |
| 分析/遥测 | 🟡 高 | 70% | 盲飞迭代 |
| 性能优化 | 🟡 高 | 40% | 移动端跑不动 |
| Game Feel | 🟡 高 | 30% | 游戏没灵魂 |
| 资产管线 | 🟢 中 | 70% | 大量手动劳动 |

---

## 28. 启动前检查清单

```
□ 读完 Theory of Fun + Art of Game Design (4 周)
□ Unity 6 + URP 项目创建 (1 天)
□ GameCI + GitHub Actions 跑通首次构建 (1 天)
□ 存档系统原型 (JSON + 版本号) (2 天)
□ 输入系统 (键鼠+触屏) (2 天)
□ 第一个 EditMode 测试用例 (0.5 天)
□ Aseprite 画第一个 sprite (1 天)
□ Blender 做第一个低多边形模型 (3 天)
□ 像素+3D 混合渲染测试 (3 天)
□ ~/.hermes/skills/game-dev/ 技能目录建好
□ 热身项目的 GDD 草稿
```

> 总计准备时间: 2-3 周 (业余时间) 或 1 周 (全职)。这之后可以正式开始热身项目。

> - [Game Design Books 2026](https://gamedesignskills.com/game-design/books)
> - [Best PC Hardware for Game Development 2026](https://technologists.dev/posts/best-pc-hardware-for-game-development-2026)
> - [Nvidia Game Dev Toolkit](https://gamedevai.guide/nvidia-toolkit)
> - [Unity AI Open Beta Guide 2026](https://www.buildfastwithai.com/blogs/unity-ai-open-beta-guide-2026)
> - [Nvidia ACE for Games](https://developer.nvidia.com/ace-for-games)
> - [Re-Logic / Terraria History](https://en.wikipedia.org/wiki/Re-Logic)
> - [Stardew Valley Development Story](https://www.gamedeveloper.com/business/the-4-years-of-self-imposed-crunch-that-went-into-i-stardew-valley-i-)
> - [Dyson Sphere Program Team](https://dyson-sphere-program.fandom.com/wiki/Dyson_Sphere_Program_(Game))
> - [Human Fall Flat 55M Sales](https://www.youtube.com/watch?v=MFtIB0LxYbc)
> - [Keywords Studios: The State of Vibe Coding 2026](https://www.keywordsstudios.com/en/about-us/news-events/news/the-state-of-vibe-coding-a-2026-strategic-blueprint)
> - [InnoGames: Beyond Vibe-Coding with Claude Code Skills](https://blog.innogames.com/beyond-vibe-coding-a-disciplined-workflow-for-ai-assisted-software-development-with-claude-code)
> - [Luden.io: AI Agents in Game Dev — Real Production Lessons](https://blog.luden.io/ai-agents-in-game-development-real-production-lessons-failed-experiments-and-workshop-101-7d71e64685fa)
> - [Dodo Payments: 8 Games One-Shotted with oh-my-opencode](https://medium.com/dodopayments/i-asked-ai-agents-to-build-8-games-they-one-shotted-every-single-one-cb89c59c92a0)
> - [Addy Osmani: My LLM Coding Workflow Going Into 2026](https://addyo.substack.com/p/my-llm-coding-workflow-going-into)
> - [Solo Game Development in 2026: What Actually Works (Ziva)](https://ziva.sh/blogs/solo-game-development)
> - [StraySpark: AI Music for Indie Game Soundtracks (April 2026)](https://www.strayspark.studio/blog/ai-music-generation-indie-soundtracks-2026)
> - [Best AI 3D Model Generators 2026 Review](https://levelup.gitconnected.com/building-game-ready-3d-assets-fast-the-best-ai-generators-for-production-workflows-in-2026-f1f725ff5df0)
> - [Machinations.io — Game Economy Design Platform](https://machinations.io)
