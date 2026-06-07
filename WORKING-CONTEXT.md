# 作業コンテキスト (Working Context)

最終更新: 2026-04-08

## 目的 (Purpose)

エージェント、スキル、コマンド、フック、ルール、インストールサーフェス、ECC 2.0 プラットフォーム構築向けの公開 ECC プラグインリポジトリ。

## 現在の正 (Current Truth)

- デフォルトブランチ: `main`
- 公開リリースサーフェスは `v1.10.0` に整合
- 公開カタログの正: エージェント `47`、コマンド `79`、スキル `181`
- 公開プラグイン slug は `ecc`；レガシー `everything-claude-code` インストールパスは互換性のため引き続きサポート
- リリース議論: `#1272`
- ECC 2.0 はリポジトリ内に存在しビルド可能だが、まだ GA ではなく alpha
- 現在アクティブな運用作業:
  - デフォルトブランチをグリーンに保つ
  - 公開 PR バックログがゼロになった今、`main` からの issue 駆動修正を継続
  - ECC 2.0 コントロールプレーンとオペレーターサーフェスの構築を継続

## 現在の制約 (Current Constraints)

- タイトルやコミットサマリーだけでマージしない。
- 出荷済み ECC サーフェスに任意の外部 runtime インストールを入れない。
- 重複するスキル、フック、エージェントは、重複が実質的で runtime 分離が不要なら統合する。

## アクティブキュー (Active Queues)

- PR バックログ: 縮小したがアクティブ；安全な ECC ネイティブ変更のみ直接ポートし、重複・古いジェネレーター・未監査の外部 runtime レーンはクローズ
- 上流ブランチバックログは選択的な採掘とクリーンアップがまだ必要:
  - `origin/feat/hermes-generated-ops-skills` にはまだ 3 つのユニークコミットがあるが、再利用可能な ECC ネイティブスキルのみ救済すべき
  - 複数の `origin/ecc-tools/*` 自動化ブランチは古く、ユニークな価値がないことを確認後に刈り込むべき
- プロダクト:
  - 選択的インストールのクリーンアップ
  - コントロールプレーン primitives
  - オペレーターサーフェス
  - 自己改善スキル
  - 出荷済み `commands/` と `skills/` と `agent.yaml` エクスポートのパリティを保ち、モダンなインストールサーフェスがコマンド登録を黙って失わないようにする
- スキル品質:
  - コンテンツ向けスキルをソース裏付けの voice モデリングに書き換える
  - 汎用 LLM 修辞、定型 CTA、強制プラットフォームステレオタイプを除去
  - 重複または低シグナルスキルコンテンツの 1 件ずつ監査を継続
  - リポジトリガイダンスと貢献フローを skills-first に移行し、commands は明示的な互換 shim のみ残す
  - 生 API や切断された primitives だけでなく、接続サーフェスをラップするオペレータースキルを追加
  - 正規 voice システム、ネットワーク最適化レーン、再利用可能な Manim 説明レーンを land する
- セキュリティ:
  - 依存関係姿勢をクリーンに保つ
  - 自己完結型のフックと MCP 挙動を維持

## オープン PR 分類 (Open PR Classification)

- 2026-04-01 にバックログ衛生 / マージポリシーでクローズ:
  - `#1069` `feat: add everything-claude-code ECC bundle`
  - `#1068` `feat: add everything-claude-code-conventions ECC bundle`
  - `#1080` `feat: add everything-claude-code ECC bundle`
  - `#1079` `feat: add everything-claude-code-conventions ECC bundle`
  - `#1064` `chore(deps-dev): bump @eslint/js from 9.39.2 to 10.0.1`
  - `#1063` `chore(deps-dev): bump eslint from 9.39.2 to 10.1.0`
- 2026-04-01 に外部エコシステム由来のためクローズ（ECC ネイティブ再ポートのみ許可）:
  - `#852` openclaw-user-profiler
  - `#851` openclaw-soul-forge
  - `#640` harper skills
- 次に完全 diff 監査すべきネイティブサポート候補:
  - `#1055` Dart / Flutter support
  - `#1043` C# reviewer and .NET skills
