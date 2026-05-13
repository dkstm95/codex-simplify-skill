---
name: simplify
description: |
  Review changed code for reuse, quality, and efficiency, then apply high-confidence behavior-preserving simplifications. Use when the user asks to simplify code, review recent changes for reuse/quality/efficiency, reduce duplication, remove unnecessary complexity, or improve memory/runtime efficiency without changing behavior.
---

# simplify

Review changed code through reuse, quality, and efficiency lenses, then directly fix only high-confidence issues that preserve behavior.

This is not a generic polish pass. Start from the changed files or the user-specified focus, combine concrete findings from all three lenses, and make safe code changes when implementation is requested.

## Scope

- Run `git status --short` first when working inside a Git repository.
- If there are staged changes, inspect `git diff HEAD`; otherwise inspect `git diff`.
- If the user asks for a current PR, current branch, or committed changes and the working diff is empty, inspect the PR or branch diff instead. Prefer `gh pr diff` when a PR is available; otherwise compare against the merge base with the repository's base branch, for example `git diff origin/main...HEAD`.
- If there is no working, staged, PR, or branch diff, review recently mentioned files or files Codex just edited in this conversation.
- If the user supplied a focus or path, prioritize it. Examples: memory efficiency, a file path, a module, or a specific changed behavior.
- Follow repository instructions such as `AGENTS.md`, closer nested instructions, and observed local code patterns.
- Exclude generated code, vendored code, external dependency caches, and mechanical lockfile churn unless the user explicitly includes them.

## Subagent Model

When subagent tools are available, use them only if the user explicitly asks for subagents, delegation, or parallel agent work.

If subagents are requested, launch three independent review-only subagents in parallel:

- `Code Reuse Review`
- `Code Quality Review`
- `Efficiency Review`

Give each subagent the same scope, diff summary, relevant paths, and user focus. Ask each one for concrete findings only: file/line, issue, why it matters, and the smallest behavior-preserving fix candidate.

Do the final deduplication, risk judgment, edits, and verification locally in the main Codex session unless the user explicitly requested code-editing subagents and disjoint ownership is clear.

If subagents are not allowed or not requested, perform the same three reviews locally as separate passes. Do not imply that subagents were used.

## Review Lenses

### 1. Code Reuse Review

- Look for newly written code that can use existing utilities, helpers, or shared modules.
- Search nearby files, utility directories, shared modules, and tests for similar patterns before introducing new abstractions.
- Flag reimplemented helpers, hand-rolled string/path/env/type-guard logic, and ad hoc conversions when an established local API already exists.
- Fix only when reusing the existing abstraction clearly simplifies the changed code without changing behavior.

### 2. Code Quality Review

- Reduce redundant state that can be derived from existing state, props, data, or store values.
- Remove unnecessary observer/effect layers, duplicate caches, and no-op update paths.
- Avoid parameter sprawl by improving call structure when a new parameter makes an interface harder to reason about.
- Consolidate copy-paste blocks with slight variation only when a local abstraction makes the code clearer.
- Repair leaky abstractions that expose internal details or cross existing ownership boundaries.
- Replace stringly typed code with existing constants, enums, string unions, branded types, or route helpers.
- Remove JSX/widget wrappers that add no layout, semantics, state boundary, or accessibility value.
- Flatten nested ternaries and 3+ level conditionals with guard clauses, lookup tables, or clear `if` / `else if` cascades.
- Remove comments that only explain what the code does, narrate a change, or mention a task/caller. Keep short comments only for hidden constraints or subtle invariants.

### 3. Efficiency Review

- Find redundant computation, repeated file reads, duplicate API/network calls, and N+1 patterns introduced or worsened by the change.
- Parallelize independent work only when the surrounding code style and error semantics support it.
- Check startup, request, render, and event hot paths for newly added blocking work.
- Add change-detection guards for polling, intervals, listeners, reducers, and store updates that repeatedly emit no-op changes.
- Preserve same-reference or "no change" signals from updater/reducer callbacks when wrappers are involved.
- Prefer operation plus error handling over file/resource existence pre-checks that introduce TOCTOU races.
- Look for unbounded data structures, missing cleanup, event listener leaks, and overly broad reads or loads.

## Fix Criteria

- Preserve behavior, outputs, error handling, permissions, transaction boundaries, and intended performance characteristics.
- Make only high-confidence, behavior-preserving fixes.
- Skip false positives and findings whose risk outweighs their value; mention them briefly only when useful.
- Do not create clever one-liners, broad abstractions, or function merges just to reduce line count.
- Do not remove meaningful domain abstractions, invariant-protecting types, or clear layer boundaries just because they are verbose.
- Before renaming or changing shared functions, types, routes, exports, or public strings, search direct references, type references, string literals, dynamic imports/requires, re-exports, tests, and mocks.

## Workflow

1. Determine the changed scope and user focus.
2. Collect findings across the three review lenses, using subagents only when explicitly allowed by the user and current Codex policy.
3. Merge and deduplicate findings.
4. Apply only behavior-preserving fixes.
5. Read the final diff to catch accidental behavior changes.
6. Run the closest relevant verification: formatter, lint, type check, unit/integration test, or targeted test for the touched area. If no verifier exists, say so.

## Reporting

- Report briefly in the user's language unless they ask for another language.
- Include what was simplified, what verification ran, and any remaining risk.
- Do not say the work is complete until the verification output has been inspected.
