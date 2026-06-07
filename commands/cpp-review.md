---
description: メモリ安全性、モダン C++ イディオム、並行性、セキュリティの包括的 C++ コードレビュー。cpp-reviewer エージェントを起動する。
---

# C++ コードレビュー (C++ Code Review)

このコマンドは **cpp-reviewer** エージェントを起動し、C++ 固有の包括的コードレビューを行う。

## このコマンドの内容 (What This Command Does)

1. **C++ 変更の特定**: `git diff` で変更された `.cpp`、`.hpp`、`.cc`、`.h` ファイルを検索
2. **静的解析の実行**: `clang-tidy` と `cppcheck` を実行
3. **メモリ安全性スキャン**: raw new/delete、バッファオーバーフロー、use-after-free を確認
4. **並行性レビュー**: スレッド安全性、mutex 使用、データ競合を分析
5. **モダン C++ チェック**: C++17/20 の規約とベストプラクティスに従っているか検証
6. **レポート生成**: severity で問題を分類

## 使用タイミング (When to Use)

`/cpp-review` を使用する場合:
- C++ コードを記述または変更した後
- C++ 変更をコミットする前
- C++ コードを含む pull request をレビューするとき
- 新しい C++ コードベースにオンボーディングするとき
- メモリ安全性の問題を確認するとき

## レビューカテゴリ (Review Categories)

### CRITICAL（必須修正）(CRITICAL (Must Fix))
- RAII なしの raw `new`/`delete`
- バッファオーバーフローと use-after-free
- 同期なしのデータ競合
- `system()` 経由のコマンドインジェクション
- 未初期化変数の読み取り
- null ポインタのデリファレンス

### HIGH（修正推奨）(HIGH (Should Fix))
- Rule of Five 違反
- `std::lock_guard` / `std::scoped_lock` の欠如
- 適切なライフタイム管理なしの detached スレッド
- `static_cast`/`dynamic_cast` の代わりの C スタイルキャスト
- `const` correctness の欠如

### MEDIUM（検討）(MEDIUM (Consider))
- 不要なコピー（`const&` の代わりに値渡し）
- 既知サイズのコンテナでの `reserve()` 欠如
- ヘッダー内の `using namespace std;`
- 重要な戻り値への `[[nodiscard]]` 欠如
- 過度に複雑なテンプレートメタプログラミング

## 実行される自動チェック (Automated Checks Run)

```bash
# Static analysis
clang-tidy --checks='*,-llvmlibc-*' src/*.cpp -- -std=c++17

# Additional analysis
cppcheck --enable=all --suppress=missingIncludeSystem src/

# Build with warnings
cmake --build build -- -Wall -Wextra -Wpedantic
```

## 使用例 (Example Usage)

```text
User: /cpp-review

Agent:
# C++ Code Review Report

## Files Reviewed
- src/handler/user.cpp (modified)
- src/service/auth.cpp (modified)

## Static Analysis Results
✓ clang-tidy: 2 warnings
✓ cppcheck: No issues

## Issues Found

[CRITICAL] Memory Leak
File: src/service/auth.cpp:45
Issue: Raw `new` without matching `delete`
```cpp
auto* session = new Session(userId);  // Memory leak!
cache[userId] = session;
```
Fix: Use `std::unique_ptr`
```cpp
auto session = std::make_unique<Session>(userId);
cache[userId] = std::move(session);
```

[HIGH] Missing const Reference
File: src/handler/user.cpp:28
Issue: Large object passed by value
```cpp
void processUser(User user) {  // Unnecessary copy
```
Fix: Pass by const reference
```cpp
void processUser(const User& user) {
```

## Summary
- CRITICAL: 1
- HIGH: 1
- MEDIUM: 0

Recommendation: FAIL: Block merge until CRITICAL issue is fixed
```

## 承認基準 (Approval Criteria)

| Status | Condition |
|--------|-----------|
| PASS: Approve | CRITICAL または HIGH の問題なし |
| WARNING: Warning | MEDIUM のみ（注意してマージ） |
| FAIL: Block | CRITICAL または HIGH の問題あり |

## 他コマンドとの連携 (Integration with Other Commands)

- まず `/cpp-test` でテストが通ることを確認
- ビルドエラーがある場合は `/cpp-build` を使用
- コミット前に `/cpp-review` を使用
- C++ 以外の懸念には `/code-review` を使用

## 関連 (Related)

- Agent: `agents/cpp-reviewer.md`
- Skills: `skills/cpp-coding-standards/`, `skills/cpp-testing/`
