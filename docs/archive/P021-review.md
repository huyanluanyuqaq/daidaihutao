# P021 Review — ProMax.yaml rules 段分组顺序重组

## 结论：PASS

验收通过，附带一个编号修复（已同步修正）。

---

## 逐项验证清单

### 1. 规则移动检查

| 规则 | 源位置 | 目标位置 | 验证 |
|---|---|---|---|
| `Cusproxy` | 第 3 节（AI/Telegram） | 第 7 节（自定义） | ✓ |
| `Public-tracker` | 原第 8 节（国外流量） | 第 7 节（自定义） | ✓ |
| `Cloudflare` | 第 3 节（AI/Telegram） | 第 6 节（大厂服务，Apple/Microsoft 后） | ✓ |

### 2. 分组顺序检查

| # | 分组 | 验证 |
|---|---|---|
| 3 | 高优先级特定服务（Ai, Telegram, Emby 等） | ✓ 不变 |
| 4 | 专属流媒体与社交（Youtube, Streaming, Socialmedia） | ✓ 不变 |
| 5 | 特殊垂直领域（Crypto, Paypal, Dev, Speedtest） | ✓ 不变 |
| 6 | 大厂特定服务（Google, Apple, Microsoft, Cloudflare） | ✓ Cloudflare 移入 |
| 7 | 自定义（Public-tracker, Cusproxy） | ✓ 新增分组 |
| 8 | 通用国外流量（Proxy, ProxyIP） | ✓ 从 #9 提前 |
| 9 | 通用国内流量（China, ChinaIP） | ✓ 从 #7 推后 |
| 10 | 兜底规则（MATCH） | ✓ |

### 3. 规则内容不变性检查

- 所有 `RULE-SET` 引用路径不变 ✓
- 所有目标策略组（`Proxy` / `China` / `REJECT` 等）不变 ✓
- `MATCH,Final` 兜底规则位置不变 ✓

### 4. YAML 语法检查

- 缩进一致，空行分隔逻辑分组 ✓
- 注释格式统一 ✓

### 5. Review 过程中发现的修复

**编号错位修复**（已同步修正）：

| 修改前 | 修改后 |
|---|---|
| `# 8. 自定义` | `# 7. 自定义` |
| `# 8. 通用国外流量` | `# 8. 通用国外流量` |
| `# 7. 通用国内流量` | `# 9. 通用国内流量` |
| `# 9. 兜底规则` | `# 10. 兜底规则` |

节编号由 7/8/8/7/9 调整为 7/8/9/10，保持连续 @@ 匹配。

---

## 总评

改动清晰合理，仅涉及规则分组顺序和注释优化，无规则内容或策略变更。编号不一致问题已修复。**PASS。**
