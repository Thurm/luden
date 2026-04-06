---
name: game-design-audit
description: |
  Auto-audit hook that verifies Claude Code's implementation against the game module design spec produced by game-module-architect. Triggers automatically when Claude finishes a coding response. Checks data models, interfaces, events, state machines, and config schemas against the design document, reporting PASS/DRIFT/VIOLATION for each item.
context: fork
hooks:
  Stop:
    - type: prompt
      prompt: |
        Check if the current task involves implementing a game module that has a design spec.
        Look for files matching: module_*_design.md or *_design.md in the project.
        If no design spec exists, respond YES (skip audit).
        If a design spec exists and the last response included code changes, respond NO (trigger audit).
      on_no:
        - type: agent
          agent: |
            You are a design-spec compliance auditor. Your job is to verify that the code
            Claude just wrote matches the design specification.

            ## Step 1: Find the design spec
            Search for files matching `module_*_design.md` or `*_design.md` in the project.
            Read the most recently modified one.

            ## Step 2: Find the code that was just written/modified
            Use `git diff HEAD` to see what changed. If no git, check recently modified files.

            ## Step 3: Audit each category

            ### 3a. Data Models
            Compare every type/interface/struct in the design spec against the implementation.
            - Field names must match exactly
            - Field types must be compatible
            - Required fields must be present

            ### 3b. Interface Signatures
            Compare public method signatures:
            - Method names must match
            - Parameter types must be compatible
            - Return types must match

            ### 3c. Event Names & Payloads
            Compare event definitions:
            - Event name strings must match exactly
            - Payload structures must be compatible

            ### 3d. State Machine Transitions
            Compare state definitions and valid transitions:
            - All states from spec must exist
            - Transition triggers must match

            ### 3e. Config Schemas
            Compare config file structures:
            - All config fields from spec must exist
            - Types must match

            ## Step 4: Output Report

            Format:
            ```
            ## 🔍 Design Spec Audit Report

            **Spec file**: [path]
            **Code scope**: [files checked]

            | Category | Status | Details |
            |----------|--------|---------|
            | Data Models | ✅ PASS / ⚠️ DRIFT / ❌ VIOLATION | [specifics] |
            | Interfaces | ✅ PASS / ⚠️ DRIFT / ❌ VIOLATION | [specifics] |
            | Events | ✅ PASS / ⚠️ DRIFT / ❌ VIOLATION | [specifics] |
            | State Machines | ✅ PASS / ⚠️ DRIFT / ❌ VIOLATION | [specifics] |
            | Config Schemas | ✅ PASS / ⚠️ DRIFT / ❌ VIOLATION | [specifics] |

            **Overall**: [PASS / NEEDS ATTENTION]
            ```

            Status definitions:
            - ✅ PASS: Implementation matches spec exactly
            - ⚠️ DRIFT: Implementation extends spec (additive, non-breaking) — acceptable but flag it
            - ❌ VIOLATION: Implementation contradicts spec (missing fields, wrong types, missing states) — must fix

            If overall status is not PASS, list specific items that need correction.
---

# Game Design Audit Hook

This skill provides **automatic compliance auditing** for game module implementations.

## How it works

1. **Trigger**: Fires automatically every time Claude Code finishes responding (Stop event)
2. **Gate check**: A lightweight prompt first checks if there's a design spec AND code was just written — if not, the audit is skipped (zero overhead for non-implementation tasks)
3. **Audit agent**: If triggered, spawns a subagent that reads the design spec and compares it against the code diff
4. **Report**: Outputs a structured audit report with PASS/DRIFT/VIOLATION per category

## When does it fire?

| Scenario | Fires? |
|---|---|
| User asks Claude to implement a game module with a `*_design.md` present | ✅ Yes |
| User asks Claude to refactor existing game code with a design spec present | ✅ Yes |
| User asks a question about the codebase (no code written) | ❌ No (gate prompt skips) |
| User works on non-game code, no design spec in project | ❌ No (gate prompt skips) |

## Audit categories

| Category | What it checks |
|---|---|
| **Data Models** | Field names, types, required fields match spec |
| **Interfaces** | Method names, parameter types, return types |
| **Events** | Event name strings, payload structures |
| **State Machines** | States exist, transitions match spec |
| **Config Schemas** | Config fields and types present |

## Status levels

- **✅ PASS**: Implementation matches spec exactly — no action needed
- **⚠️ DRIFT**: Implementation adds things not in spec (e.g., extra helper fields) — acceptable, but the team should know
- **❌ VIOLATION**: Implementation contradicts spec — must fix before merging

## Dependencies

- Requires a design spec file (produced by `game-module-architect`) in the project
- Uses `git diff` for change detection (falls back to file modification time if not a git repo)
- No external API calls — everything runs locally in Claude Code's context
