# P014-Review: 修复 QUIC/UDP 管控 AND 规则 — 位置重排 + GEOIP 替换为 RULE-SET

## Review 结论：PASS

## 逐项验证

| # | 验收标准 | 结果 | 备注 |
|---|---|---|---|
| 1 | AND 规则移至 TrackingIP 下方 | ✅ | 第 180 行，紧接 TrackingIP（179 行） |
| 2 | `GEOIP,CN,no-resolve` → `RULE-SET,ChinaIP,no-resolve` | ✅ | 替换正确，保留了 `no-resolve` |
| 3 | `# 7. QUIC / UDP 443 管控` 注释已删除 | ✅ | rules 段无残留 |
| 4 | 其他规则顺序和内容不变 | ✅ | #3-#6、#8-#10 均未改动 |
| 5 | YAML 语法验证通过 | ✅ | `yaml.safe_load` 正常 |
| 6 | GEOIP 无残留 | ✅ | `grep -c 'GEOIP'` 返回 0 |

## 验证命令输出

```bash
# YAML 语法
→ YAML OK

# GEOIP 残留
→ 0

# AND 规则位置
第 180 行，紧贴 TrackingIP（第 179 行）
```

## diff 确认

```diff
   - RULE-SET,TrackingIP,Tracking,no-resolve
+  - AND,((DST-PORT,443),(NETWORK,UDP),(NOT,((RULE-SET,ChinaIP,no-resolve)))),Tracking
...
-  # 7. QUIC / UDP 443 管控
-  - AND,((DST-PORT,443),(NETWORK,UDP),(NOT,((GEOIP,CN,no-resolve)))),Tracking
```

+1 行，-3 行，改动干净。
