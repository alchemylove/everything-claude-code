# Stale PR Salvage 台帳 (Stale PR Salvage Ledger)

この台帳は、stale、conflicted、または closed な PR から回収した有用な作業を記録します。ルールは単純です: キュー整理は stale PR を閉じますが、有用な作業は捨てません。メンテナーは closed diff を検査し、互換部分を新しいブランチで移植し、ソース PR にクレジットを付けます。

## 分類状態 (Classification States)

| 状態 (State) | 意味 (Meaning) |
| --- | --- |
| Salvaged | 有用な作業がメンテナー PR 経由で現在の `main` に移植された。 |
| Already present | 移植前に現在の `main` にすでに有用な作業が含まれていた。 |
| Superseded | 現在の `main` が同じ問題を別の方法で解決した。 |
| Skipped | PR が偶発的、広すぎる、危険、または移植に低シグナルすぎた。 |
| Translator/manual review | コンテンツに有用性がある可能性があるが、import 前に人間の言語/ドメインレビューが必要。 |

## 現在の Main に Salvage 済み (Salvaged Into Current Main)

| ソース PR (Source PR) | 元の貢献 (Original contribution) | Salvage 結果 (Salvage result) |
| --- | --- | --- |
| #1232 | `skill-scout` search-before-creating workflow | 2026-05-12 の cost/skill-scout メンテナーパスで salvage。現在のリポジトリ文言、外部ソース vetting、stale catalog-count 編集なし。 |
| #1304 | Cost tracking skill と `/cost-report` command | 2026-05-12 の cost/skill-scout メンテナーパスで salvage。現在の command/skill 規約、stale ハードコード model pricing なし。 |
| #1309 | Trading/community project material | #1761 で中立な community-project README 一覧として salvage。 |
| #1310 | Django reviewer、build resolver、Celery async task guidance | 2026-05-12 の Django/Celery メンテナーパスで salvage。現在の catalog 件数と軽微な例の整理。 |
| #1322 | Vietnamese README 翻訳 | #1764 で `docs/vi-VN/README.md` と selector 更新として salvage。 |
| #1325 | Quarkus framework guidance、Java agent、localization material | #1771 と #1803 で salvage。stale な広い docs/count 編集はコピーせず。 |
| #1326 | Angular developer skill と rules | #1763 で現在の skill、rules、install wiring、catalog 更新で salvage。 |
| #1328 | Continuous-learning Windows UTF-8 stdout 修正 | #1761 で salvage。 |
| #1329 | Plugin install 検出の hardening | #1761 で現在の harness audit 検出サポート経由で salvage。 |
| #1334 | Windows desktop E2E skill | #1762 で install、package、catalog wiring で salvage。 |
| #1352 | Qwen install target | #1738 で現在の Qwen install target 経由で salvage。 |
| #1413 | Network と homelab skill/agent | #1729、#1731、#1745、#1778 で salvage。 |
| #1414 | F# rules、reviewer agent、testing skill | #1770 で現在の install manifest、検出テスト、catalog wiring で salvage。 |
| #1429 | JoyCode install target | #1737 で現在の JoyCode install target 経由で salvage。 |
| #1467 | Scientific skill と OpenCode discovery 作業 | #1740 で有用な USPTO と gget 部分を salvage。stale 生成 claim はコピーせず。 |
| #1478 | HarmonyOS/ArkTS rules、resolver agent、CLAUDE 例 | #1769 で現在の install wiring で salvage。stale `ecc2` session/TUI 編集は持ち越さず。 |
| #1493 | SessionStart context scoping | #1774 で現在の hook 意味論とテストで salvage。 |
| #1498 | PRD planning flow | #1777 で salvage。 |
| #1504 | Statusline/context monitor hook | #1776 で現在の hook manifest 構造とテストで salvage。 |
| #1528/#1529/#1547 | Astraflow と UModelVerse provider サポート | #1775 で現在の provider wiring と防御的 tool-call パースで salvage。 |
| #1558 | `agentic-os` skill | #1772 で salvage。 |
| #1559 | `error-handling` skill | #1772 で salvage。 |
| #1566 | Agent architecture audit skill | #1772 で salvage。 |
| #1578 | OpenCode file-probe hardening | #1773 で salvage。 |
| #1603 | `plan-orchestrate` skill | #1766 で現在の manifest/catalog wiring で salvage。 |
| #1658 | Code-reviewer false-positive 抑制 | 2026-05-12 の code-reviewer メンテナーパスで salvage。現在の review-agent 文言、HIGH/CRITICAL 用 proof gate、一般的 false-positive 除外、回帰テスト。 |
| #1659 | Frontend design direction と interface-polish skill | 2026-05-12 の frontend-design メンテナーパスで salvage。canonical `skills/` レイアウトと現在の ECC frontend guidance。公式 `frontend-design` skill は `anthropics/skills` から install するリポジトリ guardrail を維持。 |
| #1674 | Production audit skill | supply-chain/privacy レビューと書き直し後 #1732 で salvage。 |
| #1687 | zh-CN localization sync | #1746-#1752 で大きな安全サブセットを salvage。残りは translator/manual review が必要。 |
| #1694 | Portfolio curation | #1723 と #1724 で有用な絞った curation 更新を salvage。 |
| #1695 | Russian README 翻訳 | #1722 で移植。 |
| #1697 | Saved LLM selector config | #1720 の provider config/tool schema 作業の一部として salvage。 |
| #1699 | Windows post-edit-format path guard | #1719 で移植。 |
| #1700 | Provider tool serialization | #1720 で移植。 |
| #1705/#1780 | Production UI motion system | #1772、#1781、#1782 で salvage。マージ前に例を修正。 |
| #1713 | Swift 言語サポート | #1721 で移植。 |
| #1715 | CI personal-path validator hardening | #1717 の CI validator hardening 経由で移植。 |
| #1727 | MySQL patterns skill | #1733 で salvage。 |
| #1757 | Machine-learning engineering workflow | #1758 で salvage、#1759 で調整。 |

