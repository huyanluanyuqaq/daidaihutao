# PROJECT-SPEC.md

## 项目简介

基于 666OS/YYDS 结构部署的 Clash/Mihomo 配置仓库。配置文件手动维护，规则文件由 P003_rules-builder 外部推送，自编译已移除。

## 项目结构

```
├── JS/                   脚本
├── icon/                 图标
├── mihomo/
│   ├── config/           配置文件（手动维护）
│   │   ├── Pro.yaml      专业版
│   │   ├── ProMax.yaml   专业增强版
│   │   └── Standard.yaml 标准版
│   └── openclash/        OpenClash 覆写
├── docs/                 项目文档
└── .gitignore
```

## 推送前一致性审查（项目特有）

除通用规范一致性审查外，每次推送前还需检查以下项目特有内容：

1. `mihomo/config/` 中的各配置文件和 `mihomo/openclash/` 中的各覆写文件是否与实际状态一致，README 描述是否准确。