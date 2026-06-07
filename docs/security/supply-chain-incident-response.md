# サプライチェーンインシデント対応 (Supply-Chain Incident Response)

この playbook は、npm、GitHub Actions、cross-ecosystem package-registry incidents 向けの ECC operator runbook である。意図的に保守的である: registry signatures、provenance、trusted publishing は有用な signal だが、workflow が意図した code path を実行したことを証明しない。

## 現在の外部トリガー (Current External Trigger)

2026-05-15 時点で、active incident class は 2026年5月の TanStack npm supply-chain compromise と broader Mini Shai-Hulud campaign である。ECC は related npm/PyPI waves に対して同じ IOC sweep を維持する。これらの incidents は package install/publish paths、AI developer-tool configs、developer credentials を標的とする:

- TanStack は 42 の `@tanstack/*` packages にわたる 84 malicious versions を報告した。2026-05-11 の 19:20 から 19:26 UTC の間に公開された。
- GitHub advisory `GHSA-g7cv-rxg3-hmpx` / `CVE-2026-45321` は、cloud credentials、GitHub tokens、npm credentials、Vault tokens、Kubernetes tokens、SSH private keys を収集する install-time malware を記述する。
- StepSecurity、Socket、Aikido、Wiz からの follow-on reporting は、同じ campaign が Mistral AI、UiPath、OpenSearch、Guardrails AI、Squawk、その他の npm/PyPI packages に関連する packages へ拡大していると記述する。
- Socket の 2026-05-14 `node-ipc` report は、`node-ipc` versions `9.1.6`、`9.2.3`、`12.0.1` に影響する別の active npm compromise を記述する。destructive または unauthorized file-writing behavior を持っていた historical malicious `node-ipc` versions も ECC によってブロックされる。
- live IOC set には、Claude Code `.claude/settings.json`、VS Code `.vscode/tasks.json`、Zed `.zed/tasks.json`、OS-level `gh-token-monitor` LaunchAgent/systemd services を通じた persistence が含まれる。一部の variants は `~/.config/gh-token-monitor/token` と dead-man-switch token description `IfYouRevokeThisTokenItWillWipeTheComputerOfTheOwner`、`.github/workflows/codeql_analysis.yml` のような malicious workflow files、`transformers.pyz` / `pgmonitor.py` のような Python runtime payloads を追加する。stolen GitHub token を rotate する前に、これらの persistence hooks を除去する。
- scanner は late-reporting markers も監視する: `router_init.js` SHA-256 prefix/suffix `ab4fcada...8601266c`、`tanstack_runner.js` SHA-256 prefix/suffix `2ec78d55...6be27fc96`、`opensearch_init.js`、`vite_setup.mjs`、campaign salt `svksjrhjkcejg`、Session protocol strings、`claude@users.noreply.github.com` dead-drop commits、`dependabout/` branch names、`OhNoWhatsGoingOnWithGitHub`。
- `node-ipc` sweep は `node-ipc.cjs` payload hash `96097e06...d9034144`、malicious `9.1.6`、`9.2.3`、`12.0.1` artifacts の tarball hashes、`sh.azurestaticprovider.net`、`bt.node.js`、`37.16.75.69`、artifacts に存在する場合の DNS exfil labels `xh` / `xd` / `xf`、`__ntw`、`__ntRun`、`/nt-` temp archives、`uname.txt`、`envs.txt`、`fixtures/_paths.txt` のような archive entries を監視する。
- attack chain は `pull_request_target`、fork/base trust boundary にわたる GitHub Actions cache poisoning、GitHub Actions runner からの OIDC token extraction を組み合わせた。
- npm trusted publishing/provenance は package が bound CI identity から来たことを確認できる。CI cache、lifecycle scripts、publish path が安全だったことをそれ自体では証明できない。

主要な参照 (Primary references):

- <https://tanstack.com/blog/npm-supply-chain-compromise-postmortem>
- <https://github.com/advisories/GHSA-g7cv-rxg3-hmpx>
- <https://tanstack.com/blog/incident-followup>
- <https://www.wiz.io/blog/mini-shai-hulud-strikes-again-tanstack-more-npm-packages-compromised>
- <https://socket.dev/blog/node-ipc-package-compromised>
- <https://docs.npmjs.com/trusted-publishers/>
- <https://www.cisa.gov/news-events/alerts/2025/09/23/widespread-supply-chain-compromise-impacting-npm-ecosystem>

## ECC 露出チェック (ECC Exposure Check)

release candidate の前、広範な dependency bump の後、package-registry incident の後にこれを実行する。

```bash
npm run security:ioc-scan
node scripts/ci/scan-supply-chain-iocs.js --home
npm ci --ignore-scripts
npm audit signatures
npm audit --audit-level=high
node scripts/ci/supply-chain-advisory-sources.js --json
node scripts/ci/validate-workflow-security.js
node tests/scripts/npm-publish-surface.test.js
node tests/run-all.js
```

search hit が documentation examples にのみ現れる場合、release evidence に記録するが、docs-only reference のために credentials を rotate しない。

## 持続的な監視ワークフロー (Durable Watch Workflow)

