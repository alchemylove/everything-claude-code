---
description: "Python coding style: common ルールの拡張"
globs: ["**/*.py", "**/*.pyi"]
alwaysApply: false
---
# Python Coding Style (Python Coding Style)

> このファイルは common coding style ルールを Python 固有の内容で拡張する。

## 標準 (Standards)

- **PEP 8** 規約に従う
- すべての関数シグネチャに **type annotation** を使用

## 不変性 (Immutability)

不変データ構造を優先:

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
- lint に **ruff**

## 参照 (Reference)

包括的な Python イディオムとパターンは skill: `python-patterns` を参照。
