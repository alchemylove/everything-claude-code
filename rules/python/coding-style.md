---
paths:
  - "**/*.py"
  - "**/*.pyi"
---
# Python コーディングスタイル (Python Coding Style)

> 本ファイルは [common/coding-style.md](../common/coding-style.md) を Python 固有の内容で拡張します。

## 標準 (Standards)

- **PEP 8** 規約に従う
- すべての関数シグネチャに **type annotations** を使用する

## 不変性 (Immutability)

不変データ構造を優先する:

```python
from dataclasses import dataclass

@dataclass(frozen=True)
class User:
    name: str
    email: str

from typing import NamedTuple

class Point(NamedTuple):
    x: float
    y: float
```

## フォーマット (Formatting)

- コードフォーマットに **black**
- import ソートに **isort**
- リンティングに **ruff**

## 参照 (Reference)

包括的な Python イディオムとパターンは skill: `python-patterns` を参照。
