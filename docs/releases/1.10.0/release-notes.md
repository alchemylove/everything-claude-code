# ECC v1.10.0 リリースノート (ECC v1.10.0 Release Notes)

## ポジショニング (Positioning)

ECC v1.10.0 は surface-sync と operator-lane リリースです。

目標は、公開リポジトリ、plugin metadata、install path、エコシステムストーリーをプロジェクトの実際のライブ状態に再び反映させつつ、コア harness レイヤー周辺で成長した operator workflow と media tooling の出荷を続けることでした。

## 変更点 (What Changed)

- ライブ OSS 面を **38 agents、156 skills、72 commands** に同期。
- Claude plugin、Codex plugin、OpenCode package metadata、リリース向け docs を **1.10.0** に更新。
- トップラインリポジトリメトリクスをライブ公開リポジトリに合わせて更新（**14万+ stars**、**2.1万+ forks**、**170+ contributors**）。
- operator/workflow lane を拡張：
  - `brand-voice`
  - `social-graph-ranker`
  - `connections-optimizer`
  - `customer-billing-ops`
  - `google-workspace-ops`
  - `project-flow-ops`
  - `workspace-surface-audit`
- media lane を拡張：
  - `manim-video`
  - `remotion-video-creation`
- `nestjs-patterns` を含む framework/domain カバレッジを追加・安定化。

## ECC 2.0 ステータス (ECC 2.0 Status)

ECC 2.0 は **alpha として実在し利用可能** ですが、**general-availability 完了ではありません**。

今日存在するもの：

- メインリポジトリ内の `ecc2/` Rust control-plane コードベース
- `cargo build --manifest-path ecc2/Cargo.toml` が通る
- 現在利用可能な `ecc-tui` コマンド：
  - `dashboard`
  - `start`
  - `sessions`
  - `status`
  - `stop`
  - `resume`
  - `daemon`

これが意味すること：

- control-plane 面を今すぐ試せる。
- ECC 2.0 roadmap 全体を完了したと説明すべきではない。
- 今日の正しいフレーミングは **ECC 2.0 alpha / control-plane preview** であり、GA ではない。

## インストールガイダンス (Install Guidance)

現在の install 面：

- Claude Code plugin
- npm の `ecc-universal`
- Codex plugin manifest
- OpenCode package/plugin 面
- AgentShield CLI + npm + GitHub Marketplace action

重要なニュアンス：

- Claude plugin は platform レベルの `rules` 配布制限の影響を受け続ける。
- selective install / OSS path は、完全な ECC 面を望むチームにとって依然として最も信頼できるフルインストール。

## 推奨アップグレードパス (Recommended Upgrade Path)

1. 最新の plugin/install metadata に更新する。
2. 完全な rules カバレッジが必要な場合は selective install / OSS path を優先する。
3. guardrail とリポジトリスキャンに AgentShield を使う。
4. オープン P0/P1 roadmap が実質的に消化されるまで、ECC 2.0 を alpha control-plane 面として扱う。
