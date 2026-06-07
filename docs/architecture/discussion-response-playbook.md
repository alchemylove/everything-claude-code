# Discussion 応答プレイブック (Discussion Response Playbook)

このプレイブックは GitHub Discussions を PR、issue、Linear 作業、リリース evidence と
同じ運用 queue に変換します。operator ガイドであり、すべての情報スレッドに
公開返信が必要という約束ではありません。

## 監査ループ (Audit Loop)

リリース前、主要 merge batch 後、Linear ITO-59 更新時に次を実行：

```bash
npm run discussion:audit -- --json
node scripts/platform-audit.js --json
```

queue が current なのは次のときのみ：

- discussion fetch エラーが説明または修正されている；
- サポート系 discussion カテゴリで `needsMaintainerTouch` がゼロ；
- 回答可能な Q&A discussion に accepted answer があるか明確な routing
  note がある；および
- プロダクトスコープのスレッドが GitHub issue、Linear issue、roadmap
  行、または明示的 deferral にリンクされている。

announcement、reference、show-and-tell、maintainer 作成の更新などの
情報スレッドは、response debt にならず visible のままでよい。

## カテゴリ (Categories)

| カテゴリ (Category) | ルーティング (Route) | 必須 readback (Required readback) |
| --- | --- | --- |
| プロダクトサポートまたはインストール混乱 (Product support or install confusion) | 正確な command/doc path で返信; 修正完了時に Q&A で accepted answer をマーク | 該当時は Discussion URL と accepted-answer URL |
| バグ報告 (Bug report) | 最小 repro、version、harness、log を依頼; 再現可能なら GitHub issue を作成またはリンク | Issue URL または延期理由 |
| 機能リクエスト (Feature request) | 望ましい outcome を認め、最も近い roadmap issue にリンク; スコープ化されていない限りコミットを暗示しない | Linear/GitHub roadmap リンク |
| セキュリティ懸念 (Security concern) | exploit 詳細と secret を private channel へ; 公開返信は短く非運用的に保つ | private escalation note と公開 safety 返信 |
| リリースまたは billing 質問 (Release or billing question) | release URL ledger と publication-readiness gate から回答; 未公開 URL、billing readiness、plugin 利用可能性を主張しない | evidence artifact または blocker リンク |
| Show-and-tell、reference、announcement | 直接質問または product-scope シグナルがない限り情報として残す | 有用なら任意の roadmap リンク |
| 古いまたは終了スレッド (Stale or concluded thread) | 現状を要約し durable doc/issue にリンク; 低シグナルスレッドの復活を避ける | closure note または明示的 no-action 理由 |

## テンプレート (Templates)

### 公開サポート (Public Support)

報告ありがとうございます。現在サポートされている path は次のとおりです:

```bash
<command>
```

関連 doc は `<doc path or URL>` です。セットアップが一致しない場合は、harness、OS、package manager、正確なエラーテキストを返信してください。

### Maintainer 調整 (Maintainer Coordination)

discussion queue で失われないよう、`<issue or Linear key>` に routing します。次の判断は `<specific decision>` です。それが land するまで、サポートされる workaround は `<workaround or "none">` です。

### 古いまたは終了 (Stale Or Concluded)

このスレッドは `<doc/issue/release>` により解決済みまたは置き換えられたように見えます。履歴のため visible のまま残しますが、アクティブな support queue item ではありません。新しい repro 詳細は `<issue/discussion path>` へ送ってください。

### リリース告知 (Release Announcement)

現在のリリースステータスは `<rc/beta/GA state>` です。live URL は `docs/releases/2.0.0-rc.1/release-url-ledger-2026-05-18.md` に記録されています。そこで pending とマークされたものは、まだ出荷済みとして告知しないでください。

### セキュリティエスカレーション (Security Escalation)

指摘ありがとうございます。公開スレッドに exploit 手順、token、顧客データ、secret 値を投稿しないでください。security response path 経由で routing し、公開スレッドは安全なステータス更新に限定します。

## 結果の記録 (Recording Outcomes)

高シグナル discussion ごとに、次のいずれかの結果を記録：

- 公開返信し accepted answer を read back；
- GitHub issue または Linear issue にリンク；
- security response path に routing；
- informational と分類；または
- 理由付きで明示的に defer。

batch 終了時に ITO-59 にサマリーをミラーし、次の operator dashboard または publication evidence refresh に件数を含める。
