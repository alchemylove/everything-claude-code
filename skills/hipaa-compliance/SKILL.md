---
name: hipaa-compliance
description: HIPAA-specific entrypoint for healthcare privacy and security work. Use when a task is explicitly framed around HIPAA, PHI handling, covered entities, BAAs, breach posture, or US healthcare compliance requirements.
origin: ECC direct-port adaptation
version: "1.0.0"
---

# HIPAA コンプライアンス (HIPAA Compliance)

タスクが明確に米国ヘルスケアコンプライアンスに関する場合の HIPAA 専用エントリポイントとして使用する。このスキルは意図的に薄く正規化されている:

- `healthcare-phi-compliance` は PHI/PII 取り扱い、データ分類、監査ログ、暗号化、漏洩防止の主要実装スキルのまま。
- `healthcare-reviewer` はコード、アーキテクチャ、プロダクト動作にヘルスケア意識のセカンドパスが必要なときの専門レビュアーのまま。
- `security-review` は一般 auth、入力処理、secrets、API、デプロイ強化に引き続き適用。

## 使用タイミング (When to Use)

- リクエストが明示的に HIPAA、PHI、covered entities、business associates、BAA に言及する
- PHI を保存、処理、エクスポート、送信する米国ヘルスケアソフトウェアの構築またはレビュー
- ログ、分析、LLM プロンプト、ストレージ、サポートワークフローが HIPAA 露出を生むか評価
- 最小必要アクセスと監査可能性が重要な患者向けまたは臨床医向けシステムの設計

## 仕組み (How It Works)

HIPAA を広いヘルスケアプライバシースキルの上に重ねるオーバーレイとして扱う:

1. 具体実装ルールは `healthcare-phi-compliance` から始める。
2. HIPAA 固有の意思決定ゲートを適用する:
   - このデータは PHI か？
   - このアクターは covered entity または business associate か？
   - データに触れる前にベンダーまたはモデルプロバイダーに BAA が必要か？
   - アクセスは最小必要スコープに限定されているか？
   - 読み取り/書き込み/エクスポートイベントは監査可能か？
3. 患者安全、臨床ワークフロー、規制対象本番アーキテクチャに影響する場合は `healthcare-reviewer` にエスカレートする。

## HIPAA 固有ガードレール (HIPAA-Specific Guardrails)

- PHI をログ、分析イベント、クラッシュレポート、プロンプト、クライアント可視エラー文字列に置かない。
- PHI を URL、ブラウザストレージ、スクリーンショット、コピーした例ペイロードに露出しない。
- PHI 読み書きには認証済みアクセス、スコープ付き認可、監査証跡を要求する。
- BAA ステータスとデータ境界が明確になるまで、第三者 SaaS、オブザーバビリティ、サポートツール、LLM プロバイダーはデフォルトでブロック。
- 最小必要アクセスに従う: 正しいユーザーはタスクに必要な最小の PHI スライスのみ見るべき。
- 名前、MRN、電話番号、住所、その他識別子より不透明な内部 ID を優先する。

## 例 (Examples)

### 例 1: HIPAA としてフレームされたプロダクトリクエスト (Example 1: Product request framed as HIPAA)

ユーザーリクエスト:

> Add AI-generated visit summaries to our clinician dashboard. We serve US clinics and need to stay HIPAA compliant.

レスポンスパターン:

- `hipaa-compliance` を有効化
- PHI の移動、ログ、ストレージ、プロンプト境界のレビューに `healthcare-phi-compliance` を使用
- PHI 送信前に要約プロバイダーが BAA でカバーされているか検証
- 要約が臨床判断に影響する場合は `healthcare-reviewer` にエスカレート

### 例 2: ベンダー/ツーリング判断 (Example 2: Vendor/tooling decision)

ユーザーリクエスト:

> Can we send support transcripts and patient messages into our analytics stack?

レスポンスパターン:

- それらのメッセージに PHI が含まれる可能性を前提とする
- 分析ベンダーが HIPAA 拘束ワークロードで承認され、データパスが最小化されていない限り設計をブロック
- 可能なら redaction または非 PHI イベントモデルを要求

## 関連スキル (Related Skills)

- `healthcare-phi-compliance`
- `healthcare-reviewer`
- `healthcare-emr-patterns`
- `healthcare-eval-harness`
- `security-review`