## 2026-05-12 Gap Pass

初期の stale-closure 台帳は P0 整理コホートと最大の salvage ブランチをカバーしました。2026-05-11 に閉じた PR のフォローアップ gap pass で、`main` にすでにあった、またはまだ移植価値のある追加項目が見つかりました。

| ソース PR (Source PR) | 処分 (Disposition) |
| --- | --- |
| #1310 | `agents/django-reviewer.md`、`agents/django-build-resolver.md`、`skills/django-celery/SKILL.md` がまだ欠けていることを確認後、Django/Celery メンテナーブランチで移植。 |
| #1325 | 有用な Quarkus material は #1771 と #1803 ですでに保持。現在の `main` に Quarkus rules/skills と Java reviewer/build-resolver surface あり。 |
| #1360 | `skills/security-bounty-hunter/` としてすでに存在。 |
| #1414 | 有用な F# サポートは #1770 ですでに保持。現在の `main` に F# rules、reviewer agent、testing skill、install wiring、検出テストあり。 |
| #1415 | `skills/vite-patterns/` としてすでに存在。 |
| #1478 | 有用な HarmonyOS/ArkTS サポートは #1769 ですでに保持。現在の `main` に ArkTS rules、resolver agent、CLAUDE 例、install wiring あり。 |
| #1438 | `skills/ui-to-vue/` としてすでに存在。 |
| #1504 | 永続 salvage 表で #1776 にすでにマップ済み。 |
| #1508 | `skills/fastapi-patterns/` と `agents/fastapi-reviewer.md` としてすでに存在。 |
| #1563/#1564/#1565 | Translator/manual review: zh-TW、tr、pt-BR README sync に有用な localization 更新がある可能性があるが、import 前に言語オーナーが stale README/version/count テキストをレビューする必要あり。 |
| #1567 | `scripts/hooks/gateguard-fact-force.js` の現在の GateGuard subagent file-gate bypass としてすでに存在。Bash gate は保持、回帰テストは `tests/hooks/gateguard-fact-force.test.js`。 |
| #1570 | 公開 `llm.prompt` import、キーワードベース `PromptBuilder` 構築、template registry ヘルパーとしてすでに存在。現在のテストは `tests/conftest.py` で `unit` マーカーを登録。 |
| #1584 | `scripts/hooks/desktop-notify.js` の iTerm2 ネイティブデスクトップ通知 fast path としてすでに存在。multiplexer は `osascript` にフォールバック。 |
| #1589 | `scripts/ci/validate-workflow-security.js` の quoted `actions/checkout` 検出と二重/単一引用符回帰テストとしてすでに存在。 |
| #1594 | HTTP 400/401/403 プローブ応答を到達可能/auth-gated と扱う HTTP MCP 到達性処理としてすでに存在。hook テストあり。 |
| #1597 | README、AGENTS、zh-CN docs、`.claude-plugin/plugin.json`、`.claude-plugin/marketplace.json` の catalog-count 検証としてすでに存在。 |
| #1602 | 新規利用を `continuous-learning-v2` にルーティングし v1 surface をアーカイブ保持する `continuous-learning` v1 非推奨としてすでに存在。 |
| #1603 | 有用な `/plan-orchestrate` 作業は #1766 で現在の package/catalog メタデータとともにすでに保持。 |
| #1604 | Skipped: Windows ドラッグアンドドロップローカル installer は直接コピーして `git pull` を実行。現在の管理 installer/profile flow の方が安全でこれに取って代わる。 |
| #1609 | Translator/manual review: Persian README 翻訳は有用な可能性があるが、import 前に言語レビューと現在の catalog/version 更新が必要。 |
| #1613 | `rules/web/hooks.md` の `tsc --incremental` とタイムアウト付き PostToolUse 例としてすでに存在。 |
| #1631 | `scripts/hooks/suggest-compact.js` と `tests/hooks/hooks.test.js` にすでに存在。現在のコードは `CLAUDE_SESSION_ID` にフォールバックする前に stdin JSON から `session_id` を読む。 |
| #1648 | `src/llm/providers/claude.py` にすでに存在。現在の Claude provider はすべての text と tool-use content block を収集し、`tests/test_claude_provider.py` でカバー。 |
| #1658 | false-positive proof gate と一般的 skip リストがまだ欠けていることを確認後、code-reviewer メンテナーブランチで移植。 |
| #1693 | `skills/redis-patterns/` としてすでに存在。 |

