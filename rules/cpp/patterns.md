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
# C++ パターン (C++ Patterns)

> このファイルは [common/patterns.md](../common/patterns.md) を拡張し、C++ 固有の内容を追加する。

## RAII (Resource Acquisition Is Initialization)

リソースの寿命をオブジェクトの寿命に結びつける:

```cpp
class FileHandle {
public:
    explicit FileHandle(const std::string& path) : file_(std::fopen(path.c_str(), "r")) {}
    ~FileHandle() { if (file_) std::fclose(file_); }
    FileHandle(const FileHandle&) = delete;
    FileHandle& operator=(const FileHandle&) = delete;
private:
    std::FILE* file_;
};
```

## Rule of Five/Zero

- **Rule of Zero**: カスタム destructor、copy/move constructor、assignment が不要なクラスを優先する
- **Rule of Five**: destructor/copy-ctor/copy-assign/move-ctor/move-assign のいずれかを定義する場合は、5 つすべてを定義する

## 値セマンティクス (Value Semantics)

- 小さく自明な型は値渡し
- 大きな型は `const&` で渡す
- 値で返す（RVO/NRVO に依存）
- sink パラメータには move semantics を使う

## エラーハンドリング (Error Handling)

- 例外的な状況には exception を使う
- 存在しない可能性のある値には `std::optional` を使う
- 想定される失敗には `std::expected`（C++23）または result 型を使う

## 参照 (Reference)

包括的な C++ パターンとアンチパターンについては skill: `cpp-coding-standards` を参照。
