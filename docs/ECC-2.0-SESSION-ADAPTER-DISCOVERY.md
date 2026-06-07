# ECC 2.0 Session Adapter Discovery (ECC 2.0 Session Adapter Discovery)

## 目的 (Purpose)

このドキュメントは、2026年3月11日の ECC 2.0 control-plane 方針を、このリポジトリに既に存在する orchestration code に基づいた具体的な adapter と snapshot 設計に落とし込む。

## 現在実装済みの基盤 (Current Implemented Substrate)

リポジトリには既に実在する first-pass orchestration substrate がある:

- `scripts/lib/tmux-worktree-orchestrator.js`
  は tmux panes と isolated git worktrees をプロビジョニングする
- `scripts/orchestrate-worktrees.js`
  は現在の session launcher である
- `scripts/lib/orchestration-session.js`
  は machine-readable session snapshots を収集する
- `scripts/orchestration-status.js`
  は session name または plan file からそれらの snapshots をエクスポートする
- `commands/sessions.md`
  は Claude の local store から隣接する session-history concepts を既に公開している
- `scripts/lib/session-adapters/canonical-session.js`
  は canonical `ecc.session.v1` normalization layer を定義する
- `scripts/lib/session-adapters/dmux-tmux.js`
  は現在の orchestration snapshot collector を adapter `dmux-tmux` としてラップする
- `scripts/lib/session-adapters/claude-history.js`
  は Claude local session history を second adapter として正規化する
- `scripts/lib/session-adapters/registry.js`
  は explicit targets と target types から adapters を選択する
- `scripts/session-inspect.js`
  は adapter registry 経由で canonical read-only session snapshots を出力する

実際には、ECC は既に以下に答えられる:

- tmux-orchestrated session にどの workers が存在するか
- 各 worker がどの pane にアタッチされているか
- 各 worker にどの task、status、handoff files が存在するか
- session が active か、panes/workers がいくつあるか
- 直近の Claude local session が orchestration sessions と同じ canonical snapshot shape でどう見えたか

これは substrate を証明するのに十分である。general ECC 2.0 control plane として認定するにはまだ不十分である。

## 現在の Snapshot が実際にモデル化しているもの (What The Current Snapshot Actually Models)

`scripts/lib/orchestration-session.js` から出力される現在の snapshot model には、以下の effective fields がある:

```json
{
  "sessionName": "workflow-visual-proof",
  "coordinationDir": ".../.claude/orchestration/workflow-visual-proof",
  "repoRoot": "...",
  "targetType": "plan",
  "sessionActive": true,
  "paneCount": 2,
  "workerCount": 2,
  "workerStates": {
    "running": 1,
    "completed": 1
  },
  "panes": [
    {
      "paneId": "%95",
      "windowIndex": 1,
      "paneIndex": 0,
      "title": "seed-check",
      "currentCommand": "codex",
      "currentPath": "/tmp/worktree",
      "active": false,
      "dead": false,
      "pid": 1234
    }
  ],
  "workers": [
    {
      "workerSlug": "seed-check",
      "workerDir": ".../seed-check",
      "status": {
        "state": "running",
        "updated": "...",
        "branch": "...",
        "worktree": "...",
        "taskFile": "...",
        "handoffFile": "..."
      },
      "task": {
        "objective": "...",
        "seedPaths": ["scripts/orchestrate-worktrees.js"]
      },
      "handoff": {
        "summary": [],
        "validation": [],
        "remainingRisks": []
      },
      "files": {
        "status": ".../status.md",
        "task": ".../task.md",
        "handoff": ".../handoff.md"
      },
      "pane": {
        "paneId": "%95",
        "title": "seed-check"
      }
    }
  ]
}
```

これは既に有用な operator payload である。主な制限は、暗黙的に1つの execution style に結び付いていることである:

- tmux pane identity
- worker slug equals pane title
- markdown coordination files
- plan-file または session-name lookup rules

## ECC 1.x と ECC 2.0 のギャップ (Gap Between ECC 1.x And ECC 2.0)

ECC 1.x には現在、2つの異なる "session" surfaces がある:

1. Claude local session history
2. Orchestration runtime/session snapshots

これらの surfaces は隣接しているが、統一されていない。

欠けている ECC 2.0 layer は、以下を正規化できる harness-neutral session adapter boundary である:

- tmux-orchestrated workers
- plain Claude sessions
- Codex worktree sessions
- OpenCode sessions
- 将来の GitHub/App または remote-control sessions

この adapter layer がなければ、将来の operator UI は tmux-specific details と coordination markdown を直接読むことを強いられる。

## Adapter 境界 (Adapter Boundary)

ECC 2.0 は canonical session adapter contract を導入すべきである。

推奨する最小インターフェース:

