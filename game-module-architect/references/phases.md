# Phase-by-Phase Instructions

Detailed guidance for each pipeline phase. The SKILL.md gives the overview; this file gives the specifics.

---

## Phase 1 · Requirements Anchoring + Design Intent Verification

### Input
- Game design document (full text from Feishu or local file)

### Process

#### 1A. Feature Extraction
Read the design doc and list every feature/mechanic. For each one, classify it:

| Category | Meaning | Action |
|---|---|---|
| **Core** | Essential for the module to function | Must include in design |
| **Enhanced** | Adds depth but module works without it | Include if effort is low |
| **Future** | Clearly belongs to a later iteration | Explicitly exclude with reason |

Output as a table with columns: Feature, Category, Reason.

#### 1B. MVP Trim Decisions
For each "Enhanced" and "Future" feature, write a one-line justification for why it's deferred. This prevents scope creep during design.

Format:
```
### MVP Boundary

**In scope (Core)**:
- [feature]: [why it's essential]

**Deferred (Enhanced)**:
- [feature]: [why it can wait]

**Out of scope (Future)**:
- [feature]: [which module it belongs to instead]
```

#### 1C. Design Intent Verification

Read the GDD's module card for this module (typically Chapter 4 of the GDD). The GDD — produced by the game-product-designer skill — should contain an **Experience Design Intent** section for each MVP module, declaring:
- **Core Decisions**: What meaningful choices the player makes
- **Satisfaction Moments**: When the player feels excited/satisfied
- **Strategy Space**: Whether multiple viable strategies exist

**Your job here is NOT to define game design — that's the game designer's territory.** Your job is to:

1. **Extract**: Read the Experience Design Intent from the GDD module card. Copy it verbatim into this document as the design anchor.

