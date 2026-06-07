---
name: homelab-network-setup
description: Practical home and homelab network planning for gateways, switches, access points, IP ranges, DHCP reservations, DNS, cabling, and common beginner mistakes.
origin: community
---

# ホームラボネットワークセットアップ (Homelab Network Setup)

フル再構築なしで成長できるホームまたは小規模ラボネットワークを設計するときにこのスキルを使用する。

## 使用タイミング (When to Use)

- 新規ホームネットワークの計画、または ISP ルーターのみ構成の再設計。
- ゲートウェイ、スイッチ、アクセスポイントの役割選択。
- IP レンジ、DHCP スコープ、静的予約、DNS の設計。
- 将来の VLAN、Pi-hole、NAS、ラボサーバー、VPN アクセスの準備。
- ダブル NAT、不安定 Wi-Fi、変わるサーバーアドレスの新規ネットワークのトラブルシュート。

## 仕組み (How It Works)

デバイス役割を分離して開始する:

```text
Internet
  |
Modem or ONT
  |
Gateway or router      NAT, firewall, DHCP, DNS, inter-VLAN routing
  |
Managed switch         wired clients, AP uplinks, optional VLAN trunks
  |
Access points          Wi-Fi only; ideally wired backhaul
Servers and NAS        stable addresses, DNS names, monitoring
Clients and IoT        DHCP pools, isolated later if VLANs are available
```

機能チェックリストだけでなくオペレーターに合うゲートウェイを選ぶ:

| Option | Best fit | Notes |
| --- | --- | --- |
| ISP router | Basic internet only | Limited control and often poor VLAN support |
| UniFi gateway | Managed home network | Good UI, ecosystem lock-in |
| OPNsense or pfSense | Flexible homelab | Strong VLAN, firewall, VPN, and DNS control |
| MikroTik | Advanced network users | Powerful, but easy to misconfigure |
| Linux router | Tinkerers | Document rollback before using as primary gateway |

## IP プラン (IP Plan)

VPN を使う見込みがあるときは最も一般的なデフォルト `192.168.1.0/24` を避ける。
ホテル、オフィス、ISP ルーターと衝突しやすい。

```text
Example small homelab plan:

192.168.10.0/24  trusted clients
192.168.20.0/24  IoT and media devices
192.168.30.0/24  servers and NAS
192.168.40.0/24  guest Wi-Fi
192.168.99.0/24  network management

Gateway convention: .1
Infrastructure reservations: .2 through .49
Dynamic DHCP pool: .50 through .240
Spare room: .241 through .254
```

ローカル名には `home.arpa` を使う。ホームネットワーク用に予約され、`home.lan` のようなアドホック名の漏洩/衝突問題を避ける。

```text
nas.home.arpa
pihole.home.arpa
gateway.home.arpa
switch-01.home.arpa
```

## DHCP と DNS (DHCP And DNS)

- SSH、ブックマーク、監視、サービス公開するものには DHCP 予約を使う。
- ローカルリゾルバを意図的にデプロイするまでゲートウェイを DNS として配布。
- Pi-hole や別 DNS フィルタを使う場合、まず予約を付け、DHCP DNS オプションをそのアドレスに向ける。
- サブネットごとに小さな静的/予約レンジを保ち、交換時に動的リースと衝突しない。

## 配線と Wi-Fi (Cabling And Wi-Fi)

- 可能なら Ethernet を引いてメッシュより有線 AP バックホールを優先。
- 予算が許せば AP とカメラ用 PoE スイッチ。
- 各ケーブル両端にラベル、簡単なポートマップを維持。
- 停電が多いならゲートウェイ、スイッチ、DNS サーバー、NAS を UPS に。

## 例 (Examples)

### 初級アップグレード (Beginner Upgrade)

目標: ISP ルーターを維持しつつ小ラボを安定化。

1. NAS、Pi、SSH ホストに DHCP 予約を設定。
2. ローカル名を `home.arpa` に移行。
3. 二次ルーター/AP の重複 DHCP サーバーを無効化。
4. 無線バックホールではなくメイン AP を有線化。

### VLAN 対応プラン (VLAN-Ready Plan)

目標: すぐ有効化せず将来のセグメンテーションに備える。

1. trusted、IoT、servers、guest、management 用に非重複 /24 を選ぶ。
2. 全サブネットでゲートウェイ .1、インフラ .2-.49 を予約。
3. VLAN と inter-VLAN ファイアウォールをサポートするゲートウェイとスイッチを購入。
4. 各ネットワークに最終的にマップする SSID とスイッチポートを文書化。

## アンチパターン (Anti-Patterns)

- 理由や文書なしのダブル NAT。
- VPN アクセス計画時の `192.168.1.0/24` 使用。
- NAS、Pi-hole、Home Assistant などサービスホストの動的アドレス。
- DHCP がまだ有効な消費者ルーターを AP として再利用。
- カメラ、スマートプラグ、ラップトップ、サーバーが同一信頼境界を共有するフラットネットワーク。

## 関連 (See Also)

- Skill: `network-interface-health`
- Skill: `network-config-validation`
