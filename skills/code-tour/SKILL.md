---
name: code-tour
description: コードツアー（CodeTour `.tour`）の作成 — ペルソナ別ウォークスルーと実ファイル・行アンカー。オンボーディング、アーキテクチャ、PR、RCA、「この仕組みを説明」向け。CodeTour, onboarding tour, architecture walkthrough, PR tour.
origin: ECC
---

# コードツアー (Code Tour)

コードベースウォークスルー用の **CodeTour** `.tour` ファイルを作成し、実際のファイルとライン範囲に直接開きます。ツアーは `.tours/` にあり、アドホックな Markdown ノートではなく CodeTour 形式を対象としています。

良いツアーは特定の読者への物語：
- 彼らが何を見ているか
- なぜそれが重要か
- 次にどのパスを従うべきか

`.tour` JSON ファイルのみを作成。このスキルの一部としてソースコードを変更しないでください。

## 使用時期 (When to Use)

次の場合にこのスキルを使用：
- ユーザーがコードツアー、オンボーディングツアー、アーキテクチャウォークスルー、または PR ツアーを求める
- ユーザーが「X がどのように機能するかを説明」と言い、再利用可能なガイド付きアーティファクトを望む
- 新しいエンジニアまたはレビュアーのためのランプアップパス
- タスクはフラット概要ではなくガイド付きシーケンスでより良くサービスされる

例：
- 新しいメンテナのオンボーディング
- 1 サービスまたはパッケージのアーキテクチャツアー
- 変更ファイルにアンカーした PR レビューウォークスルー
- 失敗パスを示す RCA ツアー
- 信頼境界と主要チェックのセキュリティレビューツアー

## 使用しないとき (When NOT to Use)

| コードツアーの代わりに | 使用 |
| --- | --- |
| 一度限りの説明で十分 | 直接答える |
| ユーザーがプロのドキュメント、`.tour` アーティファクトではなく | `documentation-lookup` またはリポドキュメント編集 |
| タスクは実装またはリファクタリング | 実装作業を行う |
| 広範なコードベースオンボーディング（ツアーアーティファクトなし） | `codebase-onboarding` |

## ワークフロー (Workflow)

### 1. 発見 (Discover)

何か書く前にリポを探索：
- README とパッケージ/アプリエントリポイント
- フォルダ構造
- 関連するコンフィグファイル
- ツアーが PR フォーカスの場合は変更されたファイル

コードの形状を理解する前にステップを書き始めないでください。

### 2. 読者を推測 (Infer the Reader)

要求からペルソナと深さを決定。

| リクエストの形 | ペルソナ | 推奨される深さ |
| --- | --- | --- |
| 「オンボーディング」「新しい参加者」 | `new-joiner` | 9-13 ステップ |
| 「クイックツアー」「雰囲気チェック」 | `vibecoder` | 5-8 ステップ |
| 「アーキテクチャ」 | `architect` | 14-18 ステップ |
| 「この PR をツアー」 | `pr-reviewer` | 7-11 ステップ |
| 「なぜこれが壊れたか」 | `rca-investigator` | 7-11 ステップ |
| 「セキュリティレビュー」 | `security-reviewer` | 7-11 ステップ |
| 「この機能の仕組みを説明」 | `feature-explainer` | 7-11 ステップ |
| 「このパスをデバッグ」 | `bug-fixer` | 7-11 ステップ |

### 3. アンカーを読み取り検証 (Read and Verify Anchors)

すべてのファイルパスとラインアンカーは実在しなければならない：
- ファイルが存在することを確認
- ライン番号が範囲内であることを確認
- selection を使う場合、正確なブロックを検証
- ファイルが揮発的な場合、pattern ベースのアンカーを優先

ライン番号を推測しない。

### 4. `.tour` を書く (Write the `.tour`)

次に書き込む：

```text
.tours/<persona>-<focus>.tour
```

パスは決定的で読みやすく保つ。

### 5. 検証 (Validate)

