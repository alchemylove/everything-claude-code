# ECC プラットフォーム価値ループ (ECC Platform Value Loop)

ECC 2.0 はポータブル harness レイヤーから完全な operator
システムへ移行中です。プロダクト方向は3レイヤー：

1. Meta-harness: ポータブル skills、rules、hooks、MCP 規約、リリースゲート、
   eval、security evidence。
2. Dedicated ECC agent: 静的指示として読むだけでなく ECC asset を直接操作するエージェント。
3. Control pane / agentic IDE: sessions、queue、skills、memory、evidence、release、チームワークフロー向けの可視 operator 面。

control pane は再現可能なデモで裏付けられるまで release-candidate 方向のままです。公開クレームは：

```text
ECC can be used full-stack as a meta-harness + agent + control pane, or
selectively as the portable harness layer inside the AI coding tools teams
already use.
```

## OSS プラットフォームテーゼ (OSS Platform Thesis)

古いオープンソースインフラ playbook は distribution first でした：無料
ソースと寛大な self-serve アクセスがデフォルトの開発者語彙を作り、
hosted infrastructure、managed teams、support、enterprise feature が
スケールでワークフローを走らせやすくして価値を捕捉しました。データベース、アプリプラットフォーム、エッジプラットフォームがこれを明確に示しました：
開発者は無料面を採用し、チームはブランドに標準化し、
有料プロダクトがスケール運用を容易にしました。

AI-agent インフラは同じ形状に従うべきですが、hosted 価値は deployment だけではありません。有料または managed 面は：

- チーム memory と session routing；
- observable queue、handoff、agent run；
- managed eval、リリースゲート、evidence pack；
- security review、supply-chain finding、policy enforcement；
- billing、entitlement、sponsor、partner workflow；
- 再利用可能な ECC skills になりうるプロダクト固有統合。

オープンリポジトリは単体で有用なままです。プラットフォームは真剣な
チームが同じワークフローを managed、measured、secured、または自社プロダクトに
接続したいときに価値を得ます。

## プロダクト統合契約 (Product Integration Contract)

外部プロダクトは ECC ブランド製品にならずに ECC 上に構築できます。契約は：

| Layer | Product contributes | ECC receives |
| --- | --- | --- |
| Skill pack | Public, non-secret workflows in `skills/*/SKILL.md` | New reusable agent behavior and install surface |
| Gated API | Optional product credentials such as `PRODUCT_API_KEY` | A clear upgrade/request path without leaking secrets |
| Fixtures and docs | Sanitized examples, no private accounts or live keys | Testable public proof instead of claims |
| Eval and risk gates | Advice, safety, data, and execution boundaries | Reusable release discipline and trust surface |
| Case study | A real product workflow that works through ECC | Distribution, sponsors, Pro interest, consulting demand |

すべての統合には次が必要：

- プライベート認証情報なしで動く公開ワークフロー；
- ライブプロダクトデータまたはアクション向けの別 gated path；
- billing と所有権が曖昧にならない明確なビジネス境界；
- 統合面を証明するテストまたは文書化されたコマンド；
- 公開 secret やプライベートアカウントデータを要求しないサポート経路。

## Ito 例 (Ito Example)

Ito は別の prediction-market basket プロダクトです。skill ワークフローが ECC
Tools を Ito プロダクトにしないまま有用なため、ECC は Ito 型 skills を配布できます。

安全な公開面は：

- research market、underlier、venue、liquidity context；
- basket をユーザー自身のメモ、ポートフォリオ制約、thesis と比較；
- 手動レビュー向けの非助言 trade-planning worksheet を起草；
- データが利用可能なとき market/concept 関係と backtesting 出力を可視化；
- より広いエージェント research への入力として prediction-market signal を使用。

gated 面は：

- ライブ Ito basket データ；
- アカウント固有状態；
- API バックド backtesting または可視化；
- `ITO_API_KEY` を要求する任意のワークフロー。

境界は厳格：公開 ECC skills は取引を行わず、投資助言を提供せず、プライベート戦略を露出せず、ECC Tools billing と Ito billing をマージしません。

## 価値ループ (Value Loop)

プラットフォームループは明示的であるべき：

1. プロダクトチームが有用なワークフローを ECC skill pack として構築。
2. 公開 skill pack は公開ソースまたはローカルユーザー提供データで動作。
3. 真剣なユーザーがライブプロダクトデータまたは hosted feature 向け gated access を要求。
4. プロダクト利用が新しい operator パターン、失敗モード、例を生む。
5. サニタイズされたパターンがより良い ECC skills、eval、gate、docs になる。
6. ECC は distribution、maintainer、sponsor、Pro interest、consulting lead を得る。
7. プロダクトはエージェントユーザーがすでにインストールした harness 経由で操作できるため採用を得る。

これは enterprise consulting 単体とは異なります。Consulting は作業を資金化できますが、
プラットフォーム目標は反復可能な distribution です：有用なプロダクト統合ごとに ECC を
インストールする理由が増え、真剣な ECC ユーザーごとに可能な sponsor、Pro user、partner、
integration customer になります。

## リリース Lane (Release Lane)

リリースクレームを分離して保つ：

- `1.10.1`: リリース済みユーザー向け安定信頼性と docs パッチ。
- `1.11.0`: control pane が GA である必要のない公開 OSS workflow-catalog モメンタム。
- `2.0.0-rc.x`: 完全 operator システムが prerelease のままの control-pane、dedicated-agent、platform、release-evidence 作業。

ORCA/CONDUCTOR 級 parity、marketplace billing、公式
plugin-directory 掲載、ライブ取引、native-payments readiness を
新鮮な evidence と owner 承認なしに announce しない。
