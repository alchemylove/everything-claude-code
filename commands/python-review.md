---
description: PEP 8 準拠、type hints、セキュリティ、Pythonic イディオム向けの包括的 Python コードレビュー。python-reviewer agent を起動する。
---

# Python コードレビュー (Python Code Review)

このコマンドは、Python 固有の包括的コードレビューのために **python-reviewer** agent を起動する。

## このコマンドの内容 (What This Command Does)

1. **Python 変更の特定**: `git diff` で変更された `.py` ファイルを検出
2. **静的解析の実行**: `ruff`, `mypy`, `pylint`, `black --check` を実行
3. **セキュリティスキャン**: SQL injection、command injection、unsafe deserialization を確認
4. **型安全性レビュー**: type hints と mypy エラーを分析
5. **Pythonic コードチェック**: PEP 8 と Python ベストプラクティスに従っているか検証
6. **レポート生成**: 重大度別に issue を分類

## 使用タイミング (When to Use)

以下の場合に `/python-review` を使用する:
- Python コードを書いたまたは変更した後
- Python 変更をコミットする前
- Python コードを含む pull request をレビューするとき
- 新しい Python コードベースにオンボーディングするとき
- Pythonic パターンとイディオムを学ぶとき

## レビューカテゴリ (Review Categories)

### CRITICAL（必須修正）
- SQL/Command injection の脆弱性
- 安全でない eval/exec の使用
- Pickle の unsafe deserialization
- ハードコードされた認証情報
- YAML の unsafe load
- エラーを隠す bare except

### HIGH（修正推奨）
- public 関数に type hints がない
- mutable default 引数
- 例外を黙って飲み込む
- リソースに context manager を使っていない
- comprehension の代わりに C スタイルのループ
- isinstance() の代わりに type() を使用
- lock なしの race condition

### MEDIUM（検討）
- PEP 8 フォーマット違反
- public 関数に docstring がない
- logging の代わりに print
- 非効率な文字列操作
- 名前付き定数のないマジックナンバー
- フォーマットに f-string を使っていない
- 不要な list 作成

## 実行される自動チェック (Automated Checks Run)

```bash
# Type checking
mypy .

# Linting and formatting
ruff check .
black --check .
isort --check-only .

# Security scanning
bandit -r .

# Dependency audit
pip-audit
safety check

# Testing
pytest --cov=app --cov-report=term-missing
```

## 使用例 (Example Usage)

```text
User: /python-review

Agent:
# Python Code Review Report

## Files Reviewed
- app/routes/user.py (modified)
- app/services/auth.py (modified)

## Static Analysis Results
✓ ruff: No issues
✓ mypy: No errors
WARNING: black: 2 files need reformatting
✓ bandit: No security issues

## Issues Found

[CRITICAL] SQL Injection vulnerability
File: app/routes/user.py:42
Issue: User input directly interpolated into SQL query
```python
query = f"SELECT * FROM users WHERE id = {user_id}"  # Bad
```
Fix: Use parameterized query
```python
query = "SELECT * FROM users WHERE id = %s"  # Good
cursor.execute(query, (user_id,))
```

[HIGH] Mutable default argument
File: app/services/auth.py:18
Issue: Mutable default argument causes shared state
```python
def process_items(items=[]):  # Bad
    items.append("new")
    return items
```
Fix: Use None as default
```python
def process_items(items=None):  # Good
    if items is None:
        items = []
    items.append("new")
    return items
```

[MEDIUM] Missing type hints
File: app/services/auth.py:25
Issue: Public function without type annotations
```python
def get_user(user_id):  # Bad
    return db.find(user_id)
```
Fix: Add type hints
```python
def get_user(user_id: str) -> Optional[User]:  # Good
    return db.find(user_id)
```

[MEDIUM] Not using context manager
File: app/routes/user.py:55
Issue: File not closed on exception
```python
f = open("config.json")  # Bad
data = f.read()
f.close()
```
Fix: Use context manager
```python
with open("config.json") as f:  # Good
    data = f.read()
```

## Summary
- CRITICAL: 1
- HIGH: 1
- MEDIUM: 2

Recommendation: FAIL: Block merge until CRITICAL issue is fixed

## Formatting Required
Run: `black app/routes/user.py app/services/auth.py`
```

## 承認基準 (Approval Criteria)

| Status | Condition |
|--------|-----------|
| PASS: Approve | CRITICAL または HIGH issue なし |
| WARNING: Warning | MEDIUM issue のみ（注意してマージ） |
| FAIL: Block | CRITICAL または HIGH issue あり |

## 他コマンドとの連携 (Integration with Other Commands)

- まず `tdd-workflow` skill でテストが通ることを確認
- Python 以外の懸念には `/code-review` を使用
- コミット前に `/python-review` を使用
- 静的解析ツールが失敗した場合は `/build-fix` を使用

## フレームワーク固有のレビュー (Framework-Specific Reviews)

### Django プロジェクト
レビュアーは以下を確認する:
- N+1 query（`select_related` と `prefetch_related` を使用）
- model 変更に migration がない
- ORM で済むのに raw SQL を使用
- 複数ステップ操作に `transaction.atomic()` がない

### FastAPI プロジェクト
レビュアーは以下を確認する:
- CORS の誤設定
- リクエスト検証用の Pydantic models
- response models の正しさ
- 適切な async/await の使用
- dependency injection パターン

### Flask プロジェクト
レビュアーは以下を確認する:
- context 管理（app context、request context）
- 適切なエラーハンドリング
- Blueprint の構成
- 設定管理

## 関連 (Related)

- Agent: `agents/python-reviewer.md`
- Skills: `skills/python-patterns/`, `skills/python-testing/`

## よくある修正 (Common Fixes)

### Type Hints の追加
```python
# Before
def calculate(x, y):
    return x + y

# After
from typing import Union

def calculate(x: Union[int, float], y: Union[int, float]) -> Union[int, float]:
    return x + y
```

### Context Manager の使用
```python
# Before
f = open("file.txt")
data = f.read()
f.close()

# After
with open("file.txt") as f:
    data = f.read()
```

### List Comprehension の使用
```python
# Before
result = []
for item in items:
    if item.active:
        result.append(item.name)

# After
result = [item.name for item in items if item.active]
```

### Mutable Default の修正
```python
# Before
def append(value, items=[]):
    items.append(value)
    return items

# After
def append(value, items=None):
    if items is None:
        items = []
    items.append(value)
    return items
```

### f-string の使用（Python 3.6+）
```python
# Before
name = "Alice"
greeting = "Hello, " + name + "!"
greeting2 = "Hello, {}".format(name)

# After
greeting = f"Hello, {name}!"
```

### ループ内の文字列連結の修正
```python
# Before
result = ""
for item in items:
    result += str(item)

# After
result = "".join(str(item) for item in items)
```

## Python バージョン互換性 (Python Version Compatibility)

レビュアーは、新しい Python バージョンの機能を使用している場合に注記する:

| Feature | Minimum Python |
|---------|----------------|
| Type hints | 3.5+ |
| f-strings | 3.6+ |
| Walrus operator (`:=`) | 3.8+ |
| Position-only parameters | 3.8+ |
| Match statements | 3.10+ |
| Type unions (&#96;x &#124; None&#96;) | 3.10+ |

プロジェクトの `pyproject.toml` または `setup.py` が正しい最小 Python バージョンを指定していることを確認する。
