# Game Design Studio — 游戏设计工具箱

一个包含两个互补 Skill 的工具箱，覆盖从游戏创意到模块技术方案的完整设计流程。

[English README](./README.md)

## 🎯 核心理念：互补而非重叠

| 维度 | Skill A (game-product-designer) | Skill B (game-module-architect) |
|---|---|---|
| **职责** | 定义 WHAT — 玩家体验什么 | 定义 HOW — 技术如何实现 |
| **输入** | 一句话游戏想法 | GDD + 架构契约 |
| **输出** | 结构化游戏设计文档 (GDD) | 单模块技术设计规格 |
| **游戏设计** | ✅ 定义核心循环、体验意图、差异化 | ❌ 从 GDD 读取，不自行定义 |
| **技术架构** | ❌ 只推荐候选技术栈 | ✅ 设计系统、数据模型、状态机 |
| **质量约束** | 6 条 GDP（软约束，风险标记） | 7 条 ADR（硬约束，触发回环） |

## ⚠️ 使用场景提示（增益 vs 劣化）

这些 Skill 在特定场景下表现最佳。请了解它们的优势和边界：

| 维度 | ✅ Skill 增益 | ❌ Skill 劣化 |
|---|---|---|
| **任务规模** | 完整模块 / 系统设计 | 快速问答、微调、增量改动 |
| **输出格式** | 技术文档、GDD | PPT、邮件、一段话、bullet list |
| **用户意图** | "帮我系统设计一下" | "给个结论"、"别分析了"、"越 wild 越好" |
| **用户身份** | 需要框架引导的初级设计者 | 已有决策的专家（pipeline 在教他做事） |
| **领域** | 游戏开发 | 非游戏（SaaS、教育等场景的游戏化） |

## 📦 安装方式

### Claude Code（推荐）

```bash
# 方法 A：项目级安装（仅当前项目可用）
cp -r game-design-studio/ .claude/skills/game-design-studio/

# 方法 B：全局安装（所有项目可用）
cp -r game-design-studio/ ~/.claude/skills/game-design-studio/
```

安装后，Claude Code 会自动发现以下斜杠命令：

| 命令 | 说明 |
|---|---|
| `/game-product-designer` | 从一句话游戏创意 → 完整 GDD（含体验设计意图） |
| `/game-module-architect` | 从 GDD → 单模块技术设计文档（含设计意图可追溯性） |

也支持自然语言触发（如 "帮我设计一个游戏"、"把这个模块做技术方案"）。

### OpenClaw

```bash
cp -r game-design-studio/ skills/game-design-studio/
```

OpenClaw 会自动扫描 `skills/` 目录并注册所有 SKILL.md。

## 🛠️ 包含的 Skill

### Skill A: game-product-designer（产品设计师）

**职责**: 从创意到 GDD

**独有能力**:
- 品类解析 + 竞品逆向工程
- 核心循环设计（30s + 5min）
- 模块拆解 + MVP 边界决策
- **体验意图声明**（每个 MVP 模块的核心决策、爽点时刻、策略空间）
- **6 条 GDP 质量检查**（软约束，违反生成风险标记）

**GDP 原则**:

| GDP | 原则 | 检查点 |
|---|---|---|
| GDP-001 | MVP = 核心体验闭环 | Phase 2 |
| GDP-002 | 核心循环独立原则 | Phase 1 |
| GDP-003 | 会话模式适配原则 | Phase 1 |
| GDP-004 | 差异化可感知、可描述、可验证 | Phase 2 |
| GDP-005 | 假设透明 + 验证方法 | Phase 0 + Phase 3 |
| GDP-006 | 体验意图显式化 | Phase 2 |

### Skill B: game-module-architect（模块架构师）

**职责**: 从 GDD 到技术方案

**独有能力**:
- **从 GDD 读取设计意图** → 评估技术可实现性
- 架构契约审查 + 后向兼容性验证
- 系统设计（状态机、事件流、数据模型）
- 技术栈推荐
- 实施任务拆解
- **7 条 ADR 硬约束**（违反触发 Phase 4 回环）

