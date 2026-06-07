---
name: recursive-decision-ledger
description: Use when the user asks for repeated rollouts, marked decision processes, high-dimensional search, stochastic optimization, local-optima exploration, ensemble comparison, or recursive reasoning with a visible evidence trail.
origin: ECC
tools: Read, Write, Edit, Bash, Grep, Glob
---

# Recursive Decision Ledger (Recursive Decision Ledger)

user が repeated trial や「Prime Gauss」型 recursive prompting でより深い計算を強制しようとするときにこの skill を使用する。有用な部分（repeated trial、prior memory、fresh information、explicit mark）は保持し、不安全な部分（ループが確実性を証明するふり）は除去する。

## Ledger 契約 (Ledger Contract)

各 rollout は次を記録:

- rollout id と timestamp
- prior accepted winner と prior watchlist
- 取り込んだ fresh information
- search space サイズ
- 使用した model family または heuristic
- trial count と effective trial count
- top candidate
- decision mark
- prior ledger との coherence mark
- promotion gate 結果

append-only ledger は JSONL、human summary は Markdown を優先。

## Rollout ループ (Rollout Loop)

1. prior ledger を読み込む。
2. time-step zero で新情報を取得する。
3. bounded search を実行する。
4. 各 candidate に mark: accept、watch、reject、decay watch、needs replay。
5. winner を prior winner と latest marked rollout と比較する。
6. drift、tail risk、stale data、failed replay で prior mark が無効なら downgrade する。
7. 要約前に artifact を append する。

## Coherence Mark (Coherence Mark)

簡潔な coherence mark を含める:

```text
Ensemble matches prior winner: true
Recursive matches prior winner: false
Latest rollout match: true
Live promotion allowed: false
Reason: replay and freshness gates not satisfied
```

## Promotion ルール (Promotion Rules)

trading、capital allocation、production deploy、migration、destructive op では、recursive confidence は approval ではない。

user が live action を明示承認し、repo/service gate が対応するまで、paper、dry-run、read-only、preview、staged mode をデフォルトとする。

次を満たすときのみ promote:

- candidate が選定 metric で prior accepted winner を上回る
- correctness と replay check が pass
- risk limit が明示されている
- evidence が durable
- 必要なら user が live step を承認している

## サマリ形式 (Summary Shape)

ドラマより decision を先に:

```text
Rollout 15 complete. The prior winner still holds, but edge deteriorated 17%.
Status: watch, not live. Next gate: 20 replay fills with fresh orderbook age
below threshold.
```
