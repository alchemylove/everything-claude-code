# ECC 2.0 Video Suite 制作マニフェスト (ECC 2.0 Video Suite Production Manifest)

スナップショット日: 2026-05-19。

これは ECC 2.0 リリース video suite の制作コントラクトである。公開リリースストーリー、ローカルソースインベントリ、レンダー出力、self-eval ゲートを 1 か所にまとめ、raw footage、private transcript export、absolute local path をコミットしない。

## 主張 (Claim)

ECC 2.0 は agentic work 向けの harness-native オペレーターシステムである。

動画はその主張を直接証明すべきである:

- Claude Code、Codex、OpenCode、Cursor、Gemini、Zed、GitHub Copilot、terminal ワークフローにまたがる 1 つの再利用可能なレイヤー;
- 再利用可能な skills、rules、hooks、agents、MCP 規約、リリースゲート、オペレーターワークフロー;
- プロダクト全体ではなく alpha control-plane/TUI 方向としての `ecc2/`;
- enterprise trust layer としての AgentShield と supply-chain ゲート;
- OSS は無料のまま、GitHub Sponsors、ECC Tools Pro、consulting が funding surface。

ローンチをリネーム、pivot、config pack、Claude-only パッケージとしてフレーミングしないこと。

## プライベート入力 (Private Inputs)

raw footage、transcript JSON、timeline export をコミットしないこと。

オペレーターは環境変数を使って validator をローカルメディアに向けること:

```bash
ECC_VIDEO_SOURCE_ROOT=/path/to/ecc_2_raws \
ECC_VIDEO_RELEASE_SUITE_ROOT=/path/to/ecc_2_release_suite \
npm run release:video-suite -- --format json
```

`ECC_VIDEO_SOURCE_ROOT` には proof 画像を含め、編集済みソースクリップを含む `_edited/` サブディレクトリを含めてよい。`ECC_VIDEO_RELEASE_SUITE_ROOT` には `edl/`、`segments/`、`renders/`、`timelines/`、`transcripts/` を含めること。

## ソースインベントリ (Source Inventory)

これらの basename はリリース suite validator に必要なローカル入力である。

| Asset | レーン (Lane) | 証拠 (Proof) |
| --- | --- | --- |
| `longform-full-wide.mp4` | Primary launch video | operator system、control-plane 方向、closing proof |
| `sf-longform-full.mp4` | Primary launch video | structured context opener |
| `sf-thread-2-whatisecc.mp4` | What is ECC | カテゴリ明確化と GitHub App 説明 |
| `sf-thread-4-security.mp4` | Security proof | AgentShield、hook、MCP、permission risk |
| `thread-2-ghapp-money.mp4` | Money/proof clip | OSS と有料 hosting/service |
| `architecture-2-wide.mp4` | B-roll | harness-native architecture |
| `terminal-scan-2-wide.mp4` | Install proof | terminal workflow と install 信頼性 |
| `new_site_raw.mp4` | B-roll | サイトと product surface |
| `coverage-montage-wide.mp4` | Coverage/social proof | 配布と social proof |
| `metrics-ticker-2-wide.mp4` | Money/proof clip | traction と funnel proof |
| `growth-timeline-2-wide.mp4` | Coverage/social proof | リリース momentum timeline |
| `gh_app_1.png` | Money/proof clip | hosted GitHub App surface |
| `star_history.png` | Coverage/social proof | OSS 採用チャート |
| `x_analytics.png` | Coverage/social proof | social 配布 proof |
| `100k.png` | Coverage/social proof | リーチ milestone proof |

## 成果物 (Deliverables)

| 成果物 (Deliverable) | 長さ (Length) | アスペクト (Aspect) | 出力 (Output) |
| --- | ---: | --- | --- |
| Primary launch video | 90-150s | 16:9 | `ecc-2-primary-launch.mp4` |
| Install proof clip | 25-35s | 16:9 and 9:16 | `ecc-2-install-proof-*` |
| What is ECC clip | 45-60s | 16:9 and 9:16 | `ecc-2-what-is-ecc-*` |
| Security proof clip | 45-60s | 16:9 and 9:16 | `ecc-2-security-proof-*` |
| Money/proof clip | 30-45s | 16:9 and 9:16 | `ecc-2-money-proof-*` |
| Coverage/social proof clip | 30-45s | 16:9 and 9:16 | `ecc-2-social-proof-*` |

