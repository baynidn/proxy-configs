# proxy-configs

四款代理客户端配置（全字段注释版）：通用设置按各家**官方文档写全**，每个字段都注明作用与推荐值；策略组与规则沿用自用现行结构（规则集走 [rules-public](https://github.com/baynidn/rules-public)，图标走 [icons](https://github.com/baynidn/icons)）。

## 文件

| 文件 | 客户端 | 说明 |
|---|---|---|
| `surge/Surge.conf` | Surge 5（iOS / Mac） | `[General]` 58 个官方字段全覆盖（通用 38 + iOS 15 + Mac 5），未启用的以注释保留 |
| `loon/Loon.conf` | Loon | `[General]` 27 个官方字段全覆盖 + 2 个主流模板扩展项（已标注），另注明 4 个已弃用字段 |
| `clash/Clash.yaml` | Mihomo 内核（FlClash / Clash Meta 等） | 顶层通用字段 + DNS / TUN / 嗅探 / NTP / 实验性全字段，含 proxy-providers 完整字段 |
| `stash/Stash.yaml` | Stash | **只用 Stash 官方文档确认支持的字段**，文件头有「禁止从 Clash 抄过来」的清单 |
| `Group.ini` | 策略组单一来源 | 四份配置的策略组段以此为准（顺序、成员、过滤）；`✈️ 我的节点` 为 Surge 专用，不进 Loon/Clash/Stash |
| `Rule.ini` | 规则单一来源 | 四份配置的规则段以此为准（顺序、策略）；RULE-SET 转各端远端规则，IP-CIDR/FINAL 直入本地规则 |

## 使用

1. 把文件里的占位符替换掉：
   - `<你的订阅地址>`：节点订阅链接可直接用作 Clash/Stash 的 provider 地址
   - `<你的 ca-p12>`、`<你的证书密码>`：自己的 MITM 证书（原件在私有仓库 `rules` 的 `config/surge/Surge.mitm.ini`、`config/loon/Loon.mitm.ini`，本仓库不存真实证书）
   - `<你的API密钥>`：仅 Clash 的 external-controller，可选
2. 策略组、规则集与仓库里现行配置保持一致；本仓库是**字段齐全的参考版/母版**，改通用设置先看这里。

## 三端通用与 TUN 差异

`clash/Clash.yaml` 与 `stash/Stash.yaml` 都是三端（iOS / 安卓 / PC）通用一份，无需按平台分文件。

- **Stash**：三端完全不用改。Stash 没有 `tun:` 段，虚拟网卡由 App 自己接管；
- **Clash**：只有 `tun:` 段按设备调三处，其余不动：

| 平台 | enable | stack | auto-detect-interface |
|---|---|---|---|
| 安卓 | `true`（现行值） | `gvisor`（现行值） | 不用（保持注释） |
| iOS | 改 `false` | 不用改 | 不用 |
| PC | `true` | 改 `system` | 改 `true`（启用） |

## 关键差异提醒

- **Stash ≠ Mihomo**：`health-check`、`exclude-filter`、`tun:`、`sniffer:`、`external-controller`、端口类字段等在 Stash 里都没有文档支持，写了会被忽略（个别还会引发更新异常）。Stash 的节点测速用 `benchmark-url` / `benchmark-timeout`，DNS 按规则转发叫 `follow-rule`。

## 字段来源

- Surge 官方手册：https://manual.nssurge.com/profile/general.html
- Loon 官方文档：https://nsloon.app/docs/General/
- Mihomo 官方 Wiki：https://wiki.metacubex.one/config/（general / dns / tun / sniff / ntp / experimental / proxy-providers / proxy-groups）
- Stash 官方 Wiki：https://stash.wiki/configuration/example-config 等

## 验证

- 策略组与规则以 `Group.ini` / `Rule.ini` 为准逐行核对：Surge 两段与 ini 原文逐字一致；Loon / Clash / Stash 的组顺序、成员、规则集顺序、规则顺序与 ini 一致（Surge 15 组含 `✈️ 我的节点`，其余三端 14 组；规则集 27 个；Clash / Stash 共 32 条规则）。
- Clash / Stash：YAML 解析通过；全部 filter 正则编译通过。
- Surge：58/58 官方字段在位；Loon：27/27 官方字段在位。
- 全仓库无真实证书、无 token（规则地址均为公开仓免 token 地址）。
