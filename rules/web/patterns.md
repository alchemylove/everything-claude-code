> このファイルは [common/patterns.md](../common/patterns.md) を拡張し、Web 固有のパターンの内容を追加する。

# Web パターン (Web Patterns)

## コンポーネントコンポジション (Component Composition)

### コンパウンドコンポーネント (Compound Components)

関連する UI が状態とインタラクションのセマンティクスを共有する場合、コンパウンドコンポーネントを使用する:

```tsx
<Tabs defaultValue="overview">
  <Tabs.List>
    <Tabs.Trigger value="overview">Overview</Tabs.Trigger>
    <Tabs.Trigger value="settings">Settings</Tabs.Trigger>
  </Tabs.List>
  <Tabs.Content value="overview">...</Tabs.Content>
  <Tabs.Content value="settings">...</Tabs.Content>
</Tabs>
```

- 親が状態を所有する
- 子はコンテキスト経由で消費する
- 複雑なウィジェットでは props のバケツリレーよりもこれを優先する

### レンダープロップ / スロット (Render Props / Slots)

- 動作は共有されるがマークアップを変える必要がある場合、レンダープロップまたはスロットパターンを使用する
- キーボードハンドリング、ARIA、フォーカスロジックはヘッドレスレイヤーに保持する

### コンテナ / プレゼンテーション分離 (Container / Presentational Split)

- コンテナコンポーネントがデータ読み込みと副作用を所有する
- プレゼンテーションコンポーネントは props を受け取り UI をレンダリングする
- プレゼンテーションコンポーネントは純粋に保つべきである

## 状態管理 (State Management)

これらを個別に扱う:

| 関心事 | ツール |
|--------|--------|
| サーバー状態 | TanStack Query、SWR、tRPC |
| クライアント状態 | Zustand、Jotai、signals |
| URL 状態 | search params、route segments |
| フォーム状態 | React Hook Form または同等のもの |

- サーバー状態をクライアントストアに複製しない
- 冗長な計算済み状態を保存する代わりに値を導出する

## 状態としての URL (URL As State)

共有可能な状態を URL に永続化する:
- フィルタ
- ソート順
- ページネーション
- アクティブタブ
- 検索クエリ

## データフェッチ (Data Fetching)

### Stale-While-Revalidate

- キャッシュされたデータを即座に返す
- バックグラウンドで再バリデーションする
- 手作りする代わりに既存のライブラリを優先する

### 楽観的更新 (Optimistic Updates)

- 現在の状態のスナップショットを取る
- 楽観的な更新を適用する
- 失敗時にロールバックする
- ロールバック時に可視的なエラーフィードバックを出す

### 並列ローディング (Parallel Loading)

- 独立したデータを並列にフェッチする
- 親子のリクエストウォーターフォールを避ける
- 正当な理由がある場合、次のルートや状態をプリフェッチする
