# セッションアダプター契約 (Session Adapter Contract)

このドキュメントは `ecc.session.v1` 向けの canonical ECC session snapshot 契約を定義する。

契約は `scripts/lib/session-adapters/canonical-session.js` に実装されている。このドキュメントは adapter と consumer 向けの normative specification である。

## 目的 (Purpose)

ECC には複数の session ソースがある:

- tmux-orchestrated worktree session
- Claude local session history
- 将来の harness と control-plane backend

adapter はこれらのソースを1つの control-plane-safe snapshot shape に正規化する。これにより inspection、persistence、将来の UI layer が harness 固有の file や runtime detail に依存しなくなる。

## Canonical Snapshot (Canonical Snapshot)

すべての adapter は次の top-level shape を持つ JSON シリアライズ可能オブジェクトを返さなければならない (MUST):

```json
{
  "schemaVersion": "ecc.session.v1",
  "adapterId": "dmux-tmux",
  "session": {
    "id": "workflow-visual-proof",
    "kind": "orchestrated",
    "state": "active",
    "repoRoot": "/tmp/repo",
    "sourceTarget": {
      "type": "session",
      "value": "workflow-visual-proof"
    }
  },
  "workers": [
    {
      "id": "seed-check",
      "label": "seed-check",
      "state": "running",
      "health": "healthy",
      "branch": "feature/seed-check",
      "worktree": "/tmp/worktree",
      "runtime": {
        "kind": "tmux-pane",
        "command": "codex",
        "pid": 1234,
        "active": false,
        "dead": false
      },
      "intent": {
        "objective": "Inspect seeded files.",
        "seedPaths": ["scripts/orchestrate-worktrees.js"]
      },
      "outputs": {
        "summary": [],
        "validation": [],
        "remainingRisks": []
      },
      "artifacts": {
        "statusFile": "/tmp/status.md",
        "taskFile": "/tmp/task.md",
        "handoffFile": "/tmp/handoff.md"
      }
    }
  ],
  "aggregates": {
    "workerCount": 1,
    "states": {
      "running": 1
    },
    "healths": {
      "healthy": 1
    }
  }
}
```

## 必須フィールド (Required Fields)

### Top level

| フィールド (Field) | 型 (Type) | 備考 (Notes) |
| --- | --- | --- |
| `schemaVersion` | string | この契約では `ecc.session.v1` でなければならない (MUST) |
| `adapterId` | string | `dmux-tmux` や `claude-history` などの安定 adapter 識別子 |
| `session` | object | Canonical session metadata |
| `workers` | array | Canonical worker record; 空でもよい |
| `aggregates` | object | 導出 worker 件数 |

### `session`

| フィールド (Field) | 型 (Type) | 備考 (Notes) |
| --- | --- | --- |
| `id` | string | adapter domain 内の安定識別子 |
| `kind` | string | `orchestrated` や `history` などの high-level session family |
| `state` | string | Canonical session state |
| `sourceTarget` | object | session を開いた target の provenance |

### `session.sourceTarget`

| フィールド (Field) | 型 (Type) | 備考 (Notes) |
| --- | --- | --- |
| `type` | string | `plan`、`session`、`claude-history`、`claude-alias`、`session-file` などの lookup class |
| `value` | string | raw target value または解決済み path |

### `workers[]`

| フィールド (Field) | 型 (Type) | 備考 (Notes) |
| --- | --- | --- |
| `id` | string | adapter scope 内の安定 worker 識別子 |
| `label` | string | operator 向け label |
| `state` | string | Canonical worker state (lifecycle) |
| `health` | string | Canonical worker health (operational condition) |
| `runtime` | object | Execution/runtime metadata |
| `intent` | object | この worker/session が存在する理由 |
| `outputs` | object | 構造化 outcome と check |
| `artifacts` | object | adapter 所有の file/path 参照 |

### `workers[].runtime`

| フィールド (Field) | 型 (Type) | 備考 (Notes) |
| --- | --- | --- |
| `kind` | string | `tmux-pane` や `claude-session` などの runtime family |
| `active` | boolean | runtime が現在 active か |
| `dead` | boolean | runtime が dead/finished と既知か |

### `workers[].intent`

| フィールド (Field) | 型 (Type) | 備考 (Notes) |
| --- | --- | --- |
| `objective` | string | 主 objective または title |
| `seedPaths` | string[] | worker/session に関連する seed または context path |

### `workers[].outputs`

| フィールド (Field) | 型 (Type) | 備考 (Notes) |
| --- | --- | --- |
| `summary` | string[] | 完了 output または summary item |
| `validation` | string[] | validation evidence または check |
| `remainingRisks` | string[] | open risk、follow-up、note |

### `aggregates`

| フィールド (Field) | 型 (Type) | 備考 (Notes) |
| --- | --- | --- |
| `workerCount` | integer | `workers.length` と等しくなければならない (MUST) |
| `states` | object | `workers[].state` から導出した count map |
| `healths` | object | `workers[].health` から導出した count map |

## オプションフィールド (Optional Fields)

オプションフィールドは省略してよい (MAY) が、出力する場合は文書化された型を保持しなければならない (MUST):

