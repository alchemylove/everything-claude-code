# LinkedIn ドラフト - ECC v2.0.0-rc.1 (LinkedIn Draft - ECC v2.0.0-rc.1)

ECC v2.0.0-rc.1 は、2.0 方向への最初の release-candidate pass として、最終リリースレビューの準備が整いました。

実務的な転換はシンプルです：ECC はもう Claude Code plugin や設定バンドルだけとして位置づけられません。

エージェント作業向けの meta-harness になりつつあります：個別の AI コーディングクライアントの上にあるポータブルレイヤーです。

- 使い捨てプロンプトではなく再利用可能な skills
- 手動規律ではなく hooks と tests
- docs、code、browser automation、research への MCP バックドアクセス
- 同じコア workflow レイヤーを共有する Codex、OpenCode、Cursor、Gemini、Zed、Claude Code 面
- chat、cron、handoff、日次作業ルーティング向けの operator shell としての Hermes

この release-candidate 面では、リポジトリを正直に保ちました。

プライベートなワークスペース状態は公開しませんでした。再利用可能なレイヤーを出荷しました：

- サニタイズされた Hermes セットアップドキュメント
- リリースノートとローンチコラテラル
- cross-harness アーキテクチャノート
- ローカル operator パターンを公開 ECC skills に変換する Hermes import ガイダンス
- PR、issue、discussion、Linear 進捗、レガシー tail、observability、supply-chain チェック向けのリリース準備ゲート
- リリースアクション前に公開パックを検証できる決定論的 preview-pack smoke test
- research、comparison、planning、risk review 向けのゲート付き Itô prediction-market skill pack（Itô API アクセスは ECC Tools から分離し、approval ベース）

レバレッジはより良いプロンプトだけではありません。

孤立した面の数を減らし、繰り返しワークフローを再利用可能な skills に変え、エージェント周辺の operating system を測定可能にすることです。

だから meta-harness という表現が好きです。目標は harness を置き換えることではありません。harness の上の workflow レイヤーをポータブルで監査可能かつチーム横断で有用にすることです。

supply-chain 作業もリリースストーリーの一部になりました。Mini Shai-Hulud/TanStack キャンペーンの後、rc.1 には IOC スキャン、no-lifecycle CI installs、advisory-source refresh、npm audit/signature チェック、AI-tool persistence カバレッジが含まれます。

GA 前にまだ harden すべき点はあります。特に packaging、installers、`ecc2/` control plane 周りです。しかし rc.1 は形状を明確に示すのに十分です。

GitHub prerelease と npm `next` パッケージはすでにライブです。公開 publication は、plugin path、video URL、最終 outbound URL、および新鮮に再確認されていない billing/native-payments クレームについて、引き続き approval-gated です。

リリース URL ledger は、すでに解決するリンクと、残りの approval-gated plugin、video、billing、outbound チェックを待たなければならないリンクを分離します。