終了前に：
- 参照されるすべてのパスが存在する
- すべてのラインまたは selection が有効
- 最初のステップが実ファイルまたはディレクトリにアンカーされている
- ツアーがファイルの羅列ではなく一貫した物語を語る

## ステップタイプ (Step Types)

### Content

控えめに使用、通常は締めのステップのみ：

```json
{ "title": "Next Steps", "description": "You can now trace the request path end to end." }
```

最初のステップを content のみにしない。

### Directory

読者をモジュールに向ける：

```json
{ "directory": "src/services", "title": "Service Layer", "description": "The core orchestration logic lives here." }
```

### File + line

デフォルトのステップタイプ：

```json
{ "file": "src/auth/middleware.ts", "line": 42, "title": "Auth Gate", "description": "Every protected request passes here first." }
```

### Selection

ファイル全体より 1 つのコードブロックが重要な場合：

```json
{
  "file": "src/core/pipeline.ts",
  "selection": {
    "start": { "line": 15, "character": 0 },
    "end": { "line": 34, "character": 0 }
  },
  "title": "Request Pipeline",
  "description": "This block wires validation, auth, and downstream execution."
}
```

### Pattern

正確なラインがずれる可能性がある場合：

```json
{ "file": "src/app.ts", "pattern": "export default class App", "title": "Application Entry" }
```

### URI

PR、issue、ドキュメントに有用な場合：

```json
{ "uri": "https://github.com/org/repo/pull/456", "title": "The PR" }
```

## 記述ルール: SMIG (Writing Rule: SMIG)

各 description は次に答える：
- **Situation**：読者が何を見ているか
- **Mechanism**：どのように動くか
- **Implication**：このペルソナにとってなぜ重要か
- **Gotcha**：賢い読者が見落としがちなこと

description はコンパクトで具体的、実際のコードに根ざしたものに保つ。

## ナラティブの形 (Narrative Shape)

タスクが明らかに別の形を必要としない限り、この弧を使う：
1. オリエンテーション
2. モジュールマップ
3. コア実行パス
4. エッジケースまたは gotcha
5. 締め / 次の一手

ツアーはインベントリではなくパスのように感じさせる。

## 例 (Example)

```json
{
  "$schema": "https://aka.ms/codetour-schema",
  "title": "API Service Tour",
  "description": "Walkthrough of the request path for the payments service.",
  "ref": "main",
  "steps": [
    {
      "directory": "src",
      "title": "Source Root",
      "description": "All runtime code for the service starts here."
    },
    {
      "file": "src/server.ts",
      "line": 12,
      "title": "Entry Point",
      "description": "The server boots here and wires middleware before any route is reached."
    },
    {
      "file": "src/routes/payments.ts",
      "line": 8,
      "title": "Payment Routes",
      "description": "Every payments request enters through this router before hitting service logic."
    },
    {
      "title": "Next Steps",
      "description": "You can now follow any payment request end to end with the main anchors in place."
    }
  ]
}
```

## アンチパターン (Anti-Patterns)

| アンチパターン | 修正 |
| --- | --- |
| フラットなファイル一覧 | ステップ間の依存関係で物語を語る |
| 汎用的な description | 具体的なコードパスまたはパターンを名指す |
| 推測されたアンカー | すべてのファイルとラインを先に検証 |
| クイックツアーにステップが多すぎる | 大胆に削る |
| 最初のステップが content のみ | 最初のステップを実ファイルまたはディレクトリにアンカー |
| ペルソナ不一致 | 汎用エンジニアではなく実際の読者向けに書く |

## ベストプラクティス (Best Practices)

- ステップ数はリポサイズとペルソナの深さに比例させる
- オリエンテーションには directory ステップ、実体には file ステップを使う
- PR ツアーでは変更ファイルを先にカバー
- モノレポではすべてをツアーせず関連パッケージにスコープ
- 要約ではなく、読者がこれから何ができるかで締める

## 関連スキル (Related Skills)

- `codebase-onboarding`
- `coding-standards`
- `council`
- 公式アップストリーム形式: `microsoft/codetour`
