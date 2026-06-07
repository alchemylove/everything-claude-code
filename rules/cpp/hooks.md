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
# C++ フック (C++ Hooks)

> このファイルは [common/hooks.md](../common/hooks.md) を拡張し、C++ 固有の内容を追加する。

## ビルド Hooks (Build Hooks)

C++ の変更をコミットする前に、次のチェックを実行する:

```bash
# Format check
clang-format --dry-run --Werror src/*.cpp src/*.hpp

# Static analysis
clang-tidy src/*.cpp -- -std=c++17

# Build
cmake --build build

# Tests
ctest --test-dir build --output-on-failure
```

## 推奨 CI パイプライン (Recommended CI Pipeline)

1. **clang-format** — フォーマットチェック
2. **clang-tidy** — 静的解析
3. **cppcheck** — 追加解析
4. **cmake build** — コンパイル
5. **ctest** — sanitizer 付きテスト実行
