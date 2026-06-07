---
description: C++ 向け TDD ワークフローを強制。GoogleTest でテストを先に書き、その後実装。gcov/lcov でカバレッジを検証。
---

# C++ TDD コマンド (C++ TDD Command)

このコマンドは GoogleTest/GoogleMock と CMake/CTest を用いた C++ コード向けのテスト駆動開発手法を強制する。

## このコマンドの内容 (What This Command Does)

1. **インターフェースの定義**: クラス/関数シグネチャを先にスキャフォールド
2. **テストの記述**: 包括的な GoogleTest テストケースを作成（RED）
3. **テストの実行**: 正しい理由で失敗することを確認
4. **コードの実装**: 通過する最小限のコードを記述（GREEN）
5. **リファクタリング**: テストをグリーンに保ちながら改善
6. **カバレッジの確認**: 80% 以上のカバレッジを確保

## 使用タイミング (When to Use)

`/cpp-test` を使用する場合:
- 新しい C++ 関数またはクラスを実装するとき
- 既存コードにテストカバレッジを追加するとき
- バグ修正（まず失敗するテストを書く）
- 重要なビジネスロジックの構築
- C++ での TDD ワークフローを学ぶとき

## TDD サイクル (TDD Cycle)

```
RED     → Write failing GoogleTest test
GREEN   → Implement minimal code to pass
REFACTOR → Improve code, tests stay green
REPEAT  → Next test case
```

## セッション例 (Example Session)

````
User: /cpp-test I need a function to validate email addresses

Agent:
# TDD Session: Email Validator

## Step 1: Define Interface

```cpp
// validator/email.hpp
#pragma once
#include <string>
#include <expected>

enum class EmailError {
    Empty,
    InvalidFormat
};

std::expected<void, EmailError> validate_email(const std::string& email);
```

## Step 2: Write Tests (RED)

```cpp
// validator/email_test.cpp
#include <gtest/gtest.h>
#include "email.hpp"

TEST(ValidateEmail, AcceptsSimpleEmail) {
    auto result = validate_email("user@example.com");
    EXPECT_TRUE(result.has_value());
}

TEST(ValidateEmail, AcceptsSubdomain) {
    EXPECT_TRUE(validate_email("user@mail.example.com").has_value());
}

TEST(ValidateEmail, AcceptsPlus) {
    EXPECT_TRUE(validate_email("user+tag@example.com").has_value());
}

TEST(ValidateEmail, RejectsEmpty) {
    auto result = validate_email("");
    ASSERT_FALSE(result.has_value());
    EXPECT_EQ(result.error(), EmailError::Empty);
}

TEST(ValidateEmail, RejectsNoAtSign) {
    EXPECT_FALSE(validate_email("userexample.com").has_value());
}

TEST(ValidateEmail, RejectsNoDomain) {
    EXPECT_FALSE(validate_email("user@").has_value());
}

TEST(ValidateEmail, RejectsNoLocalPart) {
    EXPECT_FALSE(validate_email("@example.com").has_value());
}
```

## Step 3: Run Tests - Verify FAIL

```bash
$ cmake --build build && ctest --test-dir build --output-on-failure

1/1 Test #1: email_validator_test .....***Failed
    --- undefined reference to `validate_email`

FAIL
```

✓ Tests fail as expected (unimplemented).

## Step 4: Implement Minimal Code (GREEN)

```cpp
// validator/email.cpp
#include "email.hpp"
#include <regex>

std::expected<void, EmailError> validate_email(const std::string& email) {
    if (email.empty()) {
        return std::unexpected(EmailError::Empty);
    }
    static const std::regex pattern(R"([a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,})");
    if (!std::regex_match(email, pattern)) {
        return std::unexpected(EmailError::InvalidFormat);
    }
    return {};
}
```

## Step 5: Run Tests - Verify PASS

```bash
$ cmake --build build && ctest --test-dir build --output-on-failure

1/1 Test #1: email_validator_test .....   Passed    0.01 sec

100% tests passed.
```

✓ All tests passing!

## Step 6: Check Coverage

```bash
$ cmake -DCMAKE_CXX_FLAGS="--coverage" -B build && cmake --build build
$ ctest --test-dir build
$ lcov --capture --directory build --output-file coverage.info
$ lcov --list coverage.info

validator/email.cpp     | 100%
```

✓ Coverage: 100%

## TDD Complete!
````

## テストパターン (Test Patterns)

### 基本テスト (Basic Tests)
```cpp
TEST(SuiteName, TestName) {
    EXPECT_EQ(add(2, 3), 5);
    EXPECT_NE(result, nullptr);
    EXPECT_TRUE(is_valid);
    EXPECT_THROW(func(), std::invalid_argument);
}
```

### フィクスチャ (Fixtures)
```cpp
class DatabaseTest : public ::testing::Test {
protected:
    void SetUp() override { db_ = create_test_db(); }
    void TearDown() override { db_.reset(); }
    std::unique_ptr<Database> db_;
};

TEST_F(DatabaseTest, InsertsRecord) {
    db_->insert("key", "value");
    EXPECT_EQ(db_->get("key"), "value");
}
```

### パラメータ化テスト (Parameterized Tests)
```cpp
class PrimeTest : public ::testing::TestWithParam<std::pair<int, bool>> {};

TEST_P(PrimeTest, ChecksPrimality) {
    auto [input, expected] = GetParam();
    EXPECT_EQ(is_prime(input), expected);
}

INSTANTIATE_TEST_SUITE_P(Primes, PrimeTest, ::testing::Values(
    std::make_pair(2, true),
    std::make_pair(4, false),
    std::make_pair(7, true)
));
```

## カバレッジコマンド (Coverage Commands)

```bash
# Build with coverage
cmake -DCMAKE_CXX_FLAGS="--coverage" -DCMAKE_EXE_LINKER_FLAGS="--coverage" -B build

# Run tests
cmake --build build && ctest --test-dir build

# Generate coverage report
lcov --capture --directory build --output-file coverage.info
lcov --remove coverage.info '/usr/*' --output-file coverage.info
genhtml coverage.info --output-directory coverage_html
```

## カバレッジ目標 (Coverage Targets)

| Code Type | Target |
|-----------|--------|
| Critical business logic | 100% |
| Public APIs | 90%+ |
| General code | 80%+ |
| Generated code | Exclude |

## TDD ベストプラクティス (TDD Best Practices)

**DO:**
- 実装の前に、まずテストを書く
- 各変更後にテストを実行
- 適切な場合は `ASSERT_*`（停止）より `EXPECT_*`（継続）を使う
- 実装の詳細ではなく振る舞いをテスト
- エッジケースを含める（空、null、最大値、境界条件）

**DON'T:**
- テストより先に実装を書く
- RED フェーズをスキップする
- プライベートメソッドを直接テストする（公開 API 経由でテスト）
- テストで `sleep` を使う
- フレーキーなテストを無視する

## 関連コマンド (Related Commands)

- `/cpp-build` — ビルドエラーを修正
- `/cpp-review` — 実装後にコードをレビュー
- `verification-loop` skill — フル検証ループを実行

## 関連 (Related)

- Skill: `skills/cpp-testing/`
- Skill: `skills/tdd-workflow/`
