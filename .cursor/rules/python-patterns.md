---
description: "Python patterns: common ルールの拡張"
globs: ["**/*.py", "**/*.pyi"]
alwaysApply: false
---
# Python Patterns (Python Patterns)

> このファイルは common patterns ルールを Python 固有の内容で拡張する。

## Protocol (Duck Typing)

```python
from typing import Protocol

class Repository(Protocol):
    def find_by_id(self, id: str) -> dict | None: ...
    def save(self, entity: dict) -> dict: ...
```

## Dataclasses as DTOs

```python
from dataclasses import dataclass

@dataclass
class CreateUserRequest:
    name: str
    email: str
    age: int | None = None
```

## Context Managers & Generators

- リソース管理には context manager（`with` 文）を使用
- lazy evaluation とメモリ効率の良い反復には generator を使用

## 参照 (Reference)

decorator、並行処理、package 構成を含む包括的なパターンは skill: `python-patterns` を参照。
