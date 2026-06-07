---
paths:
  - "**/*.py"
  - "**/*.pyi"
---
# Python パターン (Python Patterns)

> 本ファイルは [common/patterns.md](../common/patterns.md) を Python 固有の内容で拡張します。

## Protocol（Duck Typing）(Protocol (Duck Typing))

```python
from typing import Protocol

class Repository(Protocol):
    def find_by_id(self, id: str) -> dict | None: ...
    def save(self, entity: dict) -> dict: ...
```

## DTO としての Dataclass (Dataclasses as DTOs)

```python
from dataclasses import dataclass

@dataclass
class CreateUserRequest:
    name: str
    email: str
    age: int | None = None
```

## Context Manager と Generator (Context Managers & Generators)

- リソース管理には context manager（`with` 文）を使用する
- 遅延評価とメモリ効率のよい反復には generator を使用する

## 参照 (Reference)

decorator、並行処理、パッケージ構成を含む包括的パターンは skill: `python-patterns` を参照。
