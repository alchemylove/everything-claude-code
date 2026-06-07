> このファイルは [common/testing.md](../common/testing.md) を拡張し、Web 固有のテスト内容を追加する。

# Web テストルール (Web Testing Rules)

## 優先順位 (Priority Order)

### 1. ビジュアルリグレッション (Visual Regression)

- 主要なブレークポイントでスクリーンショットを撮る: 320、768、1024、1440
- ヒーローセクション、スクローリーテリングセクション、および意味のある状態をテストする
- ビジュアル重視の作業には Playwright スクリーンショットを使用する
- 両テーマが存在する場合は両方をテストする

### 2. アクセシビリティ (Accessibility)

- 自動アクセシビリティチェックを実行する
- キーボードナビゲーションをテストする
- 動作軽減の動作を検証する
- カラーコントラストを検証する

### 3. パフォーマンス (Performance)

- 意味のあるページに対して Lighthouse または同等のものを実行する
- [performance.md](performance.md) の CWV 目標を維持する

### 4. クロスブラウザ (Cross-Browser)

- 最低: Chrome、Firefox、Safari
- スクロール、モーション、フォールバック動作をテストする

### 5. レスポンシブ (Responsive)

- 320、375、768、1024、1440、1920 でテストする
- オーバーフローがないことを検証する
- タッチインタラクションを検証する

## E2E の形式 (E2E Shape)

```ts
import { test, expect } from '@playwright/test';

test('landing hero loads', async ({ page }) => {
  await page.goto('/');
  await expect(page.locator('h1')).toBeVisible();
});
```

- 不安定なタイムアウトベースのアサーションを避ける
- 決定的な待機を優先する

## ユニットテスト (Unit Tests)

- ユーティリティ、データ変換、カスタムフックをテストする
- 高度にビジュアルなコンポーネントでは、壊れやすいマークアップアサーションよりもビジュアルリグレッションの方がシグナルが高いことが多い
- ビジュアルリグレッションはカバレッジ目標を補完するものであり、置き換えるものではない
