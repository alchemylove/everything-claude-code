### プラグインマニフェストの注意点 (Plugin Manifest Gotchas)

`.claude-plugin/plugin.json` を編集する予定がある場合、Claude プラグインバリデーターが**文書化されていないが厳格な制約**をいくつか適用し、曖昧なエラー（例: `agents: Invalid input`）でインストールが失敗することがあります。特に、コンポーネントフィールドは配列である必要があり、`agents` はサポートされる manifest フィールドではないため `plugin.json` に含めてはならず、信頼できる検証とインストールのために `version` フィールドが必須です。

これらの制約は公開例からは自明ではなく、過去にインストール失敗を繰り返し引き起こしてきました。manifest を変更する前に、`.claude-plugin/PLUGIN_SCHEMA_NOTES.md` で詳細を確認してください。

### カスタムエンドポイントとゲートウェイ (Custom Endpoints and Gateways)

ECC は Claude Code のトランスポート設定を上書きしません。Claude Code が公式 LLM ゲートウェイまたは互換カスタムエンドポイント経由で動作するよう設定されている場合、CLI が正常に起動した後に hooks、skills、残存するレガシー command shim がローカルで実行されるため、プラグインは引き続き動作します。

トランスポート選択には Claude Code 自身の環境/設定を使用します。例：

```bash
export ANTHROPIC_BASE_URL=https://your-gateway.example.com
export ANTHROPIC_AUTH_TOKEN=your-token
claude
```