```ts
type SessionAdapter = {
  id: string;
  canOpen(target: SessionTarget): boolean;
  open(target: SessionTarget): Promise<AdapterHandle>;
};

type AdapterHandle = {
  getSnapshot(): Promise<CanonicalSessionSnapshot>;
  streamEvents?(onEvent: (event: SessionEvent) => void): Promise<() => void>;
  runAction?(action: SessionAction): Promise<ActionResult>;
};
```

### Canonical Snapshot Shape (Canonical Snapshot Shape)

推奨する first-pass canonical payload:

```json
{
  "schemaVersion": "ecc.session.v1",
  "adapterId": "dmux-tmux",
  "session": {
    "id": "workflow-visual-proof",
    "kind": "orchestrated",
    "state": "active",
    "repoRoot": "...",
    "sourceTarget": {
      "type": "plan",
      "value": ".claude/plan/workflow-visual-proof.json"
    }
  },
  "workers": [
    {
      "id": "seed-check",
      "label": "seed-check",
      "state": "running",
      "branch": "...",
      "worktree": "...",
      "runtime": {
        "kind": "tmux-pane",
        "command": "codex",
        "pid": 1234,
        "active": false,
        "dead": false
      },
      "intent": {
        "objective": "...",
        "seedPaths": ["scripts/orchestrate-worktrees.js"]
      },
      "outputs": {
        "summary": [],
        "validation": [],
        "remainingRisks": []
      },
      "artifacts": {
        "statusFile": "...",
        "taskFile": "...",
        "handoffFile": "..."
      }
    }
  ],
  "aggregates": {
    "workerCount": 2,
    "states": {
      "running": 1,
      "completed": 1
    }
  }
}
```

これは既に存在する有用な signal を保持しつつ、control-plane contract から tmux-specific details を除去する。

## 最初にサポートする Adapters (First Adapters To Support)

### 1. `dmux-tmux`

`scripts/lib/orchestration-session.js` に既にあるロジックをラップする。

substrate が既に実在するため、これが最も容易な first adapter である。

### 2. `claude-history`

`commands/sessions.md`
と既存の session-manager utilities が既に公開しているデータを正規化する:

- session id / alias
- branch
- worktree
- project path
- recency / file size / item counts

これは ECC 2.0 向けの non-orchestrated baseline を提供する。

### 3. `codex-worktree`

同じ canonical shape を使用するが、利用可能な箇所では tmux assumptions の代わりに Codex-native execution metadata で裏付ける。

### 4. `opencode`

OpenCode session metadata が正規化に十分安定したら、同じ adapter boundary を使用する。

## Adapter Layer に含めるべきでないもの (What Should Stay Out Of The Adapter Layer)

adapter layer が所有すべきでないもの:

- merge sequencing の business logic
- operator UI layout
- pricing または monetization decisions
- install profile selection
- tmux lifecycle orchestration 自体

その役割はより狭い:

- session targets を検出する
- normalized snapshots を読み込む
- オプションで runtime events をストリームする
- オプションで safe actions を公開する

## 現在のファイルレイアウト (Current File Layout)

adapter layer は現在以下に存在する:

```text
scripts/lib/session-adapters/
  canonical-session.js
  dmux-tmux.js
  claude-history.js
  registry.js
scripts/session-inspect.js
tests/lib/session-adapters.test.js
tests/scripts/session-inspect.test.js
```

現在の orchestration snapshot parser は、唯一の product contract のまま残るのではなく、adapter implementation として消費されるようになった。

## 直近の次のステップ (Immediate Next Steps)

1. 抽象化を tmux plus Claude-history を超えて進めるため、3つ目の adapter（おそらく `codex-worktree`）を追加する。
2. UI 作業を開始する前に、canonical snapshots に別々の `state` と `health` fields が必要かどうかを決定する。
3. event streaming が v1 に属するか、snapshot layer が自分を証明するまで外に置くかを決定する。
4. orchestration internals を直接読むのではなく、adapter registry の上にのみ operator-facing panels を構築する。

## 未解決の質問 (Open Questions)

1. worker identity は worker slug、branch、stable UUID のどれをキーにすべきか?
2. canonical layer で別々の `state` と `health` fields が必要か?
3. event streaming は v1 の一部とすべきか、ECC 2.0 はまず snapshot-only で ship すべきか?
4. snapshots が local machine を離れる前に、どれだけの path information を redact すべきか?
5. adapter registry は長期的にこのリポジトリ内に留まるべきか、interface が安定したら eventual ECC 2.0 control-plane app に移動すべきか?

## 推奨 (Recommendation)

現在の tmux/worktree implementation を final product surface ではなく、adapter `0` として扱う。

ECC 2.0 への最短経路は:

1. 現在の orchestration substrate を保持する
2. canonical session adapter contract でラップする
3. non-tmux adapter を1つ追加する
4. その上で初めて operator panels の構築を開始する
