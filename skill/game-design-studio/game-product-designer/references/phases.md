# Phase-by-Phase Instructions

Detailed guidance for each phase of the product design pipeline.

Read `references/principles.md` for the 6 GDP (Game Design Principles) that apply across all phases. Each quality gate below includes GDP checkpoint items.

---

## Phase 0 · Genre Analysis

### Input
- User's game idea (verbatim)
- User's platform choice (from ASK_USER gate)
- User's differentiation answer (if provided)

### Process

#### 0A. Genre Tag Extraction
Parse the idea and assign structured genre tags. Use a hierarchy:

```
Primary Genre > Sub-Genre > Mechanic Tags
```

Example: "类似宝可梦的RPG冒险游戏" →
- Primary: RPG
- Sub-Genre: Monster Collection / Creature Taming
- Mechanic Tags: Turn-based Combat, Collection, Exploration, Evolution/Growth

#### 0B. Reference Product Analysis
Identify 3-5 reference products in the same genre space. For each:

| Product | Platform | Core Hook | Key Mechanic | Revenue Model |
|---|---|---|---|---|
| [name] | [platforms] | [what makes it addictive] | [signature mechanic] | [how it makes money] |

#### 0C. Core Mechanic Reverse-Engineering
From the reference products, extract the **shared core mechanics** that define this genre:

List each mechanic with:
- **What it is**: One-sentence description
- **Why it works**: The psychological hook (collection urge, mastery, exploration, social comparison, etc.)
- **Variations across references**: How different games implement it differently

#### 0D. Differentiation Analysis
Based on the user's description and their answer to the differentiation question:

- **Explicit**: Things the user specifically mentioned as different
- **Implicit**: Gaps in the genre that the user's description hints at
- **Open**: Differentiation not yet defined — flag as open design question

Format:
```markdown
### Differentiation
**Stated**: [what the user explicitly said]
**Inferred**: [what we can reasonably guess from context]
**Open questions**:
- [question 1 — e.g., "What's the art style?"]
- [question 2 — e.g., "PvP or purely PvE?"]
```

**Important**: Do NOT block on open questions. Record them and proceed with reasonable defaults.

#### 0E. Platform Constraint Analysis

Based on the user's platform choice, identify design constraints:

| Platform | Key Constraints |
|---|---|
| iOS / Android | Touch input, short sessions (3-10min), battery/memory limits, portrait+landscape, app store review |
| Web 浏览器 | Keyboard+mouse, no install, bandwidth-dependent assets, tab competition |
| 微信小游戏 | Package size ≤20MB (分包≤30MB), WeChat API only, social sharing hooks, touch input |
| 微信小程序 | Canvas rendering limits, 小程序 lifecycle, limited multithreading |
| PC (Steam) | Keyboard+mouse+gamepad, long sessions OK, no size limits, Steam API |
| 主机 | Gamepad input, TV distance UI, certification requirements, long sessions |

Write:
```markdown
### Platform Constraints
**Selected platform(s)**: [user's choice]
**Input method**: [touch / keyboard+mouse / gamepad / hybrid]
**Session length expectation**: [short 3-10min / medium 20-40min / long 60min+]
**Distribution constraints**: [app store / web / WeChat / Steam / console cert]
**Technical constraints**: [package size / memory / rendering / API limitations]
```

This carries forward to Phase 1 (loop design must match session length) and Phase 3 (GDD tech direction).

#### 0F. Assumption Logging (GDP-005)
Review all outputs from 0A-0E. For every statement that is an assumption rather than a confirmed fact, log it:
```markdown
### Assumptions (Phase 0)
| # | Assumption | Reasoning | Verification Method |
|---|---|---|---|
| 1 | [assumption] | [why we assume this] | [how to verify] |
```

### Output
Save to `phase0_genre_analysis.md`:
```markdown
# Phase 0: Genre Analysis

## 1. Genre Tags
[hierarchy]

## 2. Reference Products
[comparison table]

## 3. Core Mechanics (Genre-Defining)
[mechanic list with analysis]

## 4. Differentiation
[stated / inferred / open questions]

## 5. Platform Constraints
[platform analysis from 0E]

## 6. Assumptions
[table from 0F with reasoning + verification method]
```

