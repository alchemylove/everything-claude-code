---
description: C++ のビルドエラー、CMake の問題、リンカーエラーを段階的に修正。最小限の外科的修正のため cpp-build-resolver エージェントを起動する。
---

# C++ ビルドと修正 (C++ Build and Fix)

このコマンドは **cpp-build-resolver** エージェントを起動し、最小限の変更で C++ ビルドエラーを段階的に修正する。

## このコマンドの内容 (What This Command Does)

1. **診断の実行**: `cmake --build`、`clang-tidy`、`cppcheck` を実行
2. **エラーの解析**: ファイルごとにグループ化し、severity でソート
3. **段階的修正**: 一度に 1 エラーずつ
4. **各修正の検証**: 変更のたびにビルドを再実行
5. **サマリーの報告**: 修正内容と残件を表示

## 使用タイミング (When to Use)

`/cpp-build` を使用する場合:
- `cmake --build build` がエラーで失敗する
- リンカーエラー（未定義参照、多重定義）
- テンプレートインスタンス化の失敗
- include/依存関係の問題
- pull 後にビルドが壊れた場合

## 実行される診断コマンド (Diagnostic Commands Run)

```bash
# CMake configure
cmake -B build -S .

# Build
cmake --build build 2>&1 | head -100

# Static analysis (if available)
clang-tidy src/*.cpp -- -std=c++17
cppcheck --enable=all src/
```

## セッション例 (Example Session)

```text
User: /cpp-build

Agent:
# C++ Build Resolution

## Initial Diagnostics

```bash
$ cmake --build build
src/service/user.cpp:25:15: error: use of undeclared identifier 'UserRepository'
src/handler/api.cpp:42:9: error: no matching function for call to 'process'
src/handler/api.cpp:58:1: error: non-void function does not return a value
```

Errors found: 3

## Fix 1: Undeclared Identifier

File: src/service/user.cpp:25
Error: use of undeclared identifier 'UserRepository'
Cause: Missing include

```cpp
// Added include
#include "repository/user_repository.hpp"
```

```bash
$ cmake --build build
# 2 errors remaining
```

## Fix 2: No Matching Function

File: src/handler/api.cpp:42
Error: no matching function for call to 'process'

```cpp
// Changed
process(params.get("count"));
// To
process(std::stoi(params.get("count")));
```

```bash
$ cmake --build build
# 1 error remaining
```

## Fix 3: Missing Return

File: src/handler/api.cpp:58
Error: non-void function does not return a value

```cpp
std::optional<User> getUser(const std::string& id) {
    if (id.empty()) {
        return std::nullopt;
    }
    auto user = findUser(id);
    // Added missing return
    return user;
}
```

```bash
$ cmake --build build
# Build successful!
```

## Final Verification

```bash
$ ctest --test-dir build --output-on-failure
Test project build
    1/5 Test #1: unit_tests ........   Passed    0.02 sec
    2/5 Test #2: integration_tests    Passed    0.15 sec
All tests passed.
```

## Summary

| Metric | Count |
|--------|-------|
| Build errors fixed | 3 |
| Linker errors fixed | 0 |
| Files modified | 2 |
| Remaining issues | 0 |

Build Status: PASS: SUCCESS
```

## よくある修正対象エラー (Common Errors Fixed)

| Error | Typical Fix |
|-------|-------------|
| `undeclared identifier` | `#include` を追加するか typo を修正 |
| `no matching function` | 引数型を修正するか overload を追加 |
| `undefined reference` | ライブラリをリンクするか実装を追加 |
| `multiple definition` | `inline` を使うか .cpp に移動 |
| `incomplete type` | forward decl を `#include` に置き換え |
| `no member named X` | メンバー名を修正するか include を追加 |
| `cannot convert X to Y` | 適切なキャストを追加 |
| `CMake Error` | CMakeLists.txt の設定を修正 |

## 修正戦略 (Fix Strategy)

1. **コンパイルエラーを最優先** — コードはコンパイル可能であること
2. **リンカーエラーを次に** — 未定義参照を解決
3. **警告を 3 番目** — `-Wall -Wextra` で修正
4. **一度に 1 修正** — 各変更を検証
5. **最小限の変更** — リファクタリングせず、修正のみ

## 停止条件 (Stop Conditions)

エージェントは以下の場合に停止して報告する:
- 3 回試行後も同じエラーが続く
- 修正がさらに多くのエラーを導入する
- アーキテクチャ変更が必要
- 外部依存関係が不足

## 関連コマンド (Related Commands)

- `/cpp-test` — ビルド成功後にテストを実行
- `/cpp-review` — コード品質をレビュー
- `verification-loop` skill — フル検証ループ

## 関連 (Related)

- Agent: `agents/cpp-build-resolver.md`
- Skill: `skills/cpp-coding-standards/`
