---
description: "Python security: common ルールの拡張"
globs: ["**/*.py", "**/*.pyi"]
alwaysApply: false
---
# Python Security (Python Security)

> このファイルは common security ルールを Python 固有の内容で拡張する。

## Secret 管理 (Secret Management)

```python
import os
from dotenv import load_dotenv

load_dotenv()

api_key = os.environ["OPENAI_API_KEY"]  # Raises KeyError if missing
```

## セキュリティスキャン (Security Scanning)

- static セキュリティ分析に **bandit** を使用:
  ```bash
  bandit -r src/
  ```

## 参照 (Reference)

Django 固有のセキュリティガイドライン（該当する場合）は skill: `django-security` を参照。
