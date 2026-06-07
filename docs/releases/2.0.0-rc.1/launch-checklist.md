# ECC v2.0.0-rc.1 ローンチチェックリスト (ECC v2.0.0-rc.1 Launch Checklist)

## リポジトリ (Repo)

- ローカル `main` が `origin/main` と同期していることを確認
- `docs/ECC-2.0-GA-ROADMAP.md` が現在の Linear milestone
  プランと Ito Markets ワークスペース配下の最新 `ECC Platform Roadmap` プロジェクトスナップショットを反映していることを確認
- `docs/HERMES-SETUP.md` が存在することを確認
- `docs/architecture/cross-harness.md` が存在することを確認
- このリリースディレクトリがコミットされていることを確認
- 最終 publish チェック前に `preview-pack-manifest.md` が公開リリース、Hermes、adapter、
  observability、publication、announcement artifact を列挙していることを確認
- `release-name-plugin-publication-checklist-2026-05-18.md` が現在の GitHub、npm、Claude、Codex、OpenCode、billing 面と
  まだ一致することを確認
- プライベート token、個人 docs、生ワークスペースエクスポートをリポジトリから除外

## リリース面 (Release Surface)

- package、plugin、marketplace、OpenCode、agent metadata が `2.0.0-rc.1` のままであることを確認
- `ecc2/Cargo.toml` が rc.1 で `0.1.0` のままであることを確認；`ecc2/` は alpha control-plane scaffold のまま
- GitHub release、npm publish、plugin 提出、announcement 投稿の前に `publication-readiness.md` を新鮮な evidence で完了
- owner 承認とライブ URL readback 記録後に `npm run release:approval-gate -- --format json` を実行；
  publish、upload、social、outbound アクションの前に ready true を返すこと
- GitHub prerelease 作成、npm 公開、Claude plugin tag push、
  Codex marketplace path 記録、公開コピー投稿の前にリリース name/plugin publication チェックリストを再実行
- 最終 evidence レビューに `publication-evidence-2026-05-17.md` と
  `operator-readiness-dashboard-2026-05-17.md` を含め、
  正確なリリースコミットから publish 向けチェックを再実行
- 専用 release-version PR でリリース metadata を1回更新
- ルートテストスイートを実行
- `cd ecc2 && cargo test` を実行

## コンテンツ (Content)

- `x-thread.md` から X スレッドを公開
- `linkedin-post.md` から LinkedIn ドラフトを公開
- 長文は `article-outline.md` を使用
- sponsor、partner、consulting、conference、podcast、GitHub
  Discussion コピーは `partner-sponsor-talks-pack.md` 経由でルーティング
- 30〜60秒の proof-of-work クリップを1本記録
- `ECC_VIDEO_SOURCE_ROOT` と `ECC_VIDEO_RELEASE_SUITE_ROOT` 設定後に
  `npm run release:video-suite -- --format json` でリリース動画スイートを検証
- `video-suite-production.md` を実際のプライマリローンチ
  レンダー、タイムライン、キャプション、self-eval gate と整合

## デモアセット提案 (Demo Asset Suggestions)

- Hermes と ECC を並べて
- リポジトリからリリース docs を生成またはレビュー
- brief から post、checklist へ移動するワークフロー
- alpha フレーミング付き `ecc2/` dashboard または session 面

## メッセージング (Messaging)

次のような表現を使う：

- "release candidate"
- "sanitized operator stack"
- "cross-harness operating system for agentic work"
- "ECC is the reusable substrate; Hermes is the operator shell"
- "private/local integrations land after sanitization"

明示的な人間の承認なしに sponsor、partner、consulting、conference、podcast outreach を送らない。
