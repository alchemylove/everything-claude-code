---
description: "Python testing: common ルールの拡張"
globs: ["**/*.py", "**/*.pyi"]
alwaysApply: false
---
# Python Testing (Python Testing)

> このファイルは common testing ルールを Python 固有の内容で拡張する。

## Framework

testing framework には **pytest** を使用。

## Coverage

```bash
pytest --cov=src --cov-report=term-missing
```

## Test 構成 (Test Organization)

test のカテゴリ分けに `pytest.mark` を使用:

```python
import pytest

@pytest.mark.unit
def test_calculate_total():
    ...

@pytest.mark.integration
def test_database_connection():
    ...
```

## 参照 (Reference)

詳細な pytest パターンと fixture は skill: `python-testing` を参照。
