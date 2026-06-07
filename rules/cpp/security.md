---
paths:
  - "**/*.cpp"
  - "**/*.hpp"
  - "**/*.cc"
  - "**/*.hh"
  - "**/*.cxx"
  - "**/*.h"
  - "**/CMakeLists.txt"
---
# C++ セキュリティ (C++ Security)

> このファイルは [common/security.md](../common/security.md) を拡張し、C++ 固有の内容を追加する。

## メモリ安全性 (Memory Safety)

- 生の `new`/`delete` は使わない — smart pointer を使う
- C スタイル配列は使わない — `std::array` または `std::vector` を使う
- `malloc`/`free` は使わない — C++ の割り当てを使う
- 絶対に必要な場合以外は `reinterpret_cast` を避ける

## バッファオーバーフロー (Buffer Overflows)

- `char*` より `std::string` を使う
- 安全性が重要な場合は境界チェック付きアクセスに `.at()` を使う
- `strcpy`、`strcat`、`sprintf` は使わない — `std::string` または `fmt::format` を使う

## 未定義動作 (Undefined Behavior)

- 変数は必ず初期化する
- 符号付き整数のオーバーフローを避ける
- null または dangling pointer を dereference しない
- CI で sanitizer を使う:
  ```bash
  cmake -DCMAKE_CXX_FLAGS="-fsanitize=address,undefined" ..
  ```

## 静的解析 (Static Analysis)

- 自動チェックに **clang-tidy** を使う:
  ```bash
  clang-tidy --checks='*' src/*.cpp
  ```
- 追加解析に **cppcheck** を使う:
  ```bash
  cppcheck --enable=all src/
  ```

## 参照 (Reference)

詳細なセキュリティガイドラインについては skill: `cpp-coding-standards` を参照。
