# Evaluator RAG プロトタイプ (Evaluator RAG Prototype)

ECC 2.0 は、ユーザーの Claude、Codex、OpenCode、dmux、Zed、または
ターミナルセットアップを盲目的に変更せずに実作業から学べる自己改善 harness loop が必要です。このプロトタイプはその loop 向けの最小 read-only artifact セットを定義します。

フィクスチャセットは
[`examples/evaluator-rag-prototype/`](../../examples/evaluator-rag-prototype/) にあります。
2026年5月の stale-PR cleanup と salvage lane から始まりました。その lane には
実入力、受け入れられた作業、拒否された作業があります。コーパスには現在
billing/Marketplace readiness シナリオも含まれ、ローンチコピーが dry-run リリース evidence や roadmap intent をライブ billing 状態として扱わないようにします。
CI-failure diagnosis シナリオは、エージェントが赤チェックの修正を提案する前に
必要な log-first ワークフローを追加します。Harness-config quality シナリオは
セットアップガイダンスを変更する前に MCP、plugin、hook、command、agent、adapter 推奨を
adapter matrix に結び付けます。AgentShield policy
exception シナリオは SARIF/report evidence、owner
フィールド、expiry 状態、remediation 対 exception 判断で security exception をゲートします。
Skill-quality evidence シナリオは、skill 修正を昇格する前に観測された失敗または feedback evidence、
動作例、reference-set ギャップ、validation command を要求します。Deep-analyzer evidence シナリオは、
リポジトリまたは commit 分析振る舞いを変更する前に analyzer
corpus case、expected-output 比較、risk-taxonomy 証明を要求します。

## 参照圧力 (Reference Pressure)

- Meta-Harness: scenario spec、verifier 結果、昇格 playbook で harness 自体を実験として扱う。
- Autocontext: インストール済み agent asset を変更する前に trace、report、artifact、再利用可能な改善を保存。
- Claude HUD: context、tools、todos、agent activity、checks、risk を露出し、
  evaluator が事後に run を判断できるようにする。
- Hermes Agent: skill、memory、scheduler 的 follow-up、terminal
  gateway 振る舞いをローカルコマンドを隠さず明示する。
- dmux、Orca、Superset、Ghast: worktree/session 状態を保持し、並列
  エージェント作業を比較、再開、きれいにクローズできるようにする。
- ECC Tools: evaluator finding を GitHub を氾濫させず PR comment、check run、Linear
  backlog item にルーティング。

## Artifact 契約 (Artifact Contract)

すべての evaluator/RAG run は verifier が playbook を昇格するまで read-only です。

| Artifact | Purpose | Fixture |
| --- | --- | --- |
| Scenario spec | Declares the objective, allowed evidence, forbidden actions, and pass/fail gates. | `scenario.json` |
| Trace | Captures observation, retrieval, proposal, verification, and promotion events. | `trace.json` |
| Report | Summarizes scores, evidence coverage, risks, and recommended next action. | `report.json` |
| Candidate playbook | Describes the maintainer-owned workflow that could be reused later. | `candidate-playbook.md` |
| Verifier result | Accepts or rejects candidates with concrete reasons and rollback notes. | `verifier-result.json` |

プロトタイプは retrieval と action を意図的に分離します。run は
closed PR diff、Linear status、CI 履歴、ローカル docs を retrieve できますが、evaluator pass の一部として
close、merge、publish、tag、config 書き換えはできません。

## Phase モデル (Phase Model)

1. 現在の queue、dirty worktree、branch 状態、open PR/issue、
   discussion、CI 状態、リリースゲートを観測。
2. 関連 reference evidence を retrieve：stale-salvage ledger 行、以前の
   maintainer PR、現在の docs、analyzer finding、CI 失敗、harness
   adapter ルール。
3. ソース帰属と期待 validation gate を持つ1つ以上の playbook を提案。
4. 明示的 acceptance と rejection ルールに対して各 playbook を verify。
5. blast radius を広げずにシナリオを改善する candidate のみ昇格。
6. rollback ガイダンスと未解決 manual-review tail を記録。

