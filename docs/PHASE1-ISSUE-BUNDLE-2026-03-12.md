# Phase 1 Issue Bundle — 2026年3月12日 (Phase 1 Issue Bundle — March 12, 2026)

## ステータス (Status)

これらの issue ドラフトは、3月11日の mega plan と 3月12日の handoff から作成した。GitHub 上で直接 issue を開こうとしたが、MCP セッションに GitHub 認証がなく issue 作成はブロックされた。

## GitHub ステータス (GitHub Status)

これらのドラフトは後に `gh` 経由で投稿された:

- `#423` Implement manifest-driven selective install profiles for ECC
- `#421` Add ECC install-state plus uninstall / doctor / repair lifecycle
- `#424` Define canonical session adapter contract for ECC 2.0 control plane
- `#422` Define generated skill placement and provenance policy
- `#425` Define governance and visibility past the tool call

以下の本文は、issue 作成に使ったローカル source bundle として保存されている。

## Issue 1

### タイトル (Title)

Implement manifest-driven selective install profiles for ECC

### ラベル (Labels)

- `enhancement`

### 本文 (Body)

```md
## 問題 (Problem)

ECC は依然として主に target と language 単位でインストールされる。リポジトリには selective-install manifest と non-mutating plan resolver の第一版が既にあるが、インストーラー自体はまだそれら profile を消費していない。

既存の groundwork はリポジトリ内に既に land 済み:

- `manifests/install-modules.json`
- `manifests/install-profiles.json`
- `scripts/ci/validate-install-manifests.js`
- `scripts/lib/install-manifests.js`
- `scripts/install-plan.js`

つまり、不足しているのは design discovery ではなくなった。不足しているのは execution である: profile/module resolution を実際の install flow に接続し、後方互換性を維持すること。

## スコープ (Scope)

現在の ECC target 向けに manifest-driven install execution を実装する:

- `claude`
- `cursor`
- `antigravity`

第一版として以下を追加:

- `ecc-install --profile <name>`
- `ecc-install --modules <id,id,...>`
- module の target サポートに基づく target-aware filtering
- rollout 中の後方互換 legacy language install

## 非目標 (Non-Goals)

- 同一 issue 内での uninstall/doctor/repair lifecycle の完全実装
- rollout を阻害する場合の第一版での Codex/OpenCode install target
- リポジトリを別 published package へ再編成すること

## 受け入れ基準 (Acceptance Criteria)

- `install.sh` が named profile を resolve してインストールできる
- `install.sh` が explicit module ID を resolve できる
- target 非対応 module は deterministically に skip または reject される
- legacy language-based install mode が引き続き動作する
- profile resolution と installer 動作をテストでカバーする
- 新しい推奨 profile/module install path を docs で説明する
```

## Issue 2

### タイトル (Title)

Add ECC install-state plus uninstall / doctor / repair lifecycle

### ラベル (Labels)

- `enhancement`

### 本文 (Body)

```md
## 問題 (Problem)

ECC には canonical installed-state record がない。そのため uninstall、repair、post-install inspection が nondeterministic になる。

現状、リポジトリは installable content を分類できるが、以下には依然として確実に答えられない:

- どの profile/modules がインストールされたか
- どの target にインストールされたか
- ECC,ECC が所有する path は何か
- ECC-managed file のみをどう remove または repair するか

install-state がなければ、lifecycle command は guesswork になる。

## スコープ (Scope)

durable install-state contract と最初の lifecycle command を導入する:

- `ecc list-installed`
- `ecc uninstall`
- `ecc doctor`
- `ecc repair`

推奨 state 配置:

- Claude: `~/.claude/ecc/install-state.json`
- Cursor: `./.cursor/ecc-install-state.json`
- Antigravity: `./.agent/ecc-install-state.json`

state file は最低限以下を記録する:

- installed version
- timestamp
- target
- profile
- resolved modules
- copied/managed paths
- source repo version または package version

## 非目標 (Non-Goals)

- インストーラー architecture のゼロからの再構築
- remote/cloud control-plane 機能の完全実装
- 自然に含まれない限り、現在の local installer を超える target サポート拡張

## 受け入れ基準 (Acceptance Criteria)

- 成功した install が deterministically に install-state を書き込む
- `list-installed` が target/profile/modules/version を明確に報告する
- `doctor` が missing または drift した managed path を報告する
- `repair` が記録された install-state から missing managed file を復元する
- `uninstall` が ECC-managed file のみを削除し、無関係な local file は残す
- install-state 作成と lifecycle 動作をテストでカバーする
```

## Issue 3

### タイトル (Title)

Define canonical session adapter contract for ECC 2.0 control plane

### ラベル (Labels)

- `enhancement`

### 本文 (Body)

```md
## 問題 (Problem)

ECC には実際の orchestration/session substrate があるが、依然として implementation-specific である。

現状:

- tmux/worktree orchestration が存在する
- machine-readable session snapshot が存在する
- Claude local session-history command が存在する

まだ存在しないのは、以下を横断して session/task state を normalize できる harness-neutral adapter boundary である:

- tmux-orchestrated worker
- plain Claude session
- Codex worktree
- OpenCode session
- 将来の remote または GitHub-integrated operator surface

その adapter contract がなければ、将来の ECC 2.0 operator shell は tmux-specific と markdown-coordination の詳細を直接読むことを強いられる。

## スコープ (Scope)

第一版 canonical session adapter layer を定義し実装する。

推奨 deliverable:

- adapter registry
- canonical session snapshot schema
- 現在の orchestration code を backing とする `dmux-tmux` adapter
- 現在の session history utility を backing とする `claude-history` adapter
- canonical session snapshot 向け read-only inspection CLI

## 非目標 (Non-Goals)

- 同一 issue 内での ECC 2.0 UI の完全実装
- monetization/GitHub App 実装
- remote multi-user control plane

## 受け入れ基準 (Acceptance Criteria)

- documented canonical snapshot contract がある
- 現在の tmux orchestration snapshot code が top-level product contract ではなく adapter として wrap されている
- abstraction が実在することを示す second non-tmux adapter がある
- adapter selection と normalized snapshot output をテストでカバーする
- design が adapter concern を orchestration と UI concern から明確に分離している
```

## Issue 4

### タイトル (Title)

Define generated skill placement and provenance policy

### ラベル (Labels)

- `enhancement`

### 本文 (Body)

```md
## 問題 (Problem)

ECC には大きく成長し続ける skill surface があるが、generated/imported/learned skill にはまだ明確な long-term placement と provenance policy がない。

これにより複数の問題が生じる:

- curated skill と generated/learned skill の分離が不明確
- ローカルに存在するかどうか不明な directory 周辺の validator noise
- imported または machine-generated skill content の weak provenance
- 将来の automated learning output の配置先の不確実性

ECC が成長するにつれ、generated skill artifact の所属先と識別方法について explicit rule が必要になる。

## スコープ (Scope)

以下について repo-wide policy を定義する:

- curated vs generated vs imported skill placement
- provenance metadata 要件
- optional/generated skill directory に対する validator 動作
- generated skill を ship、ignore、install/build step で materialize するかどうか

## 非目標 (Non-Goals)

- 完全な external skill marketplace の構築
- 既存 skill content の一括 rewrite
- 同一 issue 内でのすべての content-quality issue の解決

## 受け入れ基準 (Acceptance Criteria)

- generated/imported skill 向け documented placement policy がある
- provenance 要件が explicit である
- optional/generated skill location 周辺で validator が ambiguous な動作を出さない
- policy が publishable と local-only を明確に区別する
- follow-on implementation work が concrete で bounded な PR-sized step に分割されている
```
