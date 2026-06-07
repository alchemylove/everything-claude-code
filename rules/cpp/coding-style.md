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
# C++ コーディングスタイル (C++ Coding Style)

> このファイルは [common/coding-style.md](../common/coding-style.md) を拡張し、C++ 固有の内容を追加する。

## モダン C++ (Modern C++ (C++17/20/23))

- C スタイルの構文より **モダン C++ の機能** を優先する
- 文脈から型が明らかな場合は `auto` を使う
- コンパイル時定数には `constexpr` を使う
- structured bindings を使う: `auto [key, value] = map_entry;`

## リソース管理 (Resource Management)

- **RAII を徹底** — 手動の `new`/`delete` は使わない
- 排他的所有権には `std::unique_ptr` を使う
- 共有所有権が本当に必要な場合のみ `std::shared_ptr` を使う
- 生の `new` より `std::make_unique` / `std::make_shared` を使う

## 命名規則 (Naming Conventions)

- Types/Classes: `PascalCase`
- Functions/Methods: `snake_case` または `camelCase`（プロジェクトの規約に従う）
- Constants: `kPascalCase` または `UPPER_SNAKE_CASE`
- Namespaces: `lowercase`
- Member variables: `snake_case_`（末尾アンダースコア）または `m_` プレフィックス

## フォーマット (Formatting)

- **clang-format** を使う — スタイル論争はしない
- コミット前に `clang-format -i <file>` を実行する

## 参照 (Reference)

包括的な C++ コーディング標準とガイドラインについては skill: `cpp-coding-standards` を参照。