**关键边界**: Skill B 不发明游戏设计。玩家决策点、风险收益模型、进阶节奏、策略深度——这些由 Skill A 在 GDD 中定义，Skill B 读取并验证技术可实现性。

## 💡 使用场景

### 场景 A：从零开始（只有一个 idea）

```
用户: /game-product-designer 做一个类似宝可梦的 RPG 冒险游戏
      ↓ (Skill A 先问平台和差异化)
      ↓ (4 阶段自动流水线 + GDP 质量检查)
      → 输出: monster_quest_GDD.md
      → GDD 包含每个模块的体验设计意图 + 设计风险标记

用户: /game-module-architect 设计战斗系统模块
      ↓ (读取 GDD 体验意图 + 5 阶段自动流水线)
      → 输出: module_M01_battle_system_design.md
      → 设计文档包含设计意图可追溯性
```

### 场景 B：已有策划文档

直接使用 `/game-module-architect` → 产出技术方案
（如果 GDD 缺少体验意图声明，Skill B 会标记并从功能描述中推断）

### 场景 C：只需要产品设计

只使用 `/game-product-designer` → 产出 GDD，交给人类团队做技术设计

## 🔗 两个 Skill 的衔接

Skill A 的 GDD 输出格式专门为 Skill B 的输入设计：

| GDD 内容 | Skill B 如何使用 |
|---|---|
| Chapter 3 (System Overview) 模块清单 | Phase 1 读取，提取功能点 |
| Chapter 4 (Module Details) 体验设计意图 | Phase 1 读取，验证技术可实现性 |
| Chapter 3.3 MVP Boundary | 确定先设计哪些模块 |
| Chapter 1.4 Platform & Tech Direction | Phase 3 据此推荐具体技术栈 |
| Appendix C GDP Risk Flags | Phase 1 注意设计风险点 |

## 🔍 自动审计（Auto-Audit）

工具箱内置了一个自动审计机制：当 Claude Code 完成编码任务时，会自动对照 Skill B 的设计文档验证实现是否符合规格。

- **触发时机**：每次 Claude Code 响应结束时自动触发（Stop hook）
- **验证范围**：数据模型字段、接口签名、事件名称、状态机转换
- **输出格式**：✅ PASS / ⚠️ DRIFT / ❌ VIOLATION

详见 `audit-hook/SKILL.md`。

## 📁 目录结构

```
game-design-studio/
├── README.md                           # 英文文档
├── README_CN.md                        # 中文文档（本文件）
├── .claude-plugin/                     # 插件配置
│   ├── plugin.json                     # 插件元数据
│   └── marketplace.json                # Marketplace 配置
├── game-product-designer/              # Skill A: 产品设计师
│   ├── SKILL.md                        # 主入口 + GDP 总览
│   ├── references/
│   │   ├── phases.md                   # 4 阶段详细指南 + GDP 检查点
│   │   ├── templates.md                # GDD 模板（含体验设计意图字段）
│   │   └── principles.md              # 6 条 GDP 原则定义
│   └── evals/
│       └── evals.json                  # 11 assertions / eval
├── game-module-architect/              # Skill B: 模块架构师
│   ├── SKILL.md                        # 主入口 + 边界原则
│   ├── references/
│   │   ├── phases.md                   # 5 阶段详细指南（Phase 1C = 设计意图验证）
│   │   ├── templates.md                # 输出模板
│   │   └── adr.md                      # 7 条 ADR 硬约束
│   └── evals/
│       └── evals.json                  # 11 assertions / eval
└── audit-hook/                         # 自动审计 Hook
    └── SKILL.md
```

## 📄 许可证

MIT License - 可自由在项目中使用。

## 🤝 贡献

欢迎贡献！欢迎提交 Issue 或 Pull Request。
