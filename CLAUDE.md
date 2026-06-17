# Agent 协作分工

## 角色定位

| Agent | 工作分支 | 允许修改目录 | 职责 |
|---|---|---|---|
| Claude | `claude/review` | `docs/`、`PROJECT_LESSONS.md`、`PROJECT-SPEC.md` | 规范制定、任务拆解、Review 审查、归档 |
| Codex | `codex/automate` | 除 `docs/`、`PROJECT_LESSONS.md`、`PROJECT-SPEC.md` 外的所有项目目录 | 批量修改、配置修复、规则引用更新、测试修复 |
| MiMoCode | `mimo/work` | 除 `docs/`、`PROJECT_LESSONS.md`、`PROJECT-SPEC.md` 外的所有项目目录 | 探索、调试、研究、灵活执行、代码审查 |
| DeveCo | `deveco/execute` | 除 `docs/`、`PROJECT_LESSONS.md`、`PROJECT-SPEC.md` 外的所有项目目录 | 配置重构、批量路径变更、文件重命名、目录结构调整 |

## 完整工作流（含远程推送）

### 阶段一：Claude 规划

```
git checkout main
git fetch origin
git pull --ff-only origin main
git checkout claude/review
git rebase main
→ 创建任务文档 docs/tasks/Pxxx.md
→ git commit -m "[Claude][Pxxx] add task specification"
→ git push origin claude/review
→ git checkout main
→ git merge --ff-only claude/review
→ git push origin main
→ 等待指令
```

### 阶段二：执行 Agent

#### Codex

```
git checkout main
git fetch origin
git pull --ff-only origin main
git checkout codex/automate
git rebase main
→ 读取 docs/tasks/Pxxx.md
→ 严格按任务文档执行修改（无需中途停顿确认）
→ git commit -m "[Codex][Pxxx] implement changes"
→ git push origin codex/automate
→ git checkout main
→ git merge --ff-only codex/automate
→ git push origin main
→ 自动进入静默状态，仅提示用户："阶段二已自动执行完毕，已推送至 main，等待 Claude 进行阶段三 Review。"
```

#### MiMoCode

```
git checkout main
git fetch origin
git pull --ff-only origin main
git checkout mimo/work
git rebase main
→ 读取 docs/tasks/Pxxx.md
→ 严格按任务文档执行修改（无需中途停顿确认）
→ git commit -m "[MiMoCode][Pxxx] implement changes"
→ git push origin mimo/work
→ git checkout main
→ git merge --ff-only mimo/work
→ git push origin main
→ 自动进入静默状态，仅提示用户："阶段二已自动执行完毕，已推送至 main，等待 Claude 进行阶段三 Review。"
```

#### DeveCo

```
git checkout main
git fetch origin
git pull --ff-only origin main
git checkout deveco/execute
git rebase main
→ 读取 docs/tasks/Pxxx.md
→ 严格按任务文档执行修改（无需中途停顿确认）
→ git commit -m "[DeveCo][Pxxx] implement changes"
→ git push origin deveco/execute
→ git checkout main
→ git merge --ff-only deveco/execute
→ git push origin main
→ 自动进入静默状态，仅提示用户："阶段二已自动执行完毕，已推送至 main，等待 Claude 进行阶段三 Review。"
```

### 阶段三：Claude Review

#### 创建并合并 Review

```
git checkout main
git fetch origin
git pull --ff-only origin main
git checkout claude/review
git rebase main
→ 创建 docs/reviews/Pxxx-review.md
→ git commit -m "[Claude][Pxxx] review pass" 或 "review fail"
→ git push origin claude/review
→ git checkout main
→ git merge --ff-only claude/review
→ git push origin main
```

#### PASS → 归档

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

#### FAIL → 修复循环

```
FAIL 包含：发现的问题、影响范围、修复建议、指定修复 Agent（Codex / MiMoCode / DeveCo）

指定 Agent 拉取最新 main、修复、合并回 main：
git checkout main
git fetch origin
git pull --ff-only origin main
git checkout <工作分支>
git rebase main
→ 根据 Review 修复
→ git add .
→ git commit -m "[Agent][Pxxx] fix review issues"
→ git push origin <工作分支>
→ git checkout main
→ git merge --ff-only <工作分支>
→ git push origin main
→ Claude 重新 Review

循环模型：
Task → Agent 执行 → Claude Review → FAIL → Agent 修复 → Claude Review → ... → PASS → 归档
```

