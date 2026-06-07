---
paths:
  - "**/*.py"
  - "**/*.pyi"
---
# Python テスト (Python Testing)

> 本ファイルは [common/testing.md](../common/testing.md) を Python 固有の内容で拡張します。

## フレームワーク (Framework)

テストフレームワークに **pytest** を使用する。

## カバレッジ (Coverage)

```bash
pytest --cov=src --cov-report=term-missing
```

## テスト構成 (Test Organization)

テスト分類に `pytest.mark` を使用する:

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
