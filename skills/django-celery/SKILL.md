---
name: django-celery
description: Django + Celery async task patterns — configuration, task design, beat scheduling, retries, canvas workflows, monitoring, and testing. Use when adding background jobs, scheduled tasks, or async processing to a Django app.
origin: ECC
---

# Django + Celery 非同期タスク (Django + Celery Async Task Patterns)

Django でのバックグラウンドジョブと非同期処理。

## 使用時期 (When to Activate)

- メール送信をバックグラウンドで実行
- 重い処理をスケジュール
- 定期的なタスクを実行（日報、クリーンアップ）
- 外部API呼び出しをキューイング
- 複雑なワークフローを調整

## セットアップ (Project Setup)

### 1. Celery インストール (Installation)

```bash
pip install celery redis
```

### 2. タスク定義 (`celery.py` — App Entrypoint)

```python
from celery import shared_task

@shared_task
def send_email(recipient):
    # メール送信ロジック
    pass
```

### 3. ワーカー起動 (Django Settings)

```bash
celery -A myapp worker -l info
```

## タスク (Running Workers)

### 非同期実行 (Task Design Patterns)

```python
send_email.delay(recipient)  # すぐにキューに追加、非同期実行
```

### スケジューリング (Basic Task)

```python
from celery.schedules import crontab

app.conf.beat_schedule = {
    'send-report-daily': {
        'task': 'app.tasks.send_report',
        'schedule': crontab(hour=9, minute=0),
    },
}
```

## エラーハンドリング (Retryable Task)

- [ ] リトライロジック実装
- [ ] デッドレター処理
- [ ] ロギング構成
- [ ] モニタリング設定（Flower）

詳細については、ドキュメントを参照してください。