## すでに存在または Superseded (Already Present Or Superseded)

| ソース PR (Source PR) | 処分 (Disposition) |
| --- | --- |
| #1306 | Hook bug 回避策は `main` に `docs/hook-bug-workarounds.md` としてすでに存在。 |
| #1318 | Gemini agent adaptation utility は現在の `main` にすでに存在。 |
| #1323 | Hook config 更新は現在の `main` にすでに存在。 |
| #1337 | Catalog count 更新は現在の catalog-count sync に superseded。 |
| #1631 | `suggest-compact` stdin `session_id` 分離は hook テスト付きで現在の `main` にすでに存在。 |
| #1608 | 危険な dashboard document/terminal open 処理は safe runtime helper と project-bound document opening 経由で現在の `main` にすでに存在。 |
| #1678 | Windows MCP `.cmd`/`.bat` フォールバックは現在の health-check テスト付きで現在の `main` にすでに存在。 |
| #1682/#1701 | Strategic compact hook-path 修正は直接マージまたは現在の docs 修正に superseded。 |
| JARVIS #4/#5/#6 | Stale で失敗する dependency のみの PR。将来の dependency 状態は Dependabot で再生成すべき。 |

## 2026-05-18 Owner-Wide キュー整理 (2026-05-18 Owner-Wide Queue Cleanup)

