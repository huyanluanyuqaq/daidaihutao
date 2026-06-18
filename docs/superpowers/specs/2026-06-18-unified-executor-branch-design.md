# Unified Executor Branch Design

**Date:** 2026-06-18
**Status:** Draft — awaiting user review

## Goal

Replace three agent-specific branches (`codex/automate`, `mimo/work`, `deveco/execute`) with a single generic `executor/work` branch. Any agent (Codex, MiMoCode, DeveCo, or future tools) can pull this branch to execute tasks. Claude retains `claude/review` for planning and review only.

## Branch Structure

```
Branches:
  main              — only trusted baseline
  claude/review     — Claude only, modifies docs/ and spec files
  executor/work     — shared executor branch, all agents pull from here

Deleted:
  codex/automate
  mimo/work
  deveco/execute
```

## Role Definitions

| Agent      | Branch          | Allowed Directories                          | Responsibility          |
|------------|-----------------|----------------------------------------------|-------------------------|
| Claude     | claude/review   | `docs/`, `PROJECT_LESSONS.md`, `PROJECT-SPEC.md` | Planning, task spec, review, archival |
| Executor   | executor/work   | Everything except `docs/`, `PROJECT_LESSONS.md`, `PROJECT-SPEC.md` | Execute task documents |

Any agent acting as Executor shares the same branch permission and directory permission.

## Workflow Changes

### Phase 1: Claude Planning (unchanged)

```
git checkout main
git fetch origin
git pull --ff-only origin main
git checkout claude/review
git rebase main
→ Create task doc: docs/tasks/Pxxx.md
→ git commit -m "[Claude][Pxxx] add task specification"
→ git push origin claude/review
→ git checkout main
→ git merge --ff-only claude/review
→ git push origin main
→ Wait for instruction
```

### Phase 2: Executor Execution (simplified)

```
git checkout main
git fetch origin
git pull --ff-only origin main
git checkout executor/work
git rebase main
→ Read docs/tasks/Pxxx.md
→ Execute modifications strictly per task document
→ git commit -m "[<AgentName>][Pxxx] implement changes"
→ git push origin executor/work
→ git checkout main
→ git merge --ff-only executor/work
→ git push origin main
→ Silent: "Phase 2 complete, merged to main. Waiting for Claude Review."
```

### Phase 3: Claude Review (unchanged)

```
git checkout main
git fetch origin
git pull --ff-only origin main
git checkout claude/review
git rebase main
→ Create review doc: docs/reviews/Pxxx-review.md
→ git commit -m "[Claude][Pxxx] review pass" or "review fail"
→ git push origin claude/review
→ git checkout main
→ git merge --ff-only claude/review
→ git push origin main
```

### PASS → Archive (unchanged)

```
git checkout main
git fetch origin
git pull --ff-only origin main
git checkout claude/review
git rebase main
→ git mv docs/tasks/Pxxx.md docs/archive/
→ git mv docs/reviews/Pxxx-review.md docs/archive/
→ git commit -m "[Claude][Pxxx] archive completed task"
→ git push origin claude/review
→ git checkout main
→ git merge --ff-only claude/review
→ git push origin main
```

### FAIL → Fix Loop (same as Phase 2)

```
Executor re-pulls executor/work, rebases on main, applies fixes:
git checkout main
git fetch origin
git pull --ff-only origin main
git checkout executor/work
git rebase main
→ Apply fixes per review
→ git commit -m "[<AgentName>][Pxxx] fix review issues"
→ git push origin executor/work
→ git checkout main
→ git merge --ff-only executor/work
→ git push origin main
→ Claude re-reviews
```

## Commit Prefixes

| Purpose              | Agent    | Format                                      |
|----------------------|----------|---------------------------------------------|
| Task spec            | Claude   | `[Claude][Pxxx] add task specification`     |
| Implement changes    | Any      | `[<AgentName>][Pxxx] implement changes`     |
| Fix review issues    | Any      | `[<AgentName>][Pxxx] fix review issues`     |
| Review pass          | Claude   | `[Claude][Pxxx] review pass`                |
| Review fail          | Claude   | `[Claude][Pxxx] review fail`                |
| Spec update          | Claude   | `[Claude][Pxxx] update project specification` |
| Archive              | Claude   | `[Claude][Pxxx] archive completed task`     |
| Clarification        | Any      | `[Qxxx] request clarification`              |

## Remote Operations

1. Merge local `codex/automate` → `executor/work`
2. Merge local `mimo/work` → `executor/work`
3. Merge local `deveco/execute` → `executor/work`
4. Push `executor/work` to origin
5. Delete remote `codex/automate`
6. Delete remote `mimo/work`
7. Delete remote `deveco/execute`

## Core Rules (unchanged)

1. `main` is the only trusted baseline; work branches must sync from `main`.
2. No direct sync between work branches; all merges go through `main`.
3. Merges to `main` must use `--ff-only`; never force-push `main`.
4. Each phase end must push to remote.
5. Claude only modifies `docs/`, `PROJECT_LESSONS.md`, `PROJECT-SPEC.md`; Executors modify everything else.
6. All commits must use `[Agent][Pxxx]` prefix format.
7. Review conclusions: PASS or FAIL only, with verification checklist.
8. Major ambiguities or high-risk operations → confirm with user.
9. Minimal necessary changes only; no unrelated refactoring.
10. Clear, auditable, reusable output.
11. [Controlled auto-execution] Executors have controlled auto-execution rights in Phase 2.
12. [Error degradation] Executors may only interrupt for: ff-only failure, major ambiguity, directory violation, sensitive info needed, unresolved test/build errors.
13. [No reflective questioning] Executors must not ask "how should I modify better" — all execution follows `docs/tasks/Pxxx.md`.