### Quality Gate
- [ ] At least 3 reference products analyzed
- [ ] At least 3 core mechanics identified
- [ ] Differentiation section exists (even if mostly "open")
- [ ] Platform constraints section exists with session length + input method
- [ ] **GDP-005**: Every assumption has reasoning + verification method (not just "assumption" without "how to check")

---

## Phase 1 · Core Loop Design

### Input
- `phase0_genre_analysis.md`
- Original user idea (anchor)

### Process

#### 1A. 30-Second Loop (Moment-to-Moment)
This is the atomic unit of gameplay — what the player does every few seconds.

Define it as a cycle:
```
[Stimulus] → [Decision] → [Action] → [Feedback] → [loop]
```

For each step:
- What does the player see/hear?
- What choice do they make?
- How do they execute it?
- What tells them it worked (or didn't)?

The 30-second loop MUST feel good on its own, independent of progression. If the moment-to-moment isn't fun, no amount of meta-game will save it.

**Platform alignment check**: Does the action step work with the chosen input method? (e.g., touch vs keyboard vs gamepad)

**Evaluate**: How does this loop compare to the reference products' 30-second loops? Is it faster/slower/more complex/simpler?

**GDP-002 checkpoint**: Strip away all meta-game mentally. Is this 30s loop still fun? Does it contain a decision→feedback closure? If not, flag it:
`⚠️ GDP-002: 30s 循环缺乏独立乐趣——[具体原因]`

#### 1B. 5-Minute Loop (Session Arc)
This is what a single play session looks like:

```
[Session Start: Goal Setting] → [Core Gameplay Phase] → [Session End: Reward + Progress]
```

Define:
- **Entry**: How does a session begin? What goal does the player set?
- **Middle**: What's the core activity? How many 30-second loops fit in one session?
- **Exit**: What reward does the player receive? What progress marker changes?
- **Hook**: What makes the player want to start another session?

**Platform alignment check**: Does the session length match the platform expectation from Phase 0E?
- Mobile / 小游戏: 5-minute session should feel complete
- Web: 10-15 minutes is OK
- PC / Console: 20-40 minutes per session is fine

If the natural session length doesn't match the platform, flag it as a design tension and propose a solution (e.g., "add quick-battle mode for mobile sessions").

**GDP-003 checkpoint**: Describe the target player's typical play scenario (not just the platform). Is the session mode driven by player behavior analysis, or is it just "mobile = casual"? If the latter, flag it:
`⚠️ GDP-003: 会话模式缺乏玩家场景支撑——[具体原因]`

#### 1C. Core Fun Factors
Identify 2-3 primary fun factors from this framework:

| Fun Factor | Description | Present? |
|---|---|---|
| **Mastery** | Getting better at a skill | |
| **Discovery** | Finding new things | |
| **Collection** | Acquiring and organizing | |
| **Social** | Competing or cooperating | |
| **Expression** | Creating / customizing | |
| **Narrative** | Experiencing a story | |
| **Challenge** | Overcoming difficulty | |
| **Relaxation** | Low-stress, satisfying activity | |

For each identified factor: explain where in the loop it manifests.

#### 1D. Player Motivation Model
Map the game's appeal using Bartle's taxonomy or similar:

- What % of content serves **Achievers** (goal-oriented)?
- What % serves **Explorers** (discovery-oriented)?
- What % serves **Socializers** (interaction-oriented)?
- What % serves **Killers** (competition-oriented)?

This doesn't need exact numbers — directional is fine (e.g., "Primarily Explorer/Collector with Achiever end-game").

### Output
Save to `phase1_core_loop.md`:
```markdown
# Phase 1: Core Loop Design

## 1. 30-Second Loop
[cycle diagram + step analysis]
[platform alignment check result]
[GDP-002 check result]

## 2. 5-Minute Loop
[session arc + entry/middle/exit/hook]
[platform alignment check result]
[GDP-003 check: player scenario + session mode rationale]

## 3. Core Fun Factors
[top 2-3 with explanation]

## 4. Player Motivation Model
[directional mapping]

## 5. GDP Flags (if any)
[list any GDP-002 or GDP-003 flags raised in this phase]
```

### Quality Gate
- [ ] 30-second loop has a clear Stimulus → Decision → Action → Feedback cycle
- [ ] 5-minute loop has a clear session arc with hook
- [ ] At least 2 fun factors identified with concrete examples
- [ ] Session length aligns with platform constraints (or tension is flagged)
- [ ] **GDP-002**: 30s loop independence verified (or flagged)
- [ ] **GDP-003**: Session mode has player-scenario backing (or flagged)

---

## Phase 2 · System Decomposition

### Input
- `phase0_genre_analysis.md`
- `phase1_core_loop.md`
- Original user idea (anchor)

### Process

#### 2A. Module Identification
From the core loops + genre mechanics, identify all game systems/modules needed:

For each module:
- **Name**: Clear, descriptive name
- **Responsibility**: One-sentence description of what this module does
- **Loop Role**: Where does it appear in the 30s or 5min loop?
- **Genre Origin**: Which core mechanic (from Phase 0) does it implement?

#### 2B. Dependency Mapping
Draw a dependency matrix:

```
          Module A  Module B  Module C  Module D
Module A    —        ←        ×         ×
Module B    →        —        ←         ×
Module C    ×        →        —         ←
Module D    ×        ×        →         —
```

Where:
- `→` means "depends on" (this module needs that one to exist first)
- `←` means "depended upon" (that module needs this one)
- `×` means no direct dependency

Also express as a simple dependency list:
```
Module A → requires nothing (foundation)
Module B → requires Module A
Module C → requires Module B
Module D → requires Module C
```

#### 2C. Priority Classification

| Priority | Meaning | MVP? |
|---|---|---|
| **P0** | Game doesn't function without it | Yes |
| **P1** | Core experience is incomplete without it | Yes |
| **P2** | Enriches experience significantly | No — V1.1 |
| **P3** | Nice to have, can launch without | No — V2.0+ |

Classification criteria:
- Does the 30-second loop work without it? No → P0
- Does the 5-minute loop work without it? No → P1
- Does it appear in any reference product's launch version? Yes + enriching → P2
- Is it a differentiator that can be patched in later? → P2 or P3

**GDP-001 checkpoint**: After classification, verify the MVP (P0 + P1) constitutes a complete experience loop. Walk through one full player session mentally:
1. Player opens the game
2. Player enters core gameplay (30s loop works?)
3. Player completes a session (5min loop works?)
4. Player feels satisfaction (reward/progress marker changes?)
5. Player has a reason to come back (hook works?)

If any step breaks without a deferred module, that module should be P1, not P2. Flag if the MVP boundary feels incomplete:
`⚠️ GDP-001: MVP 可能不构成完整体验闭环——[哪个环节断裂]`

**GDP-004 checkpoint**: Check that the game's stated differentiator is included in P0 or P1 (not deferred to P2+). Then test the three criteria:
- 可感知: Player experiences it in the first 5 minutes?
- 可描述: One sentence description?
- 可验证: A test method exists?

Flag if any criterion fails:
`⚠️ GDP-004: 差异化点 [X] 未通过三标准——[哪个失败]`

#### 2D. Module Detail Cards
For each MVP module (P0 + P1), write a detail card:

```markdown
### [Module Name]
**Priority**: P0/P1
**Responsibility**: [one sentence]

**Core Features**:
- [feature 1]
- [feature 2]
- [feature 3]

**Experience Design Intent** (GDP-006):
- **Core Decisions** (1-3): [What meaningful choices does the player make in this module? Each decision: what are the options? what info does the player have? how fast is the feedback?]
- **Satisfaction Moments** (1-2): [Specific moment when the player feels excited/satisfied. Be concrete — not "feels good" but "seeing the formation light up and chain-trigger across the board"]
- **Strategy Space**: [Multiple viable strategies? Or dominant single path? Describe the key strategic axes.]

**Depends On**: [list]
**Depended By**: [list]
**Design Considerations**:
- [key design question or risk]
**Estimated Complexity**: Simple / Medium / Complex
```

**GDP-006 checkpoint**: Every P0/P1 module MUST have the Experience Design Intent section filled in with specific content. Generic statements (like "makes the player happy") are not acceptable — they must describe concrete decisions, moments, and strategies.

Flag if missing or vague:
`⚠️ GDP-006: 模块 [X] 缺少体验意图声明——[缺失哪个字段]`

For P2/P3 modules, a one-liner summary is sufficient (no experience design section needed).

### Output
Save to `phase2_systems.md`:
```markdown
# Phase 2: System Decomposition

## 1. Module List
[table: name, responsibility, loop role, priority, MVP?]

## 2. Dependency Map
[ASCII matrix + dependency chain]

## 3. MVP Boundary
**MVP includes**: [P0 + P1 modules]
**V1.1 candidates**: [P2 modules]
**Future**: [P3 modules]
[GDP-001 experience loop verification result]
[GDP-004 differentiation check result]

## 4. Module Detail Cards
[detail card for each P0/P1 module — including Experience Design Intent]
[one-liner for each P2/P3 module]

## 5. GDP Flags (if any)
[list any GDP-001, GDP-004, GDP-006 flags raised in this phase]
```

### Quality Gate
- [ ] Every module maps to at least one loop role or genre mechanic
- [ ] No circular dependencies in the dependency map
- [ ] MVP contains all P0 and P1 modules
- [ ] Every P0/P1 module has a detail card
- [ ] **GDP-001**: MVP experience loop verified (or flagged)
- [ ] **GDP-004**: Differentiator passes three criteria (or flagged)
- [ ] **GDP-006**: Every P0/P1 module card has Experience Design Intent with concrete content (or flagged)

---

## Phase 3 · GDD Assembly

### Input
- `phase0_genre_analysis.md`
- `phase1_core_loop.md`
- `phase2_systems.md`
- `pipeline_state.json` (for assumptions, open questions, platform, and GDP flags)

### Process

Assemble all phase outputs into a single GDD following the template in `references/templates.md`.

**Rules**:
1. **No information loss** — every insight from Phase 0-2 must appear in the GDD
2. **Reorganize, don't just concatenate** — the GDD structure groups information differently from the phase outputs
3. **Add connective tissue** — write transition sentences that explain how sections relate
4. **Consolidate assumptions** — gather all assumptions from all phases into Appendix A, ensuring each has reasoning + verification method (GDP-005)
5. **Highlight open questions** — anything marked as "open" in Phase 0 gets collected in Appendix B
6. **Platform & Tech Direction** — Chapter 1.4 MUST include the confirmed platform and recommended tech stacks from the SKILL.md mapping table. Present 2-3 candidates with tradeoffs.
7. **GDP final sweep** — Collect ALL GDP flags from all phases into Appendix C: Design Risk Flags. Also do a final GDP-005 sweep across the entire GDD for any uncaptured assumptions.

### Output
Save to `{game_name}_GDD.md` following the template structure.

### Quality Gate
- [ ] All 5 chapters present + 3 appendices
- [ ] No empty sections
- [ ] Assumptions appendix exists with reasoning + verification method for each (GDP-005)
- [ ] Open questions appendix exists
- [ ] MVP boundary is explicitly stated
- [ ] **Chapter 1.4 has confirmed platform + tech stack recommendations**
- [ ] **Chapter 3.4 has differentiation declaration with GDP-004 three-criteria test**
- [ ] **Chapter 4 module cards all have Experience Design Intent (GDP-006)**
- [ ] **Appendix C collects all GDP flags (may be empty if all checks passed)**
- [ ] GDD is self-contained (a reader who hasn't seen the phase files can understand it fully)
