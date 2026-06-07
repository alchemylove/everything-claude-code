---
name: skill-health
description: チャートとアナリティクス付きのスキルポートフォリオヘルスダッシュボードを表示します。
command: true
---

# スキルヘルスダッシュボード (Skill Health Dashboard)

ポートフォリオ内のすべてのスキルについて、成功率スパークライン、失敗パターンのクラスタリング、保留中の修正案、バージョン履歴を含む包括的なヘルスダッシュボードを表示します。

## 実装 (Implementation)

ダッシュボードモードでスキルヘルス CLI を実行：

```bash
ECC_ROOT="${CLAUDE_PLUGIN_ROOT:-$(node -e "var r=(()=>{var e=process.env.CLAUDE_PLUGIN_ROOT;if(e&&e.trim())return e.trim();var p=require('path'),f=require('fs'),h=require('os').homedir(),d=p.join(h,'.claude'),q=p.join('scripts','lib','utils.js');if(f.existsSync(p.join(d,q)))return d;for(var s of [['ecc'],['ecc@ecc'],['marketplaces','ecc'],['everything-claude-code'],['everything-claude-code@everything-claude-code'],['marketplaces','everything-claude-code']]){var l=p.join(d,'plugins',...s);if(f.existsSync(p.join(l,q)))return l}try{for(var g of ['ecc','everything-claude-code']){var b=p.join(d,'plugins','cache',g);for(var o of f.readdirSync(b,{withFileTypes:true})){if(!o.isDirectory())continue;for(var v of f.readdirSync(p.join(b,o.name),{withFileTypes:true})){if(!v.isDirectory())continue;var c=p.join(b,o.name,v.name);if(f.existsSync(p.join(c,q)))return c}}}}catch(x){}return d})();console.log(r)")}"
node "$ECC_ROOT/scripts/skills-health.js" --dashboard
```

特定のパネルのみ：

```bash
ECC_ROOT="${CLAUDE_PLUGIN_ROOT:-$(node -e "var r=(()=>{var e=process.env.CLAUDE_PLUGIN_ROOT;if(e&&e.trim())return e.trim();var p=require('path'),f=require('fs'),h=require('os').homedir(),d=p.join(h,'.claude'),q=p.join('scripts','lib','utils.js');if(f.existsSync(p.join(d,q)))return d;for(var s of [['ecc'],['ecc@ecc'],['marketplaces','ecc'],['everything-claude-code'],['everything-claude-code@everything-claude-code'],['marketplaces','everything-claude-code']]){var l=p.join(d,'plugins',...s);if(f.existsSync(p.join(l,q)))return l}try{for(var g of ['ecc','everything-claude-code']){var b=p.join(d,'plugins','cache',g);for(var o of f.readdirSync(b,{withFileTypes:true})){if(!o.isDirectory())continue;for(var v of f.readdirSync(p.join(b,o.name),{withFileTypes:true})){if(!v.isDirectory())continue;var c=p.join(b,o.name,v.name);if(f.existsSync(p.join(c,q)))return c}}}}catch(x){}return d})();console.log(r)")}"
node "$ECC_ROOT/scripts/skills-health.js" --dashboard --panel failures
```

機械読み取り可能な出力：

```bash
ECC_ROOT="${CLAUDE_PLUGIN_ROOT:-$(node -e "var r=(()=>{var e=process.env.CLAUDE_PLUGIN_ROOT;if(e&&e.trim())return e.trim();var p=require('path'),f=require('fs'),h=require('os').homedir(),d=p.join(h,'.claude'),q=p.join('scripts','lib','utils.js');if(f.existsSync(p.join(d,q)))return d;for(var s of [['ecc'],['ecc@ecc'],['marketplaces','ecc'],['everything-claude-code'],['everything-claude-code@everything-claude-code'],['marketplaces','everything-claude-code']]){var l=p.join(d,'plugins',...s);if(f.existsSync(p.join(l,q)))return l}try{for(var g of ['ecc','everything-claude-code']){var b=p.join(d,'plugins','cache',g);for(var o of f.readdirSync(b,{withFileTypes:true})){if(!o.isDirectory())continue;for(var v of f.readdirSync(p.join(b,o.name),{withFileTypes:true})){if(!v.isDirectory())continue;var c=p.join(b,o.name,v.name);if(f.existsSync(p.join(c,q)))return c}}}}catch(x){}return d})();console.log(r)")}"
node "$ECC_ROOT/scripts/skills-health.js" --dashboard --json
```

## 使い方 (Usage)

```
/skill-health                    # フルダッシュボードビュー
/skill-health --panel failures   # 失敗クラスタリングパネルのみ
/skill-health --json             # 機械読み取り可能な JSON 出力
```

## 実行内容 (What to Do)

1. `--dashboard` フラグで skills-health.js スクリプトを実行
2. ユーザーに出力を表示
3. 低下しているスキルがある場合、ハイライトして `/evolve` の実行を提案
4. 保留中の修正案がある場合、レビューを提案

## パネル (Panels)

- **Success Rate (30d)** — スキルごとの日次成功率を表示するスパークラインチャート
- **Failure Patterns** — 水平バーチャート付きのクラスタ化された失敗理由
- **Pending Amendments** — レビュー待ちの修正提案
- **Version History** — スキルごとのバージョンスナップショットのタイムライン