2. **Verify technical implementability**: For each declared design intent, assess:
   - Can the core decisions be supported by the module's systems? (e.g., does the decision require real-time input that conflicts with a turn-based architecture?)
   - Can the satisfaction moments be delivered technically? (e.g., does a "chain reaction visual explosion" require particle systems the platform may not support?)
   - Does the strategy space require systems that are within MVP scope? (e.g., if "multiple build paths" requires a skill tree system that's marked P2, flag the conflict)

3. **Flag constraints**: If any design intent has technical concerns, document them clearly:
   ```
   ⚠️ Design Intent Constraint: [intent item] — [technical concern]
   Suggested resolution: [propose an alternative that preserves the design intent]
   ```

4. **Confirm core loop positioning**: Where does this module sit in the game's overall loop? This should come FROM the GDD, not be invented here.
   ```
   [Start] → [Phase A] → [THIS MODULE] → [Phase C] → [End/Restart]
   ```

**If the GDD lacks an Experience Design Intent section**: This is a degraded input scenario. In this case:
- Flag it: `⚠️ GDD missing Experience Design Intent for this module`
- Extract whatever design intent you can infer from the GDD's feature descriptions
- Clearly mark inferred intent as "[Inferred — not explicitly stated in GDD]"
- Recommend that the game designer enrich the GDD module card before finalizing the architecture

### Output
Save to `phase1_requirements.md`. Structure:
```markdown
# Phase 1: Requirements Anchoring + Design Intent Verification

## 1. Feature List
[table]

## 2. MVP Boundary
[in/deferred/out]

## 3. Design Intent (from GDD)
### Core Loop Positioning
[extracted from GDD — where this module sits in the overall loop]

### Experience Design Intent
[copied from GDD module card]
- Core Decisions: [from GDD]
- Satisfaction Moments: [from GDD]
- Strategy Space: [from GDD]

### Technical Implementability Assessment
[for each design intent item: feasible / constrained / blocked]
[any ⚠️ flags with suggested resolutions]
```

### Quality Gate
Before moving to Phase 2, verify:
- [ ] Every core feature maps to at least one system responsibility
- [ ] Design intent has been extracted from the GDD (or flagged as missing)
- [ ] Technical implementability assessed for each design intent item
- [ ] No unresolved "blocked" items (constrained items are OK with documented workarounds)

---

## Phase 2 · Architecture Contract Review

### Input
- `phase1_requirements.md`
- Architecture contract document (from Feishu, if exists)

### Process

#### If architecture contract exists:
1. Scan all interfaces, models, events, stores, and bootstrap chain
2. For each core feature from Phase 1, identify which existing interfaces/models need extension
3. Identify what's entirely new (new systems, new models, new events)
4. Fill in the Interface Change Declaration template (see `references/templates.md`)
5. For each change, explicitly state: "This change IS / IS NOT backward compatible because..."

#### If no architecture contract (first module):
1. Design the initial interface landscape based on Phase 1 features
2. Define initial data models, events, stores
3. Output in the same template format, marking everything as "v1.0 initial"

### Key Rules
- **Adding fields to existing models**: OK, mark as backward compatible
- **Changing field types**: Breaking change, must find alternative
- **Removing fields**: Forbidden, always find another way
- **Adding methods to systems**: OK if existing methods unchanged
- **Changing method signatures**: Breaking change, must find alternative
- **New events**: Always OK
- **Changing event payloads**: Breaking if removing fields, OK if adding

### Output
Save to `phase2_interface_changes.md`. Use the template from `references/templates.md`.

### Quality Gate
- [ ] Every change has a backward compatibility declaration
- [ ] No "TBD" or "maybe" in compatibility assessments
- [ ] New systems have declared dependencies for bootstrap chain

---

## Phase 3 · Technical Architecture Design

### Input
- `phase1_requirements.md`
- `phase2_interface_changes.md`

### Process

For each system identified in Phase 2, produce:

#### System Design Block
```markdown
### [SystemName]

**Responsibility**: [one sentence]

**Dependencies**: [other systems or interfaces it needs]

**State Model**:
[ASCII diagram showing internal state transitions]

**Public Capabilities**:
| Capability | Input | Output | When triggered |
|---|---|---|---|
| [name] | [params] | [return] | [context] |

**Key Algorithm**: [describe in natural language]
[if complex, use pseudocode — but NOT compilable code]

**Design Intent Traceability**:
[Which GDD design intent items (from Phase 1 Section 3) does this system serve?
How does the technical design preserve the intended player experience?]
```

#### Data Model Specification
For each model, describe fields with types and constraints. Type signatures (like TypeScript interfaces) are OK as specification — but no class bodies, constructors, or method implementations.

Format:
```markdown
### [ModelName]

| Field | Type | Constraint | Description |
|---|---|---|---|
| id | string | unique | ... |
| ... | ... | ... | ... |

**Backward compatibility**: [if extending existing model, list old fields preserved]
```

#### State Flow Design
Use ASCII diagrams for state machines:
```
[State A] ──event──▶ [State B] ──event──▶ [State C]
     │                                        │
     └────────── on failure ──────────────────┘
```

#### Event Flow Design
Table format:
| Event | Payload | Publisher | Subscriber | New/Existing |
|---|---|---|---|---|

#### Configuration Schema
For each config file, describe the schema and provide sample data (as data, not code):
```markdown
### [config-name].json
**Purpose**: ...
**Schema**:
| Field | Type | Description |
|---|---|---|
| ... | ... | ... |

**Sample entry**:
| name | type | value | ... |
|---|---|---|---|
| ... | ... | ... | ... |
```

### The no-code rule in practice

Here's how to handle the boundary:

**✅ This is a specification** (OK):
```
FormationResult contains:
  - type: FormationType (the matched formation)
  - basePower: number (formation's base power value)
  - scoringCards: ICard[] (cards that contributed to the match)
```

**❌ This is implementation** (NOT OK):
```typescript
class FormationSystem {
  evaluate(cards: ICard[]): FormationResult {
    const sorted = cards.sort((a, b) => a.rank - b.rank);
    for (const detector of this.detectors) {
      const result = detector.check(sorted);
      if (result) return result;
    }
    return this.fallbackSingle(cards);
  }
}
```

**✅ This describes the algorithm without implementing it** (OK):
```
FormationSystem.evaluate algorithm:
1. Sort input cards by rank (ascending)
2. Test against formation detectors in priority order (highest-value first)
3. First detector that matches wins — return its FormationResult
4. If no detector matches, fall back to "single" formation using the highest-rank card
5. Special case: if any card has rank=1 (Ace), test both as rank 1 and rank 14, return whichever yields higher total score
```


#### Tech Stack Recommendation

If the input GDD contains a "Platform & Tech Direction" section (typically Chapter 1.4):

1. Read the platform choice and candidate tech stacks from the GDD
2. Evaluate each candidate against this specific module's needs:
   - Rendering requirements (2D/3D/UI-heavy)
   - State management complexity
   - Multiplayer / real-time needs
   - Asset pipeline requirements
3. Recommend ONE primary tech stack with rationale
4. Note how the tech stack choice affects the design:
   - What serialization format for data models?
   - What event/messaging pattern is native to this stack?
   - What config loading mechanism to use?

Format for the tech stack section in `phase3_tech_design.md`:

```markdown
## Tech Stack Recommendation

**GDD platform**: [e.g., "iOS + Android 移动端"]
**GDD candidates**: [e.g., "Unity, Cocos Creator, Godot"]

**Recommended**: [e.g., "Cocos Creator 3.x"]
**Rationale**:
- [reason 1]
- [reason 2]
- [reason 3]

**Design implications**:
- Data serialization: [e.g., "JSON via cc.JsonAsset"]
- Event system: [e.g., "cc.EventTarget / Node event system"]
- Config loading: [e.g., "JSON resources loaded via cc.resources.load"]
- State management: [e.g., "Component-based with dedicated manager nodes"]
```

If the GDD has no tech direction section, **ask the user** for their preferred tech stack before Phase 5. The task plan must reference a specific stack so coding agents can consume it directly.

### Output
Save to `phase3_tech_design.md`.

### Quality Gate
- [ ] Every system has: responsibility + dependencies + capabilities table + design intent traceability
- [ ] Every data model has: field table with types and constraints
- [ ] No compilable code anywhere in the document
- [ ] At least one state flow diagram for the primary game loop in this module
- [ ] Design intent from GDD is traceable through the technical design (no design intent items "lost" in translation)

---

## Phase 4 · Compatibility Verification

### Input
- `phase3_tech_design.md`
- Architecture contract document (from Feishu)

### Process

If no architecture contract exists, this phase is a lightweight self-consistency check instead — verify that Phase 3's designs are internally consistent (no circular dependencies, no undefined references, all events have both publishers and subscribers).

If architecture contract exists:

1. **Extract all changes** from Phase 2 and Phase 3
2. **For each change**, compare against the contract:
   - Does the field/method/event already exist?
   - If modifying: is the modification backward compatible?
   - If adding: does it conflict with any existing names?
   - Does the bootstrap chain ordering still work?

3. **Classify each conflict**:

| Type | Description | Resolution |
|---|---|---|
| **A — Additive** | Just needs new fields/methods, no breaking changes | Auto-fix in Phase 2, re-verify |
| **B — Breaking** | Would break existing consumers of the interface | Loop back to Phase 3 to redesign |
| **C — Scope overflow** | Feature exceeds MVP boundary from Phase 1 | Loop back to Phase 1 to re-trim |

4. **For each conflict**, write:
   - What exactly conflicts
   - Why it's a problem
   - Proposed resolution
   - Which phase to loop back to

### Loop Logic

```
IF conflicts.length == 0:
    convergence = true → proceed to Phase 5

ELSE IF loop_count < max_loops:
    FOR EACH conflict:
        IF Type A → revise phase2_interface_changes.md
        IF Type B → revise phase3_tech_design.md (only the conflicting system)
        IF Type C → revise phase1_requirements.md (trim the overflowing feature)
    loop_count++
    Re-run Phase 4

ELSE:
    Output best current result
    List all unresolved conflicts as "Manual Review Required"
    Proceed to Phase 5 with warnings
```

### Output
Save to `phase4_compat_report.md`. Structure:
```markdown
# Phase 4: Compatibility Verification Report

## Summary
- Total checks: N
- Conflicts found: N
- Loop iteration: N of 3

## Conflict Details (if any)
### Conflict 1: [description]
- **Type**: A/B/C
- **What**: [specific interface/model/event]
- **Why**: [why it conflicts]
- **Resolution**: [what was changed]
- **Looped back to**: Phase N

## Verification Passed ✓ (if no conflicts)
All proposed changes are backward compatible with the existing architecture contract.

## Unresolved Items (if max loops reached)
- [item]: [why it couldn't be resolved automatically]
```

---

## Phase 5 · Implementation Planning

### Input
- `phase3_tech_design.md`
- `phase4_compat_report.md`

### Process

#### Task Decomposition
Break Phase 3's design into implementation tasks. Each task must be:
- Completable in ≤ 0.5 day (4 hours)
- Independently testable
- Have a clear "done" definition
- **Reference the confirmed tech stack** from Phase 3's Tech Stack Recommendation

Prepend the task plan with a tech context block:
```markdown
## Tech Context
**Tech stack**: [confirmed stack from Phase 3]
**Language**: [e.g., TypeScript, C#, GDScript]
**Key frameworks**: [e.g., Cocos Creator 3.x, Phaser 3]
**This section enables a coding agent to directly consume the task plan without additional tech stack questions.**
```

Format:
```markdown
| # | Task | Depends on | Estimate | Acceptance criteria |
|---|---|---|---|---|
| 1 | [what to build] | — | 0.5d | [scenario: when X, then Y] |
| 2 | [what to build] | Task 1 | 0.25d | [scenario description] |
```

#### Acceptance Criteria
Write criteria as scenarios, not as code:
```
Given: player has 5 cards in hand including [A♠, A♥, A♦]
When: player plays all three A cards
Then: system recognizes "Three of a Kind" formation
And: base power is 30, base multiplier is 3
And: scoring cards are the three A cards only
```

#### Test Scenarios
For each key system, describe 3-5 test scenarios covering:
- Happy path (normal usage)
- Edge case (boundary conditions)
- Error case (invalid input)

Describe in natural language — do not write test code.

#### Effort Estimate
Sum up task estimates and add 20% buffer for integration:
```markdown
## Effort Summary
- Core tasks: X days
- Integration buffer (20%): Y days
- **Total estimate**: Z days
```

### Output
Save to `phase5_task_plan.md`.
