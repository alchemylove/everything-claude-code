---
paths:
  - "**/*.py"
  - "**/*.pyi"
---
# Python セキュリティ (Python Security)

> 本ファイルは [common/security.md](../common/security.md) を Python 固有の内容で拡張します。

## Secret 管理 (Secret Management)

```python
import os
from dotenv import load_dotenv

load_dotenv()

api_key = os.environ["OPENAI_API_KEY"]  # Raises KeyError if missing
```

## セキュリティスキャン (Security Scanning)

- 静的セキュリティ分析に **bandit** を使用する:
  ```bash
  bandit -r src/
  ```

## 参照 (Reference)

Django 固有のセキュリティガイドライン（該当する場合）は skill: `django-security` を参照。