## 提交前缀

| 用途 | Agent | 格式 |
|---|---|---|
| 任务文档 | Claude | `[Claude][Pxxx] add task specification` |
| 执行修改 | Codex | `[Codex][Pxxx] implement changes` |
| 执行修改 | MiMoCode | `[MiMoCode][Pxxx] implement changes` |
| 执行修改 | DeveCo | `[DeveCo][Pxxx] implement changes` |
| 修复 Review 问题 | Codex | `[Codex][Pxxx] fix review issues` |
| 修复 Review 问题 | MiMoCode | `[MiMoCode][Pxxx] fix review issues` |
| 修复 Review 问题 | DeveCo | `[DeveCo][Pxxx] fix review issues` |
| Review 通过 | Claude | `[Claude][Pxxx] review pass` |
| Review 失败 | Claude | `[Claude][Pxxx] review fail` |
| 规范更新 | Claude | `[Claude][Pxxx] update project specification` |
| 归档 | Claude | `[Claude][Pxxx] archive completed task` |
| 歧义问题 | 任何 Agent | `[Qxxx] request clarification` |

## 推送前规范一致性审查

**每次合并到 `main` 后、推送 `main` 到 `origin/main` 之前**，Claude 必须审查以下三份文档是否与当前项目最新代码一致：

### 1. PROJECT-SPEC.md

- 任务涉及的配置路径变更是否已反映在规范中
- Agent 分工描述（允许修改/限制目录）是否仍然准确
- 文档结构是否与实际 `docs/` 目录一致
- 项目结构（目录树）是否与实际一致
- 分支模型描述是否仍然适用
- `.gitignore` 等基础设施变更是否已同步到规范

### 2. PROJECT_LESSONS.md

- 本次任务的复盘结果是否已记录
- 已有经验教训是否仍准确有效

### 3. README.md

- 使用说明、项目介绍、配置示例等是否与实际一致

如发现过时或不一致内容，Claude 应在 `claude/review` 分支更新对应文档，然后重新合并到 `main`，再推送：

```bash
git checkout claude/review
git rebase main

# 更新文档
git add <文件路径>
git commit -m "[Claude][Pxxx] update <文件名> before push"

git checkout main
git merge --ff-only claude/review
git push origin main
```

未经此审查不得推送 `main` 到 `origin/main`。

## 核心规则

1. `main` 是唯一可信基线，工作分支必须从 `main` 同步。
2. 四个工作分支之间禁止直接同步，所有合并必须经过 `main`。
3. 合并回 `main` 必须使用 `--ff-only`，不得强制推送 `main`。
4. 每个阶段结束必须推送远程分支，确保工作进度不丢失。
5. Claude 仅修改 `docs/` 目录及 `PROJECT_LESSONS.md`、`PROJECT-SPEC.md`；执行 Agent（Codex/MiMoCode/DeveCo）不得修改上述范围。其他变更通过任务文档走执行 Agent 流程。
6. 所有 commit 必须使用 `[Agent][Pxxx]` 格式前缀（歧义问题使用 `[Qxxx]`）。
7. Review 结论只能是 PASS 或 FAIL，必须附带逐项验证清单。
8. 如存在重大歧义或高风险操作，向用户确认。
9. 优先做最小必要修改，不做无关重构。
10. 所有输出尽量清晰、可复查、可复用。
11. 【受控全自动授权】执行 Agent（Codex/MiMoCode/DeveCo）在阶段二拥有受控自动执行权。只要不违反目录权限、分支规则、`--ff-only` 合并规则和任务文档要求，应从读取任务、修改代码、测试验证、commit、push 到合并回 main 连续自动执行，不得中途询问用户。
12. 【错误降级】阶段二只有在以下情况允许中断并向用户求助：`git merge --ff-only` 失败、任务文档存在重大歧义、操作会越过允许修改目录、需要访问敏感信息或付费服务、测试/构建错误经合理修复后仍无法解决。
13. 【禁止反思提问】执行 Agent 不得就"如何修改更好""是否确认提交""是否确认推送"向用户提问。一切以 `docs/tasks/Pxxx.md` 为最高执行依据。