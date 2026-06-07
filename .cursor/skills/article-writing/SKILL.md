---
name: article-writing
description: Write articles, guides, blog posts, tutorials, newsletter issues, and other long-form content in a distinctive voice derived from supplied examples or brand guidance. Use when the user wants polished written content longer than a paragraph, especially when voice consistency, structure, and credibility matter.
---

# 記事執筆 (Article Writing)

LLM が paste に平滑化するのではなく、視点を持つ実在の人物のように聞こえる長文を書く。

## 有効化タイミング (When to Activate)

- ブログ投稿、エッセイ、launch post、ガイド、チュートリアル、newsletter 号の起草
- メモ、トランスクリプト、リサーチを polished な記事へ変換
- 例から既存 founder、operator、brand voice に合わせる
- 既存長文の構成、ペース、evidence を締める

## コアルール (Core Rules)

1. 具体物から始める: artifact、example、output、anecdote、数値、screenshot、code。
2. 例の後に説明する。前ではない。
3. source voice が意図的に expansive でない限り、文は短く保つ。
4. 形容詞ではなく proof を使う。
5. 事実、credibility、customer evidence を捏造しない。

## Voice 扱い (Voice Handling)

特定 voice が必要なら先に `brand-voice` を実行し、その `VOICE PROFILE` を再利用する。
ユーザーが明示的に求めない限り、ここで第 2 の style-analysis pass を重複しない。

voice reference がなければ、鋭い operator voice をデフォルトとする: concrete、unsentimental、useful。

## 禁止パターン (Banned Patterns)

次があれば削除して書き直す:
- "In today's rapidly evolving landscape"
- "game-changer", "cutting-edge", "revolutionary"
- 単独 bridge としての "here's why this matters"
- 偽 vulnerability arc
- engagement を煽るだけの closing question
- 議論を進めない biography padding
- 要点を遅らせる generic AI throat-clearing

## 執筆プロセス (Writing Process)

1. 読者と目的を明確化する。
2. section ごとに 1 つの役割を持つ hard outline を組む。
3. section は proof、artifact、conflict、example から始める。
4. 次の文が space を稼ぐ場合のみ展開する。
5. templated、overexplained、self-congratulatory に聞こえる部分を削る。

## 構成ガイダンス (Structure Guidance)

### 技術ガイド (Technical Guides)

- 読者が得るもので開く
- 主要 section に code、command、screenshot、concrete output を使う
- soft recap ではなく actionable takeaway で締める

### エッセイ / 意見 (Essays / Opinion)

- tension、contradiction、specific observation から始める
- section ごとに 1 本の argument thread を保つ
- 意見は evidence に答える

### Newsletter (Newsletters)

- 最初の画面で実務をこなす
- diary filler を front-load しない
- scanability を改善する場合のみ section label を使う

## 品質ゲート (Quality Gate)

納品前:
- 事実 claim は提供 source で裏付けられている
- generic AI transition が消えている
- voice が提供例または合意した `VOICE PROFILE` と一致している
- 各 section が新しい何かを追加している
- formatting が intended medium に一致している
