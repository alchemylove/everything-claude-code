---
description: "ECC coding style: 不変性、ファイル構成、エラーハンドリング、バリデーション"
alwaysApply: true
---
# Coding Style (Coding Style)

## 不変性 (Immutability)（CRITICAL）

常に新しいオブジェクトを作成し、既存のものを変更しない:

```
// Pseudocode
WRONG:  modify(original, field, value) → changes original in-place
CORRECT: update(original, field, value) → returns new copy with change
```

根拠: 不変データは隠れた副作用を防ぎ、デバッグを容易にし、安全な並行処理を可能にする。

## ファイル構成 (File Organization)

少数の大きなファイルより多数の小さなファイル:
- 高凝集、低結合
- 典型的には 200-400 行、最大 800 行
- 大きなモジュールから utility を抽出
- 型ではなく feature/domain 単位で整理

## エラーハンドリング (Error Handling)

常に包括的にエラーを処理:
- すべてのレベルで明示的にエラーを処理
- UI 向けコードではユーザーフレンドリーなエラーメッセージを提供
- サーバー側では詳細なエラーコンテキストをログ
- エラーを黙って握りつぶさない

## 入力バリデーション (Input Validation)

システム境界で常にバリデーション:
- 処理前にすべてのユーザー入力をバリデーション
- 可能な場合は schema ベースのバリデーションを使用
- 明確なエラーメッセージで fail fast
- 外部データ（API レスポンス、ユーザー入力、ファイル内容）を信頼しない

## コード品質チェックリスト (Code Quality Checklist)

作業完了前に:
- [ ] コードが読みやすく、命名が適切
- [ ] 関数が小さい（<50 行）
- [ ] ファイルが焦点を絞っている（<800 行）
- [ ] 深いネストがない（>4 レベル）
- [ ] 適切なエラーハンドリング
- [ ] ハードコードされた値がない（定数または config を使用）
- [ ] 変更がない（不変パターンを使用）
