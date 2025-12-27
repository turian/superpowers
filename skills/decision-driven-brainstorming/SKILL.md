---
name: decision-driven-brainstorming
description: Use when you want brainstorming in batches instead of one-question-per-turn during early requirements or design shaping
---

# Decision-Driven Brainstorming (Batch Mode)

## Overview
- Cut "annoying" back-and-forth by batching decisions with recommended defaults while keeping accuracy.
- Maintain a persistent decisions file plus final design doc; defaults stay **provisional until accepted**.
- Announces at start and hands off to superpowers:writing-plans; no code generation or implementation planning inside this skill.

## When to Use
- Early requirements/design shaping with many coupled choices (APIs, auth, data models, UX modes).
- User wants batch mode, defaults, or fewer turns; brainstorming would otherwise be multi-message.
- Avoid if a validated design already exists or only tiny tweaks are needed (go directly to writing-plans/executing-plans).

## Announce at Start
"I'm using the decision-driven-brainstorming skill to reduce turns by proposing decision points with recommended defaults."

## Topic and File Paths
- Propose a derived slug (kebab-case from user text). Say: "I'll use `<slug>` (edit if you want)." Wait for explicit acceptance before writing files.
- Date: `YYYY-MM-DD` (today).
- Decisions file: `docs/plans/YYYY-MM-DD-<topic>-decisions.md`
- Design doc: `docs/plans/YYYY-MM-DD-<topic>-design.md`
- Decisions file is the source of truth; the design doc must not contradict it.

## Decisions File Structure (Hybrid)
- **Header:** Topic | Date | Status (Draft | In Progress | Finalized).
- **Basic Spec** (rewrite each cycle to current understanding): Goal (1–2 sentences), Non-goals, Constraints, Success criteria, Assumptions, Open questions.
- **Current Decisions** (rewrite summary table each cycle): ID | Title | Status (proposed | accepted-default | decided) | Choice | Notes.
- **Decision Log** (append-only):
  - `### D-001: <Title>`
  - `- Status: proposed | accepted-default | decided`
  - `- Chosen: <Option>` (for accepted-default/decided)
  - `- Rationale:` 1–3 bullets
  - `- Timestamp:` optional
- **Backlog/Deferred:** list unresolved/non-critical items with IDs to keep them visible.

## Status Semantics
- **proposed:** presented; waiting for user selection.
- **accepted-default:** user explicitly accepted the recommended default or said "accept defaults"/"proceed with defaults" for unresolved items.
- **decided:** user chose a non-default option or overrode a default.
- Defaults are never auto-upgraded to decided without explicit acceptance.

## Cycle Workflow (Batch Mode)
1) Confirm topic slug and capture Basic Spec seed from the user.
2) Refresh Basic Spec and Current Decisions table before proposing new options.
3) Rank decision candidates (default 5, max 10) using the heuristic below; assign stable IDs (D-001...).
4) Present iteration output:
   - **Current understanding:** 3–6 bullets.
   - **Top Decision Points (ranked):** ID, why it matters (impact/risk/coupling), options A/B/C (2–4 options), recommended default labeled "provisional until accepted," and "If you choose differently, what changes." Include score tags if helpful.
   - **Compact response instructions:** "Accept all defaults" or "D-002=B, D-004=C with note: ...".
   - **Remaining critical decisions count:** show how many high-impact items stay unresolved.
5) **STOP.** Wait for user input; do **not** append to the file yet.
6) On response:
   - "Accept all defaults" → append those IDs as accepted-default with the recommended option; update summary/backlog.
   - Specific overrides → mark those as decided; leave others proposed/backlog; present next ranked set.
   - "Proceed with defaults for unresolved" → convert remaining proposed to accepted-default.
7) Repeat until no critical/high-risk items remain or the user stops. Keep defaults provisional until explicitly accepted.
8) Update decisions file status: Draft (collecting seed), In Progress (mid-iteration), Finalized (after design doc unless user keeps it open).

## Decision Ranking Heuristic
Score each candidate on: user-visible impact, irreversibility/migration cost, security/compliance risk, public interface commitment (API/CLI/data format), and engineering coupling. Prioritize high-impact + high-irreversibility. Always surface security/auth, data format/persistence, and public interface decisions early.

## Defaults Policy (Critical Constraint)
- Recommended defaults are allowed to save turns, but they remain **provisional until accepted**.
- Never treat a default as decided without explicit acceptance.
- Before the final design, confirm either: (a) user accepted defaults, (b) user chose options, or (c) user said "proceed with defaults for unresolved items."
- Silence or non-response is not acceptance; do not proceed. Any decision ID not mentioned stays proposed unless the user says "accept defaults" or "proceed with defaults for unresolved items."

## Design Document (Final Output)
- Create after critical decisions are accepted or the user says to proceed.
- Path: `docs/plans/YYYY-MM-DD-<topic>-design.md`
- Reference the decisions file near the top as the binding source of truth.
- Include: context/goals/non-goals, architecture/components, data flow & storage, interfaces/public contracts, error handling & edge cases, testing approach (unit/integration/acceptance), dependencies/risks/assumptions, and **Deferred/TBD decisions** (with IDs).
- Do not introduce new requirements not present in the decisions file unless explicitly labeled "proposal/TBD".
- Close by asking: "Proceed to implementation planning via superpowers:writing-plans?"

## Turn-Reduction Guardrails
- Batch questions into ranked decision sets; avoid one-question-per-turn.
- Keep options concise and multiple-choice.
- Show remaining critical decision count each cycle; recommend stopping when it reaches zero.

## Compliance Checklist
- Announce skill usage.
- Confirm topic slug before writing files.
- Maintain hybrid decisions file (current summary + append-only log) with stable IDs.
- Label defaults as provisional until accepted; never silently decide.
- Pause after presenting options; wait for explicit user choices.
- Produce design doc aligned to the decisions file and list deferred items.
- Offer handoff to superpowers:writing-plans.
