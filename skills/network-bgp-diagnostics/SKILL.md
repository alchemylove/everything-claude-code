---
name: network-bgp-diagnostics
description: Diagnostics-only BGP troubleshooting patterns for neighbor state, route exchange, prefix policy, AS path inspection, and safe evidence collection.
origin: community
---

# Network BGP 診断 (Network BGP Diagnostics)

BGP session が down、flapping、Established だが route 欠落、または想定外 prefix を advertise しているときにこの skill を使用する。デフォルト workflow は read-only evidence 収集。policy と reset action は reviewed change window に属する。

## 使用タイミング (When to Use)

- BGP neighbor が Idle、Connect、Active、OpenSent、OpenConfirm で止まっている
- session は Established だが期待 prefix がない
- route-map、prefix-list、max-prefix limit、AS path policy が route を filter している可能性
- BGP 変更の before/after evidence が必要
- BGP summary output を parse する automation をレビューしている

## Read-Only トリアージフロー (Read-Only Triage Flow)

1. 正確な neighbor、address family、VRF、local/remote ASN を特定
2. summary state と last reset reason を取得
3. peer source address への reachability を証明
4. transport failure と決めつける前に route policy 参照を確認
5. プラットフォームが対応する場合 advertised、received、installed route を比較

```text
show bgp summary
show bgp neighbors <peer>
show ip route <peer>
show tcp brief | include <peer>|:179
show logging | include BGP|<peer>
show running-config | section router bgp
show ip prefix-list
show route-map
```

device が VRF、IPv6、VPNv4、EVPN を使う場合は platform 固有 address-family command を使用。global IPv4 unicast を仮定しない。

## 状態解釈 (State Interpretation)

| State | First checks |
| --- | --- |
| Established with prefix count | Route exchange は up。policy と table selection を inspect |
| Established with zero prefixes | inbound policy、max-prefix、advertised route、AFI/SAFI を確認 |
| Active | TCP session が完了しない。routing、source、ACL、peer reachability を確認 |
| Connect | TCP 接続進行中。path と remote listener を確認 |
| OpenSent/OpenConfirm | TCP は動作。ASN、authentication、timer、capability、log を確認 |
| Idle | neighbor が無効、config 欠落、policy で block、backoff timer の可能性 |

## トランスポート確認 (Transport Checks)

```text
ping <peer> source <local-source>
traceroute <peer> source <local-source>
show ip route <peer>
show bgp neighbors <peer> | include BGP state|Last reset|Local host|Foreign host
```

peer が loopback から source される場合、双方向で loopback address に route があり、neighbor config が期待 update source を使うことを確認。

診断の shortcut として ACL や firewall policy を無効化しない。先に hit counter、log、path state を読む。

## Route Policy 確認 (Route Policy Checks)

```text
show bgp neighbors <peer> advertised-routes
show bgp neighbors <peer> routes
show ip prefix-list <name>
show route-map <name>
show bgp <prefix>
```

一部 platform では `received-routes` の前に追加 config が必要。operator が change を承認しない限り incident triage 中に追加しない。

## AS Path と Prefix レビュー (AS Path And Prefix Review)

```text
show bgp regexp _65001_
show bgp regexp ^65001$
show bgp <prefix>
show bgp neighbors <peer> advertised-routes | include Network|Path|<prefix>
```

AS-path regex は慎重に。`_65001_` は token として AS 65001 に一致。単独 `65001` は長い ASN や無関係 text に一致しうる。

## Parser パターン (Parser Pattern)

```python
import re
from typing import Any

BGP_SUMMARY_RE = re.compile(
    r"^(?P<neighbor>\d{1,3}(?:\.\d{1,3}){3})\s+"
    r"(?P<version>\d+)\s+"
    r"(?P<remote_as>\d+)\s+"
    r"(?P<msg_rcvd>\d+)\s+"
    r"(?P<msg_sent>\d+)\s+"
    r"(?P<table_version>\d+)\s+"
    r"(?P<input_queue>\d+)\s+"
    r"(?P<output_queue>\d+)\s+"
    r"(?P<uptime>\S+)\s+"
    r"(?P<state_or_prefixes>\S+)$",
    re.M,
)

def parse_bgp_summary(raw: str) -> list[dict[str, Any]]:
    rows = []
    for match in BGP_SUMMARY_RE.finditer(raw):
        state_or_prefixes = match.group("state_or_prefixes")
        if state_or_prefixes.isdigit():
            state = "Established"
            prefixes_received = int(state_or_prefixes)
        else:
            state = state_or_prefixes
            prefixes_received = None
        rows.append({
            "neighbor": match.group("neighbor"),
            "remote_as": int(match.group("remote_as")),
            "state": state,
            "prefixes_received": prefixes_received,
            "uptime": match.group("uptime"),
        })
    return rows
```

structured parser output を優先するが、BGP summary format は platform と address family で異なるため raw output を incident record と共に保存する。

## Change Window のみ (Change-Window Only)

routing に影響しうる action。自動診断として提案しない:

- BGP session の clear
- neighbor authentication、timer、update source、route-map、prefix-list の変更
- 追加 received-route storage の有効化
- firewall、ACL、control-plane policy の緩和

reset が承認された場合、platform が対応する最小破壊の soft または route-refresh を優先し、安全な理由を文書化する。

## アンチパターン (Anti-Patterns)

- `Active` は常に remote 側 down と仮定
- VRF、address family、update-source の差異を無視
- token boundary なしの広い AS-path regex
- last reset reason と log を読まず peer を hard-reset
- `received-routes` 出力欠如を route 未到着の証明とみなす

## 関連 (See Also)

- Skill: `cisco-ios-patterns`
- Skill: `network-config-validation`
- Skill: `network-interface-health`
