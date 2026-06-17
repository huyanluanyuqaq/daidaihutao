# P010-Review: ProMax.yaml 与 Pro.yaml 配置同步及规则排序统一

## Review 结论：PASS（修复后）

### 第一次审查（FAIL）

**问题：** ProMax.yaml 和 Pro.yaml 存在重复 `rules:` 头部。

**修复 Agent：** MiMoCode 删除两处多余行（各 2 行）。

### 第二次审查（PASS）

全部验收通过：

| # | 验收标准 | 结果 |
|---|---|---|
| 1 | ProMax.yaml Embydirect proxies 为 `*direct` | ✅ |
| 2 | ProMax.yaml rules 按 10 组标准顺序排列 | ✅ |
| 3 | ProMax.yaml rules 条数不变（33 条） | ✅ |
| 4 | Pro.yaml proxy-groups 无 Youtube | ✅ |
| 5 | Pro.yaml rules 按目标顺序排列，AI/AiIP/Dev 已补回 | ✅ |
| 6 | Pro.yaml rules 条数不变（20 条） | ✅ |
| 7 | YAML 语法正确 | ✅ |
| 8 | 无重复 `rules:` 键 | ✅ |
| 9 | Standard.yaml 不变（7 条） | ✅ |

## 逐项验证

| # | 验收标准 | 结果 | 备注 |
|---|---|---|---|
| 1 | ProMax.yaml Embydirect proxies 为 `*direct` | ✅ | `[DIRECT, *us-first]` → `*direct` |
| 2 | ProMax.yaml rules 按 10 组标准顺序排列 | ✅ | 顺序正确 |
| 3 | ProMax.yaml rules 条数不变 | ✅ | 33 条 |
| 4 | Pro.yaml proxy-groups 无 Youtube | ✅ | 已删除 |
| 5 | Pro.yaml rules 按目标顺序排列 | ✅ | AI/AiIP/Dev 已补回 |
| 6 | Pro.yaml rules 条数不变 | ✅ | 20 条 |
| **7** | **YAML 语法正确** | **❌** | **重复 `rules:` 头部** |
| 8 | Standard.yaml 不变 | ✅ | 未修改 |

## 问题详情

### F1 [严重] ProMax.yaml 多余 `rules:` 头部

**位置**：`mihomo/config/ProMax.yaml` 第 161-164 行

```yaml
# 规则路由
rules:                   # ← 多余的
# 规则路由
rules:                   # ← 保留这个
  # 1. 本地/私有
```

### F2 [严重] Pro.yaml 多余 `rules:` 头部

**位置**：`mihomo/config/Pro.yaml` 第 135-138 行

```yaml
# 规则路由
rules:                   # ← 多余的
# 规则路由
rules:                   # ← 保留这个
  # 1. 本地/私有
```

### 影响

- PyYAML 静默取最后一个 `rules:` 键，文件在 **当前版本 mihomo** 可以工作
- 但 **严格 YAML 解析器** 会拒绝重复键
- 代码质量缺陷，需要修复

## 修复方案

### ProMax.yaml：删除第 161-162 行

删除 `# 规则路由` 和 `rules:` 这两个多余的头部行。

### Pro.yaml：删除第 135-136 行

删除 `# 规则路由` 和 `rules:` 这两个多余的头部行。

## 指定修复 Agent

**Codex** — 删除两处重复头部行，提交修复