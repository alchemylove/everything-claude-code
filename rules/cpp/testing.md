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
# C++ テスト (C++ Testing)

> このファイルは [common/testing.md](../common/testing.md) を拡張し、C++ 固有の内容を追加する。

## フレームワーク (Framework)

**GoogleTest**（gtest/gmock）と **CMake/CTest** を使う。

## テスト実行 (Running Tests)

```bash
cmake --build build && ctest --test-dir build --output-on-failure
```

## カバレッジ (Coverage)

```bash
cmake -DCMAKE_CXX_FLAGS="--coverage" -DCMAKE_EXE_LINKER_FLAGS="--coverage" ..
cmake --build .
ctest --output-on-failure
lcov --capture --directory . --output-file coverage.info
```

## Sanitizer

CI では常に sanitizer 付きでテストを実行する:

```bash
cmake -DCMAKE_CXX_FLAGS="-fsanitize=address,undefined" ..
```

## 参照 (Reference)

詳細な C++ テストパターン、TDD ワークフロー、GoogleTest/GMock の使い方については skill: `cpp-testing` を参照。
