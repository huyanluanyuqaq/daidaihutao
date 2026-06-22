# 🚀 胡桃老师一手自用，50包邮

## 📢 Telegram: [@huyanluanyuqaq](https://t.me/huyanluanyuqaq) &nbsp;|&nbsp; 📦 规则库: [hutaorules](https://github.com/huyanluanyuqaq/hutaorules)

<p align="center">
  <a href="https://github.com/huyanluanyuqaq/daidaihutao"><img src="https://img.shields.io/badge/Mihomo-v1.19.27-4FC08D?style=flat-square" alt="Mihomo"/></a>
  <img src="https://img.shields.io/badge/Maintained-Yes-4FC08D?style=flat-square" alt="Maintained"/>
  <img src="https://img.shields.io/badge/License-GPLv3-blue?style=flat-square" alt="License"/>
</p>

---

## ⚠️ 免责声明

任何以任何方式查看此项目的人或直接或间接使用该项目的使用者，都应仔细阅读此声明。保留随时更改或补充此免责声明的权利。一旦使用并复制了该项目的任何文件，则视为您已接受此免责声明：

1. 本项目所有配置文件**仅用于资源共享和学习研究**，不保证其合法性、准确性、完整性和有效性。
2. 因间接使用本项目导致的隐私泄漏或其他后果，**概不负责**。
3. 请勿将本项目的任何内容用于**商业或非法目的**，否则后果自负。
4. 本项目不提供任何形式的代理/翻墙服务，也不承担因使用本配置产生的任何法律责任。
5. 对任何配置文件问题概不负责，包括因配置错误导致的任何损失或损害。
6. 您必须在下载后的 **24 小时内**从计算机或手机中完全删除以上内容。

---

## 📋 仓库结构

```
├── 📁 JS/                   脚本（Sub-Store 重命名、内核更新等）
├── 📁 icon/                 图标资源
├── 📁 mihomo/
│   ├── 📁 config/           配置文件
│   └── 📁 openclash/        OpenClash 覆写配置
├── 📁 docs/                 项目文档
└── .gitignore
```

---

## ⚙️ 配置文件

### Mihomo 配置

`mihomo/config/` 目录下提供三套配置模板，覆盖从极简到全功能的不同需求：

| 版本 | 定位 | 策略组 | 本地导入 | RAW 直链 |
|:-----|:------|:------:|:---------|:----------|
| **ProMax** 🦾 | 全能版 — 完整策略集，支持测速与负载均衡 | 28 | [ProMax.yaml](mihomo/config/ProMax.yaml) | [RAW](https://raw.githubusercontent.com/huyanluanyuqaq/daidaihutao/main/mihomo/config/ProMax.yaml) |
| **Pro** 💪 | 轻量版 — 常用策略集，含故障转移 | 20 | [Pro.yaml](mihomo/config/Pro.yaml) | [RAW](https://raw.githubusercontent.com/huyanluanyuqaq/daidaihutao/main/mihomo/config/Pro.yaml) |
| **Standard** 🎯 | 极简版 — 基础分流，仅保留手动节点选择 | 3 | [Standard.yaml](mihomo/config/Standard.yaml) | [RAW](https://raw.githubusercontent.com/huyanluanyuqaq/daidaihutao/main/mihomo/config/Standard.yaml) |

> 💡 **提示**：RAW 直链可用于 Mihomo Party、Clash Verge Rev 等客户端的远程配置功能，配置随仓库自动同步更新。

### OpenClash 覆写

`mihomo/openclash/` 目录下的 `.conf` 文件用于 OpenClash 覆写配置：

| 文件 | 对应配置 | RAW 直链 |
|:-----|:---------|:----------|
| [ProMax_overwrite.conf](mihomo/openclash/ProMax_overwrite.conf) | ProMax.yaml | [RAW](https://raw.githubusercontent.com/huyanluanyuqaq/daidaihutao/main/mihomo/openclash/ProMax_overwrite.conf) |
| [Pro_overwrite.conf](mihomo/openclash/Pro_overwrite.conf) | Pro.yaml | [RAW](https://raw.githubusercontent.com/huyanluanyuqaq/daidaihutao/main/mihomo/openclash/Pro_overwrite.conf) |
| [Standard_overwrite.conf](mihomo/openclash/Standard_overwrite.conf) | Standard.yaml | [RAW](https://raw.githubusercontent.com/huyanluanyuqaq/daidaihutao/main/mihomo/openclash/Standard_overwrite.conf) |

---

## 🔧 核心特性

### 🧩 策略与规则对称

配置遵循 **"策略即路径"** 设计理念：

- 策略组排序与规则匹配顺序严格 **1:1 对应**
- 客户端看到的策略列表 = 流量实际匹配路径
- 无需阅读规则文件，即可直观理解流量走向

### 🔄 自动故障转移

- 基础流量策略默认启用故障转移，节点异常时自动切换备用节点或地区
- AI、流媒体等独立策略组采用固定地区策略，避免频繁切换影响使用体验

### 📦 规则集来源

配置文件通过规则集提供者（`rule-providers`）动态加载远端规则，规则文件由 [hutaorules](https://github.com/huyanluanyuqaq/hutaorules) 仓库维护。

---

## 🙏 致谢

- [**MetaCubeX/meta-rules-dat**](https://github.com/MetaCubeX/meta-rules-dat) — 规则集数据源
- [**blackmatrix7/ios_rule_script**](https://github.com/blackmatrix7/ios_rule_script) — 规则分类参考与脚本资源
- [**666OS/YYDS**](https://github.com/666OS/YYDS) — 原始配置结构，为本项目提供了坚实的基础
- [**Koolson/Qure**](https://github.com/Koolson/Qure) — 策略组图标资源

---

## 📄 License

[GNU General Public License v3.0](LICENSE)
