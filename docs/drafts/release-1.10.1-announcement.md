# ECC 1.10.1 リリース告知ドラフト (ECC 1.10.1 release announcement draft)

ECC 1.10.1 は 1.10.0 へのフォローアップ安定化リリースです。

このリリースは install 正確性、クロス面命名の明確化、Windows/PowerShell リカバリ、Cursor プロジェクト install 正確性、Claude Code hook 互換性に焦点を当てます。feature 多めのリリースではありません。

## 安定化パスで着地したもの (What landed in the stabilization pass)
- npm/package/release 面が整合し `ecc-universal@1.10.0` が npm でライブ
- Windows locale/path と PowerShell install-path 回帰を修正
- Bash hook process-storm 回帰を修正
- Claude Code 2.1.x hook schema 互換性を修正
- Cursor native プロジェクト install path を修復：
  - `.cursor/hooks.json` に必要な schema/version 面を含む
  - `.cursor/mcp.json` が native Cursor プロジェクト位置に書き込まれる
- continuous-learning-v2 が `claude-desktop` を有効 entrypoint として受け入れ
- Windows observe path が `AppInstallerPythonRedirector.exe` をスキップ
- docs が plugin install とフル手動 install をより明確に区別

## 1.10.1 の目的 (What 1.10.1 is for)
- 現在の install 面を予測可能にする
- 古い命名/install ガイダンスを減らす
- 1.10.0 からのフォローアップ回帰を閉じる
- issue と discussion 横断で修正を寄せ集める代わりに1つの安定更新点をユーザーに与える

## 含まれるリリース修正 (Included release fixes)
- `#1543` Cursor native プロジェクト hook + MCP install 修復
- `#1524` `settings.local.json` の Claude Code v2.1.116 argv-dup 緩和
- `#1522` continuous-learning-v2 が `claude-desktop` を有効 entrypoint として受け入れ
- `#1511` Windows observe path が `AppInstallerPythonRedirector.exe` をスキップ
- `#1546` continuous-learning-v2 plugin クイックスタート修正
- `#1535` hero overflow フォローアップ

## 重要な命名の明確化 (Important naming clarification)
- Claude marketplace/plugin 識別子: `everything-claude-code@everything-claude-code`
- npm パッケージ: `ecc-universal`
- GitHub リポジトリ: `affaan-m/everything-claude-code`

これらは意図的に異なる面です。plugin 識別子は Anthropic marketplace ルールに従い；npm パッケージは `ecc-universal` のままです。

## 引き続き監視中 (Still being monitored)
これは「すべてのエッジケースが解決した」ではなく安定化リリースとして announce すべきです。

引き続き監視：
- macOS、Windows、Linux 横断の OS 固有エッジケース
- shell 固有の振る舞い差
- 古いまたは混合 install でのみ現れる Cursor vs Claude plugin install-path 不一致
- 現在 main での再現が必要な第三者 provider/tool-name 互換性報告

現在の watch-list 例：
- `#1520` 現在の installer で再現が戻らない限り obsolete の可能性
- `#1516` 現在 `main` で再現されない限り gating しない
- `#1484` アクティブなリリースゲートではなく Windows umbrella/watch-list issue のまま

## 推奨更新ガイダンス (Recommended update guidance)
1.10.0 install/runtime 問題に遭遇した場合：
1. 最新 package/plugin 面に更新
2. docs が明示的にそう言う場合を除き plugin install とフル手動 repo コピーを混ぜない
3. 問題が続く場合は報告：
   - OS + shell
   - Claude Code/Cursor バージョン
   - 使用した install 方法
   - 正確な stderr/出力
   - 問題が plugin install、npm install、repo sync、Cursor プロジェクト install のどれか
