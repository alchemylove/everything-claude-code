# install_hook_wrapper.ps1 argv-dup bug 回避策 (2026-04-22) (install_hook_wrapper.ps1 argv-dup bug workaround (2026-04-22))

## 概要 (Summary)

`docs/fixes/install_hook_wrapper.ps1` は PowerShell ヘルパーで、
`observe-wrapper.sh` を `~/.claude/skills/continuous-learning/hooks/` にコピーし、
observer hook がそれを指すよう `~/.claude/settings.local.json` を書き換えます。

以前のバージョンは次の形式の hook command を生成していました：

```
"C:\Program Files\Git\bin\bash.exe" "C:\Users\...\observe-wrapper.sh"
```

Claude Code v2.1.116 では第1 argv トークンが重複します。そのトークンが
引用符付き Windows 実行ファイルパスの場合、`bash.exe` が自分自身を
`$0` として再呼び出され、`cannot execute binary file`（exit 126）で失敗します。PR #1524
が根本原因を文書化しています。このスクリプトは、修正済み `settings.local.json` レイアウトと
インストーラーを同期させるコンパニオンです。

## 修正内容 (What the fix does)

- 第1トークンは PATH 解決された `bash`（引用符付き `.exe` パスなし）になり、
  argv-dup bug がバイナリをスクリプトとして渡さなくなります。
- wrapper パスは hook command に埋め込む前に forward slash に正規化され、
  MSYS のバックスラッシュ処理の意外な挙動を避けます。
- `PreToolUse` と `PostToolUse` は wrapper が期待する形に合わせ、
  明示的な `pre` / `post` 位置引数を持つ個別 command を受け取ります。
- settings ファイルは LF 改行で書き込まれ、`ConvertTo-Json` からの
  混在 CRLF/LF 出力を下流 JSON パーサーが見ないようにします。

## 結果の command 形状 (Resulting command shape)

```
bash "C:/Users/<you>/.claude/skills/continuous-learning/hooks/observe-wrapper.sh" pre
bash "C:/Users/<you>/.claude/skills/continuous-learning/hooks/observe-wrapper.sh" post
```

## 使い方 (Usage)

```powershell
# Place observe-wrapper.sh next to this script, then:
pwsh -File docs/fixes/install_hook_wrapper.ps1
```

スクリプトは書き込み前に `settings.local.json` を
`settings.local.json.bak-<timestamp>` にバックアップします。

## PowerShell 5.1 互換性 (PowerShell 5.1 compatibility)

`ConvertFrom-Json -AsHashtable` は PowerShell 7+ のみです。スクリプトは
まず `-AsHashtable` を試し、Windows PowerShell 5.1 では手動の `PSCustomObject` →
`Hashtable` 変換にフォールバックします。両方の hook バケット
（`PreToolUse`、`PostToolUse`）とその内部 `hooks` 配列は
シリアライズ前に `System.Collections.ArrayList` として具体化されるため、
PS 5.1 の `ConvertTo-Json` が単一要素配列を
素のオブジェクトに潰しません。`pwsh` なしで Windows PowerShell 5.1 のみの
Windows 11 マシンで `powershell -NoProfile -File
docs/fixes/install_hook_wrapper.ps1` を実行して検証済み。

## 関連 (Related)

- PR #1524 — settings.local.json 形状修正（同じ argv-dup 根本原因）
- PR #1511 — observer python 解決で `AppInstallerPythonRedirector.exe` をスキップ
- PR #1539 — ロケール非依存の `detect-project.sh`
- PR #1542 — `patch_settings_cl_v2_simple.ps1` コンパニオン修正
