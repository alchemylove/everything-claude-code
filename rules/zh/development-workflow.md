# 開発ワークフロー (Development Workflow)

> このファイルは [common/git-workflow.md](./git-workflow.md) を拡張し、git 操作前のフル機能開発プロセスの内容を追加する。

Feature Implementation Workflow は、調査、計画、TDD、コードレビュー、そして git へのコミットまでの開発パイプラインを説明します。

## Feature 実装ワークフロー (Feature Implementation Workflow)

0. **調査と再利用 (Research & Reuse)** _（新規実装の前に必須）_
   - **GitHub code search を最優先:** 新規コードを書く前に `gh search repos` と `gh search code` を実行し、既存実装、テンプレート、パターンを探す。
   - **ライブラリ docs を次に:** Context7 または公式ベンダー docs で API の挙動、パッケージの使い方、バージョン固有の詳細を実装前に確認する。
   - **Exa は上記 2 つで不足するときのみ:** GitHub 検索と一次 docs の後に、より広い Web 調査や探索に Exa を使う。
   - **パッケージレジストリを確認:** ユーティリティコードを書く前に npm、PyPI、crates.io などを検索する。手書きより実戦検証済みライブラリを優先する。
   - **適応可能な実装を探す:** 問題の 80% 以上を解決し、fork、移植、ラップできる OSS を探す。
   - 要件を満たすなら、ゼロから書くより実証済みアプローチの採用・移植を優先する。

1. **先に計画 (Plan First)**
   - **planner** agent で実装計画を作成する
   - コーディング前に計画ドキュメントを生成する: PRD、architecture、system_design、tech_doc、task_list
   - 依存関係とリスクを特定する
   - フェーズに分割する

2. **TDD アプローチ (TDD Approach)**
   - **tdd-guide** agent を使用する
   - 先にテストを書く（RED）
   - テストをパスする実装を行う（GREEN）
   - リファクタリング（IMPROVE）
   - 80%+ カバレッジを検証する

3. **コードレビュー (Code Review)**
   - コード記述直後に **code-reviewer** agent を使用する
   - CRITICAL と HIGH の問題に対処する
   - 可能なら MEDIUM も修正する

4. **コミットとプッシュ (Commit & Push)**
   - 詳細なコミットメッセージ
   - conventional commits 形式に従う
   - コミットメッセージ形式と PR プロセスは [git-workflow.md](./git-workflow.md) を参照

5. **レビュー前チェック (Pre-Review Checks)**
   - すべての自動チェック（CI/CD）がパスしていることを確認する
   - マージコンフリクトを解消する
   - ブランチがターゲットブランチと同期していることを確認する
   - これらのチェックがパスしてからレビューを依頼する
