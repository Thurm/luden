# Game Design Studio

A comprehensive toolbox for game design, covering the complete workflow from game idea to module architecture.

## 🎯 Core Concept: Complementary Skills

| Dimension | Skill A (game-product-designer) | Skill B (game-module-architect) |
|---|---|---|
| **Responsibility** | Define WHAT — the player experience | Define HOW — technical implementation |
| **Input** | One-line game idea | GDD + Architecture Contract |
| **Output** | Structured Game Design Document (GDD) | Single-module technical spec |
| **Game Design** | ✅ Defines core loops, experience intent, differentiation | ❌ Reads from GDD, does not invent |
| **Technical Architecture** | ❌ Only recommends candidate tech stacks | ✅ Designs systems, data models, state machines |
| **Quality Constraints** | 6 GDPs (soft constraints, risk flags) | 7 ADRs (hard constraints, triggers loops) |

## ⚠️ When to Use (and When NOT to Use)

These skills shine in specific scenarios. Know their strengths and boundaries:

| Dimension | ✅ Skill Shines | ❌ Skill Degrades |
|---|---|---|
| **Task Scale** | Complete module / system design | Quick questions, minor tweaks, incremental changes |
| **Output Format** | Technical documents, GDD | PPT, email, one-paragraph summary, bullet points |
| **User Intent** | "Help me design this systematically" | "Just give me a conclusion", "Skip analysis", "Go wild" |
| **User Role** | Junior designers needing framework guidance | Experts with pre-made decisions (the pipeline would feel prescriptive) |
| **Domain** | Game development | Non-game (gamification for SaaS, education, etc.) |

## 📦 Installation

### Claude Code (Recommended)

```bash
# Option A: Project-level installation (current project only)
cp -r game-design-studio/ .claude/skills/game-design-studio/

# Option B: Global installation (all projects)
cp -r game-design-studio/ ~/.claude/skills/game-design-studio/
```

After installation, Claude Code will automatically discover these slash commands:

| Command | Description |
|---|---|
| `/game-product-designer` | From one-line idea → complete GDD (with experience design intent) |
| `/game-module-architect` | From GDD → module technical design (with intent traceability) |

Natural language triggering is also supported (e.g., "design a game for me", "create a technical plan for this module").

### OpenClaw

```bash
cp -r game-design-studio/ skills/game-design-studio/
```

OpenClaw will automatically scan the `skills/` directory and register all SKILL.md files.

## 🛠️ Included Skills

### Skill A: game-product-designer

**Responsibility**: From idea to GDD

**Unique Capabilities**:
- Genre analysis + competitive reverse-engineering
- Core loop design (30s + 5min)
- System decomposition + MVP boundary decisions
- **Experience Design Intent** (core decisions, satisfaction moments, strategy space for each MVP module)
- **6 GDP Quality Checks** (soft constraints, generate risk flags on violation)

**GDP Principles**:

| GDP | Principle | Checkpoint |
|---|---|---|
| GDP-001 | MVP = Complete experience loop | Phase 2 |
| GDP-002 | Core loop independence | Phase 1 |
| GDP-003 | Session mode adaptation | Phase 1 |
| GDP-004 | Differentiation: perceivable, describable, verifiable | Phase 2 |
| GDP-005 | Transparent assumptions + verification methods | Phase 0 + Phase 3 |
| GDP-006 | Explicit experience intent | Phase 2 |

### Skill B: game-module-architect

**Responsibility**: From GDD to technical plan

**Unique Capabilities**:
- **Read design intent from GDD** → Verify technical implementability
- Architecture contract review + backward compatibility verification
- System design (state machines, event flows, data models)
- Tech stack recommendations
- Implementation task breakdown
- **7 ADR Hard Constraints** (violations trigger Phase 4 loop)

**Key Boundary**: Skill B does NOT invent game design. Player decision points, risk/reward models, progression pacing, strategy depth — these are defined in the GDD by Skill A. Skill B reads and verifies technical implementability.

## 💡 Usage Scenarios

### Scenario A: From Scratch (Just an Idea)

```
User: /game-product-designer Make a Pokemon-like RPG adventure game
      ↓ (Skill A asks about platform and differentiation first)
      ↓ (4-phase auto-pipeline + GDP quality checks)
      → Output: monster_quest_GDD.md
      → GDD includes experience design intent + design risk flags for each module

User: /game-module-architect Design the battle system module
      ↓ (Reads GDD experience intent + 5-phase auto-pipeline)
      → Output: module_M01_battle_system_design.md
      → Design document includes design intent traceability
```

### Scenario B: Existing Design Document

Use `/game-module-architect` directly → produces technical plan
(If GDD lacks experience intent declarations, Skill B will flag and infer from feature descriptions)

### Scenario C: Product Design Only

Use only `/game-product-designer` → produces GDD, hand off to human team for technical design

## 🔗 Skill Interoperability

Skill A's GDD output format is specifically designed for Skill B's input:

| GDD Content | How Skill B Uses It |
|---|---|
| Chapter 3 (System Overview) Module List | Phase 1 reads, extracts features |
| Chapter 4 (Module Details) Experience Design Intent | Phase 1 reads, verifies technical implementability |
| Chapter 3.3 MVP Boundary | Determines which modules to design first |
| Chapter 1.4 Platform & Tech Direction | Phase 3 recommends specific tech stacks based on this |
| Appendix C GDP Risk Flags | Phase 1 notes design risk points |

## 🔍 Auto-Audit

The toolbox includes an automatic audit mechanism: when Claude Code completes a coding task, it automatically verifies the implementation against Skill B's design document.

- **Trigger**: Automatically fires every time Claude Code finishes responding (Stop hook)
- **Verification Scope**: Data model fields, interface signatures, event names, state machine transitions
- **Output Format**: ✅ PASS / ⚠️ DRIFT / ❌ VIOLATION

See `audit-hook/SKILL.md` for details.

## 📁 Directory Structure

```
game-design-studio/
├── README.md                           # This file
├── README_CN.md                        # Chinese documentation
├── .claude-plugin/                     # Plugin configuration
│   ├── plugin.json                     # Plugin metadata
│   └── marketplace.json                # Marketplace listing
├── game-product-designer/              # Skill A: Product Designer
│   ├── SKILL.md                        # Main entry + GDP overview
│   ├── references/
│   │   ├── phases.md                   # 4-phase detailed guide + GDP checkpoints
│   │   ├── templates.md                # GDD template (with experience design intent fields)
│   │   └── principles.md               # 6 GDP principle definitions
│   └── evals/
│       └── evals.json                  # 11 assertions / eval
├── game-module-architect/              # Skill B: Module Architect
│   ├── SKILL.md                        # Main entry + boundary principles
│   ├── references/
│   │   ├── phases.md                   # 5-phase detailed guide (Phase 1C = design intent verification)
│   │   ├── templates.md                # Output template
│   │   └── adr.md                      # 7 ADR hard constraints
│   └── evals/
│       └── evals.json                  # 11 assertions / eval
└── audit-hook/                         # Auto-audit Hook
    └── SKILL.md
```

## 📄 License

MIT License - feel free to use in your projects.

## 🤝 Contributing

Contributions are welcome! Feel free to submit issues or pull requests.
