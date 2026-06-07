---
name: social-publisher
description: Agent-driven scheduling and publishing of social media posts across 13 platforms via SocialClaw. Use when the user wants to publish to X, LinkedIn, Instagram, Facebook Pages, TikTok, Discord, Telegram, YouTube, Reddit, WordPress, or Pinterest — or when managing campaigns, uploading media, or monitoring post delivery status.
origin: community
---

# Social Publisher (SocialClaw)

Claude Code を [SocialClaw](https://getsocialclaw.com) に接続し、単一 workspace API key で 13 プラットフォームへ agent 駆動 publishing を行う。

## 有効化タイミング (When to Activate)

- X、LinkedIn、Instagram、TikTok などへ content を publish
- 複数プラットフォームへ同時 post campaign をスケジュール
- social post 用 media を upload
- 公開前に post schedule を検証
- publishing run status と配信 analytics を監視

## セットアップ (Setup)

```bash
# Required: workspace API key from https://getsocialclaw.com/dashboard
export SC_API_KEY="<workspace-key>"

# Verify access
curl -sS -H "Authorization: Bearer $SC_API_KEY" https://getsocialclaw.com/v1/keys/validate

# Install CLI (optional but recommended)
npm install -g socialclaw@0.1.12
socialclaw login --api-key <workspace-key>
```

## コアワークフロー (Core Workflow)

### 1. 接続済み account を一覧
```bash
socialclaw accounts list --json
```

未接続の場合:
```bash
socialclaw accounts connect --provider x --open
socialclaw accounts connect --provider linkedin --open
```

### 2. media を upload（任意）
```bash
socialclaw assets upload --file ./image.png --json
# → { "asset_id": "..." }
```

### 3. schedule.json を構築
```json
{
  "posts": [
    {
      "provider": "x",
      "account_id": "<account-id>",
      "text": "Post text here",
      "scheduled_at": "2026-06-01T10:00:00Z"
    }
  ]
}
```

### 4. 公開前に validate
```bash
socialclaw validate -f schedule.json --json
```

### 5. 公開
```bash
socialclaw apply -f schedule.json --json
# → { "run_id": "..." }
```

### 6. 監視
```bash
socialclaw status --run-id <run-id> --json
socialclaw posts list --json
```

## 対応 Provider (Supported Providers)

| Provider | Key |
|----------|-----|
| X (Twitter) | `x` |
| LinkedIn profile | `linkedin` |
| LinkedIn page | `linkedin_page` |
| Instagram Business | `instagram_business` |
| Instagram standalone | `instagram` |
| Facebook Page | `facebook` |
| TikTok | `tiktok` |
| YouTube | `youtube` |
| Reddit | `reddit` |
| WordPress | `wordpress` |
| Discord | `discord` |
| Telegram | `telegram` |
| Pinterest | `pinterest` |

## セキュリティ (Security)

- 外向き request は `getsocialclaw.com` のみ
- Provider OAuth は SocialClaw dashboard 内 — agent に per-provider secret は露出しない
- `SC_API_KEY` は workspace スコープ key

## 関連 Skill (Related Skills)

- `x-api` — 直接 X/Twitter API 操作
- `social-graph-ranker` — outreach targeting 向け network 分析

## ソース (Source)

- npm: `npm install -g socialclaw@0.1.12`
- Dashboard: [SocialClaw dashboard](https://getsocialclaw.com/dashboard)
