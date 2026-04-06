# Architecture Decision Records (ADR)

These decisions constrain all design output. When Phase 4 finds a violation, it's a conflict that triggers a loop.

---

## ADR-001: Backward Compatibility Iron Law

**Decision**: When extending existing interfaces or data models, adding new fields/methods is permitted. Deleting or renaming existing fields/methods is forbidden.

**Rationale**: Multiple modules consume shared interfaces. A rename that seems trivial breaks every existing consumer. The cost of "one more field" is always less than the cost of coordinating a rename across all modules.

**In practice**:
- New optional field on a model → OK
- Rename `type` to `formationType` → FORBIDDEN (add `formationType` alongside `type` if needed)
- Remove deprecated field → FORBIDDEN (mark as deprecated in comments, keep the field)

---

## ADR-002: Data Model Extension Pattern (FormationResult Pattern)

**Decision**: When a new module needs to extend a shared data model, keep all original fields at the top level AND add new fields alongside them. Do not nest, wrap, or restructure.

**Rationale**: Existing consumers read specific top-level fields. If you nest them inside a sub-object, every consumer breaks. The "flat extension" approach means old code keeps working while new code can use the new fields.

**Example**:
```
Original model has: type, basePower, baseMultiplier
Module 2 needs: formation object, upgrade bonus, affix effect

✅ Correct: Keep type/basePower/baseMultiplier at top level, ADD formation/upgradeBonus/affixEffect
❌ Wrong: Wrap old fields in a "legacy" sub-object and add new fields at top level
```

---

## ADR-003: Event Communication Principles

**Decision**: Events flow in two one-way channels. No cycles allowed.

Channel 1: Logic → Rendering (Systems and Stores publish events, Views subscribe and render)
Channel 2: Input → Logic (Input system translates user actions to events, game logic subscribes)

**Rationale**: Bidirectional event flows create hidden coupling and make debugging nearly impossible. If View A fires an event that triggers System B that fires an event that triggers View A, you have an infinite loop that's hard to trace.

**Test**: Draw the event chain. If any node appears twice, you have a cycle. Redesign.

---

## ADR-004: Store Layering

**Decision**: State is organized in three layers with different lifetimes:

| Store | Lifetime | Persisted? | Example |
|---|---|---|---|
| Battle Store | Single battle | No | Hand cards, current power, play chances |
| Run Store | Single run (multiple battles) | No | Deck composition, gold, map position |
| Meta Store | Cross-run (permanent) | Yes (localStorage) | Formation upgrades, achievements, unlocks |

**Rationale**: Mixing lifetimes in a single store leads to bugs where battle state leaks into the next battle, or run state persists when it shouldn't.

**Rule**: Every new state field must declare which store it belongs to and why.

---

## ADR-005: Config-Driven Design

**Decision**: All numeric values (damage, thresholds, costs, probabilities) live in external JSON config files. Systems read from config at initialization; they never hardcode values.

**Rationale**: Balance tuning is the most frequent change in game development. If a designer needs a programmer to change a number, that's a broken workflow. External configs enable:
- Designers editing values directly
- A/B testing different configs
- Hot-reload without recompilation (future)

**Exception**: Structural constants (like "a deck has 52 cards") can be hardcoded if they're genuinely invariant.

---

## ADR-006: Bootstrap Chain Injection

**Decision**: All systems are created in a specific order in bootstrap.ts. Each system declares its dependencies, and the bootstrap chain creates them in dependency order, injecting dependencies via constructor.

**Rationale**: No implicit singletons, no global state, no service locators. Every dependency is explicit in the constructor signature. This makes testing trivial (inject mocks) and makes dependency cycles impossible (the chain won't compile).

**Rule for new modules**: When adding a new system, declare where it fits in the bootstrap chain and what it depends on. If it depends on a system created later in the chain, that's a design problem — restructure to break the cycle.

---

## ADR-007: Contract-Implementation Separation

**Decision**: The architecture contract document is a living document that evolves with each module. Implementation specifications (like "MVP 1.1 Tech Spec") are frozen after completion and never modified.

**Rationale**: If every new module modifies the original spec, it becomes unclear what was the original design versus what was added later. The architecture contract is the single source of truth for current interfaces; the implementation specs are historical records.

**Flow**:
1. New module reads the architecture contract (latest version)
2. New module proposes changes via Interface Change Declaration
3. Changes are reviewed for backward compatibility
4. Architecture contract is updated with new version number
5. Original module specs remain untouched
