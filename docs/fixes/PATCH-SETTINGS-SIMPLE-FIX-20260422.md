# patch_settings_cl_v2_simple.ps1 argv-dup bug 回避策 (2026-04-22) (patch_settings_cl_v2_simple.ps1 argv-dup bug workaround (2026-04-22))

## 概要 (Summary)

`docs/fixes/patch_settings_cl_v2_simple.ps1` は最小限の PowerShell
ヘルパーで、observer hook が `observe-wrapper.sh` を指すよう
`~/.claude/settings.local.json` をパッチします。PR #1540 の
`docs/fixes/install_hook_wrapper.ps1` の「シンプル」版で、wrapper スクリプトは
コピーせず settings ファイルのみ書き換えます。

このヘルパーの以前のバージョンは、生の `observe.sh` パスを
hook command として登録し、`PreToolUse` と `PostToolUse` で単一 command 文字列を
共有し、CRLF 改行を出力しうる `ConvertTo-Json` デフォルトに依存していました。
Claude Code v2.1.116 では第1 argv トークンが重複するため、wrapper は
特定の形状で呼び出す必要があり、2つの hook phase は個別エントリが必要です。

## 修正内容 (What the fix does)

- 第1トークンは PATH 解決された `bash`（引用符付き `.exe` パスなし）で、
  argv-dup bug がバイナリをスクリプトとして渡さなくなります。PR #1524 と
  PR #1540 に一致。
- wrapper パスは hook command に埋め込む前に forward slash に正規化され、
  MSYS のバックスラッシュ処理の意外な挙動を避けます。
- `PreToolUse` と `PostToolUse` は明示的な
  `pre` / `post` 位置引数を持つ個別 command を受け取ります。
- settings ファイルは UTF-8（BOM なし）で書き込まれ、CRLF は LF に正規化され、
  下流 JSON パーサーが混在改行を見ないようにします。
- 既存 hooks（レガシー `observe.sh` エントリや無関係な
  第三者 hooks を含む）は保持 — スクリプトはまだ登録されていない場合のみ
  新しい wrapper エントリを追加します。
- 再実行時に冪等：2回目の呼び出しは正規 command 文字列を認識し、
  エントリを重複させず `[SKIP]` をログします。

## 結果の command 形状 (Resulting command shape)

```
bash "C:/Users/<you>/.claude/skills/continuous-learning/hooks/observe-wrapper.sh" pre
bash "C:/Users/<you>/.claude/skills/continuous-learning/hooks/observe-wrapper.sh" post
```

## 使い方 (Usage)

```powershell
pwsh -File docs/fixes/patch_settings_cl_v2_simple.ps1
# Windows PowerShell 5.1 is also supported:
powershell -NoProfile -ExecutionPolicy Bypass -File docs/fixes/patch_settings_cl_v2_simple.ps1
```

スクリプトは既存 settings ファイルを
`settings.local.json.bak-<timestamp>` にバックアップしてから書き込みます。

## PowerShell 5.1 互換性 (PowerShell 5.1 compatibility)

`ConvertFrom-Json -AsHashtable` は PowerShell 7+ のみです。スクリプトは
まず `-AsHashtable` を試し、Windows PowerShell 5.1 では手動の `PSCustomObject` →
`Hashtable` 変換にフォールバックします。両方の hook バケット
（`PreToolUse`、`PostToolUse`）とその内部 `hooks` 配列は
シリアライズ前に `System.Collections.ArrayList` として具体化されるため、
PS 5.1 の `ConvertTo-Json` が単一要素配列を素のオブジェクトに潰しません。

## 検証済みケース（dry-run）(Verified cases (dry-run))

1. 新規インストール — 既存 settings なし → 正規ファイルを作成。
2. 冪等な再実行 — 既存の正規ファイル → 両 phase で `[SKIP]`、
   書き込み前バックアップ以外はファイル内容不変。
3. レガシー `observe.sh` 存在 → レガシーエントリを保持し、
   新しい `observe-wrapper.sh` エントリを並べて追加。

3ケースすべて LF のみ出力を生成し、PR #1524 の `settings.local.json` 手動修正と
同じ形状に一致します。

## 関連 (Related)

- PR #1524 — settings.local.json 形状修正（同じ argv-dup 根本原因）
- PR #1539 — ロケール非依存の `detect-project.sh`
- PR #1540 — `install_hook_wrapper.ps1` argv-dup 修正（コンパニオンスクリプト）