## 最初のシナリオ (First Scenario)

最初のシナリオは `stale-pr-salvage-maintainer-branch` です。

2026年5月 cleanup 中に Affaan が設定したルールをモデル化：stale closure は
queue hygiene であり、有用作業の喪失ではない。有用な closed PR 作業は
帰属/backlink 付き maintainer-owned PR に port し、生成 churn、
bulk localization、曖昧な translator 作業は blind
cherry-pick から除外。

Verifier は次の maintainer salvage branch を accept します：

- ソース PR を credit；
- 生プライベート context と個人 path を避ける；
- translator レビューなしで stale bulk localization を import しない；
- durable ledger 更新を記録；
- 通常の code、docs、catalog 変更と同じ validation gate を実行；
- リリース publication アクションは approval-gated のまま。

Verifier は次の blind cherry-pick 提案を reject します：

- stale translation/doc churn を wholesale import；
- 現在の catalog/install アーキテクチャをスキップ；
- 帰属がない；
- テストまたは ledger 更新がない；
- リリースまたは plugin publication 状態を変更。

## Corpus フィクスチャ (Corpus Fixtures)

ルート fixture ファイルは元の
`stale-pr-salvage-maintainer-branch` プロトタイプを保持します。同じ5-artifact 契約を再利用する追加シナリオは
サブディレクトリに置けます。

現在のコーパス：

- `stale-pr-salvage-maintainer-branch`: 帰属と validation で
  maintainer-owned branch 経由の有用 closed PR 作業を回収。
- `billing-marketplace-readiness`: 公開コピーがライブと言う前に billing、App、Marketplace
  ローンチクレームを verify。
- `ci-failure-diagnosis`: CI fix playbook を昇格する前に failed-job log、changed-file scope、
  名前付き regression command を要求。
- `harness-config-quality`: harness セットアップ推奨を昇格する前に adapter 状態、install/onramp path、
  verification command、risk note、config-preservation 振る舞いを要求。
- `agentshield-policy-exception`: policy exception を昇格する前に AgentShield SARIF または report
  evidence、policy-pack source、owner/ticket/scope/expiry フィールド、expired
  exception enforcement を要求。
- `skill-quality-evidence`: skill 修正を昇格する前に focused skill scope、観測失敗または
  user-feedback evidence、example/reference-set カバレッジ、validation command、
  publication safety を要求。
- `deep-analyzer-evidence`: deep-analysis 振る舞いを変更する前に maintained analyzer corpus case、
  expected-output 比較、代表的 repository/commit 履歴、regression command を要求。

## ECC Tools マッピング (ECC Tools Mapping)

ECC Tools は retrieval、
embedding、ranking、evaluator 変更向けの欠落 RAG/evaluator evidence をすでにフラグします。このプロトタイプはそれらのチェックに
ターゲット形状を与えます：

- `scenario.json` → analyzer corpus 入力。
- `trace.json` → golden trace と run telemetry。
- `report.json` → PR comment サマリーと Linear backlog サマリー。
- `candidate-playbook.md` → 提案 follow-up PR body。
- `verifier-result.json` → pass/fail check-run evidence。

将来の ECC Tools 作業は hosted retrieval または model-backed judging を追加する前に
これらの artifact を fixture 形状として消費すべきです。ローカルプロトタイプは
有料 API や vector store を導入する前に契約を証明するのに十分です。

## 昇格ルール (Promotion Rules)

candidate は次のときのみ昇格できます：

- verifier 結果が `accepted`；
- 少なくとも1つの rejected candidate が verifier が no と言えることを証明；
- すべてのソース PR または reference artifact に帰属がある；
- 提案アクションが maintainer-owned で reversible；
- validation command が名前付き；
- 未解決 translator、release、billing、publication 項目は別途承認まで blocked のまま。

## 次の拡張 (Next Expansion)

ローカル evaluator/RAG コーパスは現在の evidence bucket をカバーします。将来の
作業は hosted
retrieval、vector storage、model-backed judging、自動 check-run
昇格を追加する前に ECC Tools からこれらの fixture を消費すべきです。