| フィールド (Field) | 型 (Type) | 備考 (Notes) |
| --- | --- | --- |
| `session.repoRoot` | `string \| null` | 既知の場合の repo/worktree root |
| `workers[].branch` | `string \| null` | 既知の場合の branch 名 |
| `workers[].worktree` | `string \| null` | 既知の場合の worktree path |
| `workers[].runtime.command` | `string \| null` | 既知の場合の active command |
| `workers[].runtime.pid` | `number \| null` | 既知の場合の process id |
| `workers[].artifacts.*` | adapter-defined | adapter 所有の file path または構造化参照 |

adapter 固有のオプションフィールドは `runtime`、`artifacts`、または他の文書化 nested object 内に属する。adapter はこの契約を更新せずに新しい top-level field を invent してはならない (MUST NOT)。

## 状態セマンティクス (State Semantics)

契約は意図的に `session.state` と `workers[].state` を複数 harness に対応できるよう柔軟に保つが、現在の adapter は次の値を使用する:

- `dmux-tmux`
  - session state: `active`、`completed`、`failed`、`idle`、`missing`
  - worker state: worker status file から導出、例: `running` や `completed`
- `claude-history`
  - session state: `recorded`
  - worker state: `recorded`

consumer は未知の state 文字列を有効な adapter 固有値として扱い、gracefully に degrade しなければならない (MUST)。

## バージョニング戦略 (Versioning Strategy)

`schemaVersion` が唯一の互換性ゲートである。consumer はそれに基づいて分岐しなければならない (MUST)。

### `ecc.session.v1` で許可される変更

- 新しい optional nested field の追加
- 新しい adapter id の追加
- 新しい state 文字列値の追加
- 新しい health 文字列値の追加
- `workers[].artifacts` 内の新しい artifact key の追加

### 新しい schema version が必要な変更

- required field の削除
- field の rename
- field 型の変更
- 既存 field の意味を非互換に変更
- 同じ version 文字列のままデータを別 field へ移動

上記のいずれかが起きた場合、producer は `ecc.session.v2` などの新しい version 文字列を emit しなければならない (MUST)。

## Adapter 準拠要件 (Adapter Compliance Requirements)

すべての ECC session adapter は次を満たさなければならない (MUST):

1. 正確に `schemaVersion: "ecc.session.v1"` を emit する。
2. すべての required field と型を満たす snapshot を返す。
3. 未知の optional scalar には `null`、未知の list には空配列を使う。
4. adapter 固有 detail を `runtime`、`artifacts`、または他の文書化 nested object 配下に保持する。
5. `aggregates.workerCount === workers.length` を保証する。
6. `aggregates.states` が emit した worker state と一致することを保証する。
7. `aggregates.healths` が emit した worker health 値と一致することを保証する。
8. plain JSON シリアライズ可能な値のみを produce する。
9. persistence または downstream use の前に canonical shape を validate する。
10. 正規化 canonical snapshot を session recording shim 経由で persist する。この repo では shim はまず `scripts/lib/state-store` を試し、state store module がまだ利用不可の場合のみ JSON recording file にフォールバックする。

## Consumer 期待 (Consumer Expectations)

consumer は次を行うべき (SHOULD):

- `ecc.session.v1` では文書化 field のみに依存する
- 未知の optional field を無視する
- `adapterId`、`session.kind`、`runtime.kind` を exhaustive enum ではなく routing hint として扱う
- `workers[].artifacts` 内の adapter 固有 artifact key を期待する

consumer は次を行ってはならない (MUST NOT):

- 文書化されていない field から harness 固有挙動を推論する
- すべての adapter が tmux pane、git worktree、markdown coordination file を持つと仮定する
- state 文字列が unfamiliar なだけで snapshot を拒否する

## 現在の Adapter マッピング (Current Adapter Mappings)

### `dmux-tmux`

- Source: `scripts/lib/orchestration-session.js`
- Session id: orchestration session name
- Session kind: `orchestrated`
- Session source target: plan path または session name
- Worker runtime kind: `tmux-pane`
- Artifacts: `statusFile`、`taskFile`、`handoffFile`

### `claude-history`

- Source: `scripts/lib/session-manager.js`
- Session id: 存在すれば Claude short id、なければ session filename 由来 id
- Session kind: `history`
- Session source target: explicit history target、alias、または `.tmp` session file
- Worker runtime kind: `claude-session`
- Intent seed paths: `### Context to Load` から parse
- Artifacts: `sessionFile`、`context`

## 検証リファレンス (Validation Reference)

repo 実装は次を validate する:

- required object structure
- required string field
- boolean runtime flag
- string-array output と seed path
- aggregate count consistency

adapter は validation 失敗を user input error ではなく contract bug として扱うべきである。

## Recording フォールバック挙動 (Recording Fallback Behavior)

JSON fallback recorder は dedicated state store が land する前の一時的 compatibility shim である。挙動は次のとおり:

- latest snapshot は常に in-place で置換される
- history は distinct snapshot body のみを記録する
- 変更のない repeated read は duplicate history entry を append しない

これにより `session-inspect` と他の polling 型 read が、同じ unchanged session snapshot に対して unbounded history を増やさない。