## プライマリローンチ動画 (Primary Launch Video)

rough v1 primary launch assembly が現在の spine である。speech-led を維持し、product proof が jump cut と古い wording をカバーする。

| 順序 (Order) | ソース (Source) | In | Out | 用途 (Use) |
| --- | --- | ---: | ---: | --- |
| 01 | `sf-longform-full.mp4` | 161.12 | 177.68 | クリーンな opener: skill、command、agent、hook、project setup による structured context としての ECC。 |
| 02 | `thread-2-ghapp-money.mp4` | 21.84 | 30.40 | 直接的な product thesis: agentic harness optimization。 |
| 03 | `thread-2-ghapp-money.mp4` | 41.00 | 59.72 | 別 harness ではない; ECC は harness 上のレイヤーと tooling。 |
| 04 | `longform-full-wide.mp4` | 254.60 | 271.20 | Agentic IDE、observability、tracing、multi-agent control-plane 方向。 |
| 05 | `sf-thread-2-whatisecc.mp4` | 40.08 | 60.60 | GitHub App が repo を分析し project 固有 skill、prompt、hook を注入。 |
| 06 | `sf-thread-4-security.mp4` | 17.60 | 32.72 | Security risk セットアップ: hook、MCP server、permission。 |
| 07 | `sf-thread-4-security.mp4` | 37.28 | 51.32 | AgentShield proof: rule、category、grade、secret、injection、exfiltration。 |
| 08 | `thread-2-ghapp-money.mp4` | 59.72 | 75.96 | OSS-first ビジネスモデルと managed GitHub App surface。 |
| 09 | `longform-full-wide.mp4` | 507.34 | 525.62 | workflow、tested shipping、secure daily agent work で締める。 |

必須ローカル rough v1 artifact:

- `edl/primary-launch.edl.md`
- `timelines/primary-launch-v1.timeline.json`
- `renders/ecc-2-primary-launch-rough-v1.mp4`
- `renders/ecc-2-primary-launch-rough-v1.captions.srt`
- `segments/primary-launch-v1/01-structured-context.mp4`
- `segments/primary-launch-v1/02-agentic-harness-optimization.mp4`
- `segments/primary-launch-v1/03-not-another-harness.mp4`
- `segments/primary-launch-v1/04-agentic-ide-surface.mp4`
- `segments/primary-launch-v1/05-github-app-proof.mp4`
- `segments/primary-launch-v1/06-security-risk.mp4`
- `segments/primary-launch-v1/07-agentshield-proof.mp4`
- `segments/primary-launch-v1/08-oss-paid-model.mp4`
- `segments/primary-launch-v1/09-close-shipping-system.mp4`

## 公開候補出力 (Publish-Candidate Outputs)

リリース validator は `renders/publish-candidates/` 配下の現在の publish-candidate set も期待する。これらはまだローカルレビューファイルであり、公開アップロードまたはコミット済みメディアではない。

| 出力 (Output) | ターゲット (Target) |
| --- | --- |
| `ecc-2-primary-launch.mp4` | 90-150s, 1920x1080, audio |
| `ecc-2-primary-launch.captions.srt` | primary captions |
| `ecc-2-install-proof-wide.mp4` | 25-35s, 1920x1080, audio |
| `ecc-2-install-proof-vertical.mp4` | 25-35s, 1080x1920, audio |
| `ecc-2-what-is-ecc-wide.mp4` | 45-60s, 1920x1080, audio |
| `ecc-2-what-is-ecc-vertical.mp4` | 45-60s, 1080x1920, audio |
| `ecc-2-security-proof-wide.mp4` | 45-60s, 1920x1080, audio |
| `ecc-2-security-proof-vertical.mp4` | 45-60s, 1080x1920, audio |
| `ecc-2-money-proof-wide.mp4` | 30-45s, 1920x1080, audio |
| `ecc-2-money-proof-vertical.mp4` | 30-45s, 1080x1920, audio |
| `ecc-2-social-proof-wide.mp4` | 30-45s, 1920x1080, audio |
| `ecc-2-social-proof-vertical.mp4` | 30-45s, 1080x1920, audio |

