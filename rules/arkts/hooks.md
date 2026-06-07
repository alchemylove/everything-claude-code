---
paths:
  - "**/*.ets"
  - "**/*.ts"
  - "**/module.json5"
  - "**/oh-package.json5"
---
# HarmonyOS / ArkTS フック (HarmonyOS / ArkTS Hooks)

> このファイルは [common/hooks.md](../common/hooks.md) を拡張し、HarmonyOS / ArkTS 固有の内容を追加する。

## ビルドコマンド (Build Commands)

### HAP パッケージのビルド (HAP Package Build)

```bash
# HAP パッケージをビルドする（グローバル hvigor 環境） (Build HAP package (global hvigor environment))
hvigorw assembleHap -p product=default

# 特定のモジュールでビルドする (Build with specific module)
hvigorw assembleHap -p module=entry -p product=default

# クリーンビルド (Clean build)
hvigorw clean
```

### DevEco Studio CLI (DevEco Studio CLI)

```bash
# プロジェクト構造を確認する (Check project structure)
hvigorw --version

# 依存関係をインストールする (Install dependencies)
ohpm install

# 依存関係を更新する (Update dependencies)
ohpm update
```

## 推奨 PostToolUse フック (Recommended PostToolUse Hooks)

### .ets/.ts ファイル編集後 (After Editing .ets/.ts Files)

ArkTS のコンパイルエラーを確認するために hvigor ビルドを実行する:

```json
{
  "type": "PostToolUse",
  "matcher": {
    "tool": ["Edit", "Write"],
    "filePath": ["**/*.ets", "**/*.ts"]
  },
  "hooks": [
    {
      "command": "hvigorw assembleHap -p product=default 2>&1 | tail -20",
      "async": true,
      "timeout": 60000
    }
  ]
}
```

### module.json5 編集後 (After Editing module.json5)

パーミッションとアビリティの宣言を検証する:

```json
{
  "type": "PostToolUse",
  "matcher": {
    "tool": "Edit",
    "filePath": "**/module.json5"
  },
  "hooks": [
    {
      "command": "echo '[HarmonyOS] module.json5 modified - verify permissions and abilities'",
      "async": false
    }
  ]
}
```

### oh-package.json5 編集後 (After Editing oh-package.json5)

依存関係を再インストールする:

```json
{
  "type": "PostToolUse",
  "matcher": {
    "tool": "Edit",
    "filePath": "**/oh-package.json5"
  },
  "hooks": [
    {
      "command": "ohpm install 2>&1 | tail -10",
      "async": true,
      "timeout": 30000
    }
  ]
}
```

## PreToolUse フック (PreToolUse Hooks)

### V1 デコレーターガード (V1 Decorator Guard)

コードに V1 状態管理デコレーターが含まれている場合に警告する:

```json
{
  "type": "PreToolUse",
  "matcher": {
    "tool": ["Write", "Edit"],
    "filePath": "**/*.ets"
  },
  "hooks": [
    {
      "command": "echo '[HarmonyOS] Reminder: Use @ComponentV2 / @Local / @Param - V1 decorators (@State, @Prop, @Link) are prohibited'"
    }
  ]
}
```

## 検証チェックリスト (Validation Checklist)

各実装サイクルの後、以下を確認する:

- [ ] `hvigorw assembleHap` がエラーなしで完了する
- [ ] 新規または変更した `.ets` ファイルに V1 デコレーターがない
- [ ] 新規または変更したファイルに `@ohos.router` のインポートがない
- [ ] すべての API パーミッションが `module.json5` に宣言されている
- [ ] すべての依存関係が `oh-package.json5` に記載されている
- [ ] リソース文字列がすべての i18n ディレクトリに追加されている
- [ ] 新しいカラーリソースにダークテーマのカラーが提供されている
