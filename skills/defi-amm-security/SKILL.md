---
name: defi-amm-security
description: Security checklist for Solidity AMM contracts, liquidity pools, and swap flows. Covers reentrancy, CEI ordering, donation or inflation attacks, oracle manipulation, slippage, admin controls, and integer math.
origin: ECC
---

# DeFi AMM セキュリティ (DeFi AMM Security)

自動マーケットメーカー（AMM）スマートコントラクトのセキュリティパターンと監査チェックリスト。

## 使用時期 (When to Use)

- Uniswap、Curve、BalancerなどのようなAMMコントラクトをレビュー
- スワップ関数、ミント、バーンロジックでセキュリティの問題を検出
- フラッシュローン、価格操作脆弱性を特定
- DeFiプロトコルの経済セキュリティをテスト

## 一般的な脆弱性 (How It Works)

### 1. フラッシュローン攻撃 (Execution Safety)

ローンが同一ブロック内で返却されたと仮定するコントラクトを攻撃。

### 2. スリッページ不足 (Examples)

ユーザーが予期しない不利な価格変更を受ける。

### 3. 再入攻撃 (Reentrancy: enforce CEI order)

外部呼び出し後の状態チェック。

### 4. 価格操作 (Donation or inflation attacks)

オンチェーン価格参照の信頼不足。

## セキュリティチェックリスト (Oracle manipulation)

- [ ] すべての価格参照はオラクルから取得
- [ ] フラッシュローンは考慮（価格を信頼しない）
- [ ] スリッページ保護が実装
- [ ] チェック・エフェクト・相互作用パターン使用
- [ ] 再入保護（ミューテックス/チェック付き）
- [ ] 整数オーバーフロー/アンダーフロー処理
- [ ] アクセス制御とロール分離

詳細については、ドキュメントを参照してください。