ECC リリースリポジトリはすでにクリーンでしたが、owner 全体の `gh search` スイープで古い公開/非公開プロジェクトに stale キューが見つかりました。整理により stale dependency-bot PR 24件と legacy payments/0EM roadmap issue 72件を閉じ、最後の stale/generated/conflicting/test PR 9件と legacy/outreach/placeholder issue 5件を閉じました。`affaan-m` owner 名前空間はライブ `gh search` で open PR 0、open issue 0 です。詳細な before/after 証拠と最終キュー disposition は `docs/releases/2.0.0-rc.1/owner-queue-cleanup-2026-05-18.md` に記録されています。

| スコープ (Scope) | 処分 (Disposition) |
| --- | --- |
| `stoictradingAI`、`Behavioral_RL`、`dprc-autotrader-v2`、`x-algorithm-score`、`polycule-secure`、`pragmAItism_defAInce` の Dependabot PR | Stale 生成 dependency bump として Skipped。まだ必要なら現在のベースから再生成。 |
| `payments0-api`、`payments0-sdk`、`agent-payments-gateway`、`0EM_Frontend`、`0em-payments-dashboard`、`yield-optimizer` の legacy issue | ECC Tools native-payments、hosted analysis、billing-readback、Linear/project roadmap レーンに superseded。 |
| PR 閉鎖で触れた archived repo | `stoictradingAI`、`dprc-autotrader-v2`、`polycule-secure`、`pragmAItism_defAInce` は stale PR 閉鎖後に archived 状態に復元。 |
| 最終 PR/issue スイープ | 残りの生成 ECC bundle、stale Cloudflare rename PR、stale README-card PR、test/noise PR、公開 outreach issue、空 placeholder issue を閉鎖。閉鎖前に `dexploy#25` の所見を Linear `ITO-62` に保持。 |

## Skipped

| ソース PR (Source PR) | 理由 (Reason) |
| --- | --- |
| #1308 | Stale zh-CN sync は現在のツリー状態を巻き戻し/削除しすぎる。具体的 selector-link 修正はすでに存在。 |
| #1320 | Package-manager 削除は現在の npm/pnpm/yarn/bun CI ポリシーと競合。 |
| #1341 | 安全な focused salvage 単位のない非常に大きく低シグナルな生成変更。 |
| #1416/#1465 | focused 貢献のない偶発的 fork-sync PR。 |
| #1475 | 1行の Gemini CLI bridge アイデアは古すぎて仕様不足で安全に移植できない。 |
| #1604 | ドラッグアンドドロップ Windows installer は現在の管理 installer をバイパスし、直接広いコピーとローカル install script からの `git pull` を実行。 |

## 残りの手動レビューバックログ (Remaining Manual-Review Backlog)

残りの plausibly 有用なバックログは、言語オーナーレビューなしに auto-port すると危険な翻訳/localization 作業です。この tail は Linear ITO-55 に紐づき、リリースブロッキング salvage タスクではありません。リリース作業はバックログが記録され blind import から除外されていることだけを検証すべきです:

- #1687 zh-CN localization tail
- #1609 Persian README 翻訳
- #1563 zh-TW README sync
- #1564 Turkish README sync
- #1565 pt-BR README sync

処理ルール:

1. これらの PR は translator/manual review に置く。
2. 将来の作業は surface ごとに分割: agent、command、トップレベル docs、release と count surface、次に skill。
3. 古い version や catalog-count 事実を持つ stale トップレベル docs を import しない。
4. 元の作者が現在の rebase で戻る場合以外、古い PR を reopen しない。メンテナー側 salvage は attribution 付きの新しいブランチで行う。

## 将来の整理ルール (Future Cleanup Rule)

stale/conflicted PR 整理バッチごとに:

1. キューポリシーに基づき PR を閉じるかコメントする。
2. ソース PR をこの台帳または日付付き後続台帳に追加する。
3. salvaged、already present、superseded、skipped、translator/manual review に分類する。
4. 有用なら新しいメンテナーブランチで小さな互換スライスを移植する。
5. メンテナー PR 本文でソース PR と作者にクレジットする。