## video-use 互換ワークフロー (video-use compatible workflow)

Video Use と同じ制作形状を使い、ECC 固有のメディアスタックは維持する:

1. Transcript と timeline データを編集 surface として扱う。
2. 視覚検査はオンデマンドに保つ: filmstrip、waveform/timeline composite、または曖昧なカットポイントでのみ frame sample。
3. レンダー前に編集戦略と EDL を提案する。
4. FFmpeg で決定論的にカットする。
5. プロダクト主張に視覚的証拠が必要な箇所では Remotion または Manim で proof overlay を追加する。
6. MP4 と編集可能な timeline、caption 状態をエクスポートする。
7. アップロードまたはソーシャル投稿の前に cut-boundary、audio、caption、black-frame、product-claim self-eval を実行する。

frame をリポジトリにダンプしない。self-eval に使う frame sample はローカルリリース suite ワークスペースに属する。

## ブラウザキャプチャ計画 (Browser Capture Plan)

Browser または同等のデスクトップキャプチャは、リリース日に最新である必要がある proof footage にのみ使用する:

| サーフェス (Surface) | キャプチャ (Capture) |
| --- | --- |
| GitHub repo | README hero、install block、sponsor link、release notes |
| Codex plugin | repo marketplace install path と local plugin README |
| OpenCode package | package install と plugin banner |
| ECC Tools Pro | billing/product page（live readback が claim を確認した後のみ） |
| AgentShield | CLI 出力、policy category view、supply-chain gate |
| `ecc2/` | alpha control-plane/TUI surface（alpha フレーミング付き） |

surface が live でない場合は、ローカルブラウザキャプチャを使い、local または release-candidate proof としてラベル付けする。証拠が存在する前に marketplace、billing、公式 directory 可用性を主張しないこと。

## Self-Eval ゲート (Self-Eval Gate)

validator を実行する:

```bash
ECC_VIDEO_SOURCE_ROOT=/path/to/ecc_2_raws \
ECC_VIDEO_RELEASE_SUITE_ROOT=/path/to/ecc_2_release_suite \
npm run release:video-suite -- --format json
```

次に最終レンダーを手動で確認する:

- primary render の validator self-eval 合格: 90-150 秒、最低 1280x720、video stream あり、audio stream あり、非空出力;
- publish-candidate set の validator self-eval 合格: primary MP4 と captions、5 つの short clip（wide と vertical 両方）;
- validator visual QA がすべての publish-candidate MP4 で検出 black-frame segment ゼロを報告;
- blank frame または意図しないデスクトップ露出なし;
- caption に stale repo 名、pivot、rename、Claude-only フレーミングなし;
- speech を false claim に書き換える caption なし;
- stale URL、古い install コマンド、リネーム前リポジトリリンクなし;
- 投稿が明示的に必要でない限り内部 MRR 数値なし;
- すべてのカットで audio 連続性;
- 最初の 10 秒で ECC が何かを明確に伝える;
- 最終 CTA が clutter なしで repo、sponsor、Pro、consulting にルーティングする。

## 公開してはいけない条件 (Do Not Publish If)

- `npm run release:video-suite` がローカルソースルートで ready でない。
- primary launch render が 90-150 秒ターゲット外。
- caption に旧リポジトリ名が含まれる。
- プロダクト proof が private 画面、secret、顧客データ、raw local path に依存している。
- リリース URL、npm、プラグイン、billing、marketplace 主張が `publication-readiness.md` の証拠を超えている。
