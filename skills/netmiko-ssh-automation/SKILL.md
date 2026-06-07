---
name: netmiko-ssh-automation
description: Safe Python Netmiko patterns for read-only collection, bounded batch SSH, TextFSM parsing, guarded config changes, timeouts, and network automation error handling.
origin: community
---

# Netmiko SSH Automation (Netmiko SSH Automation)

Netmiko で network device に接続する Python automation の作成・レビュー時にこの skill を使用する。デフォルト path は read-only。config 変更には別 change window、peer review、rollback plan が必要。

## 使用タイミング (When to Use)

- router、switch、firewall 横断で `show` command output を収集
- interface、routing、config evidence 向け小規模 audit script
- network SSH script に timeout と exception handling を追加
- template があるとき TextFSM で command output を parse
- production device に触れる前の automation レビュー

## 安全デフォルト (Safety Defaults)

- read-only `send_command()` 収集から開始
- inventory は小さく明示的。address range 全体を sweep しない
- credential は env、vault、`getpass`。ハードコードしない
- connection と read timeout を設定
- 古い device を過負荷にしない concurrency 上限
- `send_config_set()` の前に明示 operator flag を要求
- 変更が検証・承認されるまで `save_config()` を呼ばない

## Read-Only 接続パターン (Read-Only Connection Pattern)

```python
import os
from getpass import getpass
from netmiko import ConnectHandler
from netmiko.exceptions import (
    NetmikoAuthenticationException,
    NetmikoTimeoutException,
    ReadTimeout,
)

device = {
    "device_type": "cisco_ios",
    "host": "192.0.2.10",
    "username": os.environ.get("NETMIKO_USERNAME") or input("Username: "),
    "password": os.environ.get("NETMIKO_PASSWORD") or getpass("Password: "),
    "secret": os.environ.get("NETMIKO_ENABLE_SECRET"),
    "conn_timeout": 10,
    "auth_timeout": 20,
    "banner_timeout": 15,
    "read_timeout_override": 30,
}

try:
    with ConnectHandler(**device) as conn:
        if device.get("secret") and not conn.check_enable_mode():
            conn.enable()
        output = conn.send_command("show ip interface brief", read_timeout=30)
        print(output)
except NetmikoAuthenticationException:
    print("Authentication failed")
except NetmikoTimeoutException:
    print("SSH connection timed out")
except ReadTimeout:
    print("Command read timed out")
```

example では documentation range の placeholder address を使用。実 inventory は ignored local file または secrets-managed system に。

## バッチ収集 (Batch Collection)

```python
from concurrent.futures import ThreadPoolExecutor, as_completed
from typing import Any

def collect_show(device: dict[str, Any], command: str) -> dict[str, Any]:
    host = device["host"]
    try:
        with ConnectHandler(**device) as conn:
            output = conn.send_command(command, read_timeout=45)
        return {"host": host, "ok": True, "output": output}
    except (NetmikoAuthenticationException, NetmikoTimeoutException, ReadTimeout) as exc:
        return {"host": host, "ok": False, "error": type(exc).__name__}

results = []
with ThreadPoolExecutor(max_workers=8) as pool:
    futures = [pool.submit(collect_show, device, "show version") for device in devices]
    for future in as_completed(futures):
        results.append(future.result())
```

device estate と AAA が higher volume を処理できると分かるまで `max_workers` は低く保つ。

## 構造化 Parsing (Structured Parsing)

Netmiko は TextFSM、TTP、Genie で supported command output を parse 可能。parser output は最適化であり唯一の evidence path ではない。

```python
with ConnectHandler(**device) as conn:
    parsed = conn.send_command(
        "show ip interface brief",
        use_textfsm=True,
        raise_parsing_error=False,
        read_timeout=30,
    )

if isinstance(parsed, str):
    print("No parser template matched; store raw output for review")
else:
    for row in parsed:
        print(row)
```

parsing が blocking decision を駆動するなら、mismatch 検査のため raw command output も保持。

## Guarded Config パターン (Guarded Config Pattern)

```python
import os

commands = [
    "interface GigabitEthernet0/1",
    "description CHANGE-1234 UPLINK-TO-CORE",
]

apply_changes = os.environ.get("APPLY_NETWORK_CHANGES") == "1"

if not apply_changes:
    print("Dry run only. Candidate commands:")
    print("\n".join(commands))
else:
    with ConnectHandler(**device) as conn:
        conn.enable()
        before = conn.send_command("show running-config interface GigabitEthernet0/1")
        output = conn.send_config_set(commands)
        after = conn.send_command("show running-config interface GigabitEthernet0/1")
        print(before)
        print(output)
        print(after)
        print("Verify behavior before saving startup config.")
```

config save は別 approval step。production では rollback snippet と before/after evidence を change record に含める。

## レビューチェックリスト (Review Checklist)

- script は明示 inventory source を特定しているか
- credential は source、log、exception message から除外されているか
- `conn_timeout`、`auth_timeout`、command `read_timeout` は設定されているか
- failure は batch 全体を止めず device ごとに報告されるか
- 広範 scan と unbounded concurrency を避けているか
- config change は dry-run または明示 operator flag の背後か
- `save_config()` は initial push から分離され verification に紐づくか

## アンチパターン (Anti-Patterns)

- source に password、enable secret、private key をハードコード
- デフォルト code path で config command を送信
- reviewed inventory ではなく CIDR range に automation を実行
- sanitization なしに full running config を shared system に log
- parser 成功を device state 正しさの証明とみなす

## 関連 (See Also)

- Skill: `cisco-ios-patterns`
- Skill: `network-config-validation`
- Skill: `network-interface-health`
