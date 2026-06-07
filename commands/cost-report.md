---
description: cost-tracker SQLite データベースからローカルの Claude Code コストレポートを生成する。
argument-hint: [csv]
---

# コストレポート (Cost Report)

ローカルのコスト追跡データベースを照会し、日別・プロジェクト別・ツール別・セッション別の支出レポートを提示する。このコマンドは、cost-tracking hook または plugin が既に `~/.claude-cost-tracker/usage.db` に usage 行を書き込んでいることを前提とする。

## このコマンドの動作 (What This Command Does)

1. `sqlite3` が利用可能か確認する。
2. `~/.claude-cost-tracker/usage.db` が存在するか確認する。
3. `usage` テーブルに対して集計クエリを実行する。
4. コンパクトなレポートを提示する。引数が `csv` の場合は直近行を CSV でエクスポートする。

## 前提条件 (Prerequisites)

データベースはローカルの cost tracker で投入されている必要がある。ファイルがない場合は tracker が未設定であることを伝え、信頼できる Claude Code cost-tracking hook/plugin のインストールまたは有効化を先に提案する。

```bash
test -f ~/.claude-cost-tracker/usage.db && echo "Database found" || echo "Database not found"
```

## サマリークエリ (Summary Query)

```bash
sqlite3 -header -column ~/.claude-cost-tracker/usage.db "
  SELECT
    ROUND(COALESCE(SUM(CASE WHEN date(timestamp) = date('now') THEN cost_usd END), 0), 4) AS today_cost,
    ROUND(COALESCE(SUM(CASE WHEN date(timestamp) = date('now', '-1 day') THEN cost_usd END), 0), 4) AS yesterday_cost,
    ROUND(COALESCE(SUM(cost_usd), 0), 4) AS total_cost,
    COUNT(*) AS total_calls,
    COUNT(DISTINCT session_id) AS sessions
  FROM usage;
"
```

## プロジェクト別内訳 (Project Breakdown)

```bash
sqlite3 -header -column ~/.claude-cost-tracker/usage.db "
  SELECT project, ROUND(SUM(cost_usd), 4) AS cost, COUNT(*) AS calls
  FROM usage
  GROUP BY project
  ORDER BY cost DESC;
"
```

## ツール別内訳 (Tool Breakdown)

```bash
sqlite3 -header -column ~/.claude-cost-tracker/usage.db "
  SELECT tool_name, ROUND(SUM(cost_usd), 4) AS cost, COUNT(*) AS calls
  FROM usage
  GROUP BY tool_name
  ORDER BY cost DESC;
"
```

## 直近7日間 (Last Seven Days)

```bash
sqlite3 -header -column ~/.claude-cost-tracker/usage.db "
  SELECT date(timestamp) AS date, ROUND(SUM(cost_usd), 4) AS cost, COUNT(*) AS calls
  FROM usage
  GROUP BY date(timestamp)
  ORDER BY date DESC
  LIMIT 7;
"
```

## CSV エクスポート (CSV Export)

ユーザーが `/cost-report csv` を要求した場合、明示的なカラム一覧で直近の usage 行をエクスポートする:

```bash
sqlite3 -csv -header ~/.claude-cost-tracker/usage.db "
  SELECT timestamp, project, tool_name, input_tokens, output_tokens, cost_usd, session_id, model
  FROM usage
  ORDER BY timestamp DESC
  LIMIT 100;
"
```

## レポート形式 (Report Format)

応答は次の形式で整形する:

1. サマリー: 今日、昨日、合計、呼び出し数、セッション数。
2. プロジェクト別: 総コスト順のプロジェクト。
3. ツール別: 総コスト順のツール。
4. 直近7日間: 日付、コスト、呼び出し数。

1ドル未満は小数点以下4桁。生トークンからこのコマンドで料金を推定しない。tracker が書き込んだ事前計算の `cost_usd` 値に依存する。

## 出典 (Source)

コミュニティ PR #1304（`MayurBhavsar`）から salvage。
