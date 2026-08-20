# DESIGN CONFLICT PROTOCOL — PharmOS

Status: ACTIVE

## Purpose

Design Battalion must understand why current PharmOS UI exists before changing it. It must distinguish user-originated requirements/problems, PharmOS implementation decisions, architecture/safety invariants, historical accretion, and Design's own proposals.

## Provenance classes

- U-E: exact user-originated statement/problem verified.
- U-S: user requirement preserved only as a summarized handoff/decision record.
- P: PharmOS implementation/product decision.
- A: architecture/security/state-transition invariant.
- H: historical accretion/technical debt.
- D: Design Battalion independent proposal.
- X: external HCI/domain/product evidence.
- ?: unresolved provenance; never attribute to user.

## Burden of proof

Evidence priority:
1. actual user behavior/failure/explicit requirement
2. workflow, safety, state transition, recovery outcomes
3. PharmOS/Design doctrine
4. HCI/human-factors/domain research
5. validated external product cases
6. visual trend/preference

Design may not win a conflict by saying only 'modern apps do this.' PharmOS may not win by saying only 'this is how it has always been.'

## Intent vs implementation

Functional intent and implementation shape must be separated.

Example: mobile workboard.
- Current code repeatedly tuned a compact 3x3 board from V0.12.2 through V0.12.6 and marks the last rule `FINAL: true compact 3 x 3 board`.
- Current code also declares an UX invariant that the selected task action panel belongs immediately after that task row, not at the top of the section or on a separate page/dialog.
- Historical handoff documents preserve `3x3 기본, 3x∞ 슬라이드` as a prior requirement, but an attributable verbatim user statement is not currently available.
- The same handoff also records BUG-010 mobile card density: too many columns may make text unreadable, and the user-facing UX direction includes large text / no microscopic mobile text.

Therefore:
- Fast one-screen scanning is a protected intent.
- 3x3 is a strong PharmOS implementation decision, not automatically an immutable architecture invariant.
- Readability is also a protected requirement.
- Design must compare current 3x3, improved 3x3, and adaptive alternatives before replacing it.
- Prototype v2's immediate 3x3 -> 1xN change is not sufficiently evidenced and is treated as an experiment, not a final recommendation.

## User modernization mandate

Verified prior conversation context records that the user considers PharmOS visually dated and wants consumer-app-caliber modernization, with colors/fonts/animation and other causes analyzed rather than assumed. The user also approved backend/backbone-preserving frontend modernization and separate PC/mobile improvements.

This supports redesigning presentation. It does not authorize arbitrary navy/teal palettes, radii, shadows, or layouts without separate evidence.

## Design push / compromise / retreat

Design should PUSH when the current implementation creates a verified user problem and an alternative preserves functionality while producing stronger task-time/readability/error/recovery evidence.

Design should COMPROMISE when PharmOS presents real state-transition, security, audit, domain, or architecture constraints, or when competing user goals both matter (e.g. high density and large text).

Design should RETREAT when a change weakens permission, audit, recovery, data integrity, or removes capabilities, or when the only support is visual fashion.

## Required fields before every new design change

1. Current behavior
2. User problem/requirement provenance
3. PharmOS rationale
4. Architecture/security invariant
5. Design objection
6. Proposed change
7. Evidence and expected outcome
8. Validation metric and rollback condition

If a material provenance field is unresolved, the change is EXPERIMENT, not FINAL.