- 監査後に直接ポート済み候補:
  - `#1078` hook-id dedupe for managed Claude hook reinstalls
  - `#844` ui-demo skill
  - `#1110` install-time Claude hook root resolution
  - `#1106` portable Codex Context7 key extraction
  - `#1107` Codex baseline merge and sample agent-role sync
  - `#1119` stale CI/lint cleanup that still contained safe low-risk fixes
- 完全監査後に ECC 内でポートまたは再構築:
  - `#894` Jira integration
  - `#814` + `#808` rebuild as a single consolidated notifications lane for Opencode and cross-harness surfaces

## インターフェース (Interfaces)

- 公開の正: GitHub issues と PRs
- 内部実行の正: ECC プログラム配下のリンク済み Linear 作業項目
- 現在リンク済み Linear 項目:
  - `ECC-206` ecosystem CI baseline
  - `ECC-207` PR backlog audit and merge-policy enforcement
  - `ECC-208` context hygiene
  - `ECC-210` skills-first workflow migration and command compatibility retirement

## 更新ルール (Update Rule)

このファイルは現在のスプリント、ブロッカー、次のアクションに必要な詳細のみを保持する。完了した作業は、アクティブに実行を形作らなくなったらアーカイブまたはリポジトリドキュメントに要約する。

## 最新の実行メモ (Latest Execution Notes)

- 2026-04-05: `#1213` の重複クリーンアップを継続し、`coding-standards` を削除せずベースラインのクロスプロジェクト規約レイヤーに絞り込んだ。React/UI は `frontend-patterns`、バックエンド/API は `backend-patterns` / `api-design` を指し、再利用可能な命名・可読性・不変性・コード品質期待のみを保持。
- 2026-04-05: `#1287` で公開 `v1.10.0` アーティファクトが古いままだった OpenCode リリースパスのパッケージング回帰ガードを追加。`tests/scripts/build-opencode.test.js` が `npm pack --dry-run` tarball に `.opencode/dist/index.js` とコンパイル済み plugin/tool エントリポイントを含むことを検証。
- 2026-04-05: `#829` 向けに `skills/agent-introspection-debugging` を land。ガイダンスファーストの ECC ネイティブ自己デバッグフレームワークで、偽の runtime 自動化ではなく capture → 分類 → 最小 contained 回復 → introspection レポート → 必要に応じ `verification-loop` / `continuous-learning-v2` へハンドオフ。
- 2026-04-05: 最新の直接ポート後に `main` の npm CI 破損を修正。`package-lock.json` が `globals` devDependency で `package.json` から乖離（`^17.1.0` vs `^17.4.0`）し `npm ci` が失敗。ロックファイルのみ更新し `npm ci --ignore-scripts` を検証。
- 2026-04-05: `#1221` の有用な discoverability 部分を直接ポート。`skills/hipaa-compliance/SKILL.md` を canonical `healthcare-phi-compliance` / `healthcare-reviewer` レーンへの薄い HIPAA エントリポイントとして追加し、両 healthcare privacy スキルを `security` インストールモジュールに配線。
- 2026-04-05: `#1222` の監査済み blockchain/web3 セキュリティレーンを 4 つの自己完結スキルとして直接ポート: `defi-amm-security`、`evm-token-decimals`、`llm-trading-agent-security`、`nodejs-keccak256`。`security` インストールモジュールに追加。
- 2026-04-05: `#1203` の有用な救済パスを `main` で完了。`skills/security-bounty-hunter`、`skills/api-connector-builder`、`skills/dashboard-builder` を ECC ネイティブ書き換えとして in-tree 化。元 PR は superseded 扱い。
- 2026-04-08: `#1327` で報告された command-export 回帰を修正。`agent.yaml` に canonical `commands:` セクションを復元し、`tests/ci/agent-yaml-surface.test.js` で YAML エクスポートと実 `commands/` の完全一致を強制。全テスト `1764/1764` 合格。

（それ以前の 2026-04-01〜04-05 の詳細メモは、上流 `WORKING-CONTEXT.md` の英語履歴と同一内容を日本語で保持。PR 番号・コミットハッシュ・パスは原文のまま。）
