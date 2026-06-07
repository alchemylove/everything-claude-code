---
name: deep-research
description: Multi-source deep research using firecrawl and exa MCPs. Searches the web, synthesizes findings, and delivers cited reports with source attribution. Use when the user wants thorough research on any topic with evidence and citations.
---

# 深掘り調査 (Deep Research)

firecrawl と exa MCP ツールを使い、複数の Web ソースから徹底的で引用付きの調査レポートを作成する。

## 有効化タイミング (When to Activate)

- ユーザーが任意のトピックを深く調査してほしいとき
- 競合分析、技術評価、市場規模の算出
- 企業、投資家、技術のデューデリジェンス
- 複数ソースからの統合が必要な質問
- ユーザーが「research」「deep dive」「investigate」「what's the current state of」と言ったとき

## MCP 要件 (MCP Requirements)

少なくともいずれか:
- **firecrawl** — `firecrawl_search`, `firecrawl_scrape`, `firecrawl_crawl`
- **exa** — `web_search_exa`, `web_search_advanced_exa`, `crawling_exa`

両方揃えるとカバレッジが最良。`~/.claude.json` または `~/.codex/config.toml` で設定。

## ワークフロー (Workflow)

### ステップ 1: 目的を理解する (Step 1: Understand the Goal)

1〜2つの簡潔な確認質問:
- 「目的は何ですか — 学習、意思決定、執筆のどれですか？」
- 「特定の角度や深さの希望はありますか？」

ユーザーが「とにかく調べて」と言った場合 — 妥当なデフォルトで先に進む。

### ステップ 2: 調査を計画する (Step 2: Plan the Research)

トピックを3〜5の調査サブ質問に分解。例:
- トピック: "Impact of AI on healthcare"
  - 今日の医療における主な AI 応用は何か？
  - どのような臨床アウトカムが測定されているか？
  - 規制上の課題は何か？
  - この分野をリードする企業はどこか？
  - 市場規模と成長軌道はどうか？

### ステップ 3: マルチソース検索を実行 (Step 3: Execute Multi-Source Search)

各サブ質問について、利用可能な MCP ツールで検索:

**firecrawl 使用時:**
```
firecrawl_search(query: "<sub-question keywords>", limit: 8)
```

**exa 使用時:**
```
web_search_exa(query: "<sub-question keywords>", numResults: 8)
web_search_advanced_exa(query: "<keywords>", numResults: 5, startPublishedDate: "2025-01-01")
```

**検索戦略:**
- サブ質問ごとに2〜3の異なるキーワードバリエーションを使用
- 一般検索とニュース重視のクエリを混ぜる
- 合計15〜30のユニークソースを目標
- 優先度: 学術・公式・信頼できるニュース > ブログ > フォーラム

### ステップ 4: 重要ソースを深読み (Step 4: Deep-Read Key Sources)

最も有望な URL について全文を取得:

**firecrawl 使用時:**
```
firecrawl_scrape(url: "<url>")
```

**exa 使用時:**
```
crawling_exa(url: "<url>", tokensNum: 5000)
```

深さのため3〜5の重要ソースを全文で読む。検索スニペットのみに頼らない。

### ステップ 5: 統合してレポートを書く (Step 5: Synthesize and Write Report)

レポート構成:

```markdown
# [Topic]: Research Report
*Generated: [date] | Sources: [N] | Confidence: [High/Medium/Low]*

## エグゼクティブサマリー (Executive Summary)
[3-5 sentence overview of key findings]

## 1. [First Major Theme]
[Findings with inline citations]
- Key point ([Source Name](url))
- Supporting data ([Source Name](url))

## 2. [Second Major Theme]
...

## 3. [Third Major Theme]
...

## 主要な要点 (Key Takeaways)
- [Actionable insight 1]
- [Actionable insight 2]
- [Actionable insight 3]

## ソース (Sources)
1. [Title](url) — [one-line summary]
2. ...

## 方法論 (Methodology)
Searched [N] queries across web and news. Analyzed [M] sources.
Sub-questions investigated: [list]
```

### ステップ 6: 配信 (Step 6: Deliver)

- **短いトピック**: チャットに全文レポートを投稿
- **長いレポート**: エグゼクティブサマリー + 主要な学びを投稿し、全文はファイルに保存

## サブエージェントによる並列調査 (Parallel Research with Subagents)

広いトピックでは、Claude Code の Task ツールで並列化:

```
Launch 3 research agents in parallel:
1. Agent 1: Research sub-questions 1-2
2. Agent 2: Research sub-questions 3-4
3. Agent 3: Research sub-question 5 + cross-cutting themes
```

各エージェントが検索・ソース読み取り・所見返却。メインセッションが最終レポートに統合。

## 品質ルール (Quality Rules)

1. **すべての主張にソースが必要。** 出典のない断言はしない。
2. **相互参照する。** 1ソースだけの場合は未検証と明記。
3. **新しさが重要。** 過去12ヶ月のソースを優先。
4. **ギャップを認める。** サブ質問で良い情報が見つからなければそう述べる。
5. **幻覚しない。** わからなければ「insufficient data found」と言う。
6. **事実と推論を分離。** 推定、予測、意見を明確にラベル付け。

## 例 (Examples)

```
"Research the current state of nuclear fusion energy"
"Deep dive into Rust vs Go for backend services in 2026"
"Research the best strategies for bootstrapping a SaaS business"
"What's happening with the US housing market right now?"
"Investigate the competitive landscape for AI code editors"
```