ECC は `.github/workflows/supply-chain-watch.yml` を6時間ごとと manual dispatch で実行する。workflow は read-only で、checkout credential persistence を無効化し、`npm ci --ignore-scripts` でインストールし、npm registry signatures を検証し、IOC scanner fixtures を実行し、`scripts/ci/supply-chain-advisory-sources.js --refresh --json` を実行し、`supply-chain-ioc-report.json` と `supply-chain-advisory-sources.json` を出力し、GitHub Actions hardening rules を再検証する。

scheduled watch が失敗した場合、operator が failure が newly reported advisory、stale scanner fixture、registry-signature issue、workflow hardening regression のいずれかを確認するまで release blocker として扱う。scanner に新しい indicators が必要な場合、`scripts/ci/scan-supply-chain-iocs.js` を更新し、`tests/ci/scan-supply-chain-iocs.test.js` に fixture coverage を追加し、この runbook を更新し、最新の JSON artifact を release evidence に添付する。

advisory-source artifact は ITO-57 status payload である。trusted source registry、live URL refresh warnings、Linear-ready summary を記録する。IOC coverage を変更する前に `npm run security:advisory-sources -- --json` で source coverage を refresh し、各 significant merge batch の後に artifact を次の Linear project status update に添付する。

## 即時対応 (Immediate Response)

ECC または maintainer machine が known-bad package version をインストールした場合:

1. host が publish または deploy しないよう停止する。
2. cleanup の前に evidence を保全する:
   - パッケージマネージャーのコマンド履歴;
   - `package-lock.json`、`pnpm-lock.yaml`、または `yarn.lock`;
   - CI run の URL と runner logs;
   - npm package versions と tarball integrity hashes;
   - 利用可能な場合の outbound network logs。
3. lifecycle scripts が実行された可能性がある場合、install host を compromised として扱う。
4. token revocation の前に persistence hooks を除去する:
   - `~/.claude/settings.json` `SessionStart` hooks と隣接する `router_runtime.js` / `setup.mjs` payload files;
   - `.vscode/tasks.json` folder-open tasks と隣接する payload files;
   - `~/Library/LaunchAgents/com.user.gh-token-monitor.plist`;
   - `~/.config/systemd/user/gh-token-monitor.service`;
   - `~/.config/systemd/user/pgsql-monitor.service`;
   - `~/.config/gh-token-monitor/token`;
   - `~/.local/bin/gh-token-monitor.sh`;
   - `~/.local/bin/pgmonitor.py`;
   - `/tmp/transformers.pyz`、`/tmp/pgmonitor.py`、および macOS 上の `/private/tmp/` equivalents。
5. process が到達可能なすべての credential を rotate する:
   - npm automation tokens と maintainer tokens;
   - GitHub PATs、fine-grained tokens、deploy keys、および Actions secrets;
   - cloud credentials、Vault tokens、Kubernetes service-account tokens、SSH
     keys、および local `.npmrc` tokens;
   - environment variables または user-scope config で利用可能な MCP、plugin、harness credentials。
6. 影響を受けた repositories の GitHub Actions dependency caches を purge する。
7. まず lifecycle scripts を無効化した clean environment から再インストールする:
   `npm ci --ignore-scripts`、`pnpm install --ignore-scripts`、
   `yarn install --mode=skip-build`、または `bun install --ignore-scripts`。
8. dependency tree と package versions が known-clean releases に pin された後にのみ lifecycle scripts を再有効化する。

## GitHub Actions ルール (GitHub Actions Rules)

ECC は `scripts/ci/validate-workflow-security.js` を通じて以下のルールを強制する:

- privileged workflows は untrusted PR refs を checkout してはならない;
- すべての workflow dependency installs は lifecycle scripts を無効化しなければならない;
- workflows は active supply-chain hardening 中に shared GitHub Actions dependency caches を restore または save してはならない;
- `id-token: write` を持つ workflows は shared dependency caches を restore または save してはならない;
- `npm audit` を実行する workflows は `npm audit signatures` も実行しなければならない;
- `pull_request_target` workflows は shared dependency caches を restore または save してはならない。

違反は release blocker として扱う。

## 公開ルール (Publication Rules)

ECC を tag または publish する前に:

1. active advisory の packages への予期しない dependency がないことを確認する。
2. release commands には clean checkout または throwaway worktree を使用する。
3. PR/test caches を publish jobs と混在させない。
4. `id-token: write` を shared dependency caches を使用しない release workflows に限定する。
5. サポートされている箇所では trusted publishing/provenance を優先しつつ、local package-surface tests と registry-signature verification を引き続き要求する。
6. publication-readiness evidence document で npm dist-tag、GitHub release、Claude plugin、Codex plugin、OpenCode package state を確認する。

## エスカレーションのタイミング (When To Escalate)

以下の場合、release または merge の前に maintainer security review にエスカレーションする:

- dependency lockfile が active advisory に名指しされた package を参照している;
- `node scripts/ci/scan-supply-chain-iocs.js --home` が Claude Code、VS Code、Zed、または OS-level persistence indicators を検出した;
- workflow が `pull_request_target` と dependency installation、cache restore/save、PR-head checkout、または write permissions を組み合わせている;
- release workflow が `id-token: write` と shared cache usage を組み合わせている;
- publish workflow が documented reason なしに long-lived npm token を使用している;
- AgentShield、GitGuardian、Dependabot、npm audit、または registry-signature checks が不一致である。
