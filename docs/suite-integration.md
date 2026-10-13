# Suite integration / Suite 合并说明

**重复功能以 Archer76/dsh-memory-evolve-suite 的技能与流程为准；仅接入 suite 没有的上游能力。** Overlapping features use the suite implementation and workflows. Unique upstream capabilities and necessary compatibility/data-integrity fixes are retained.

## Sources and history / 来源与历史

- Upstream: [csyangwen/dsh-memory-evolve](https://github.com/csyangwen/dsh-memory-evolve), commit `e6f6b84c56f0fc18ba4a6a1a5959fbf86ea9ec8b`.
- Suite: [Archer76/dsh-memory-evolve-suite](https://github.com/Archer76/dsh-memory-evolve-suite), commit `1746629e9e6aae383c391ded45b7124404e4230e` (0.3.2).
- Fork: [Archer76/dsh-memory-evolve](https://github.com/Archer76/dsh-memory-evolve).

The first integration commit `0c1f8d5` retains both independent Git histories. This follow-up applies the owner's suite-first deduplication policy without rewriting that history. 上游更新日志保留为来源历史，其中记载的技能不代表本 fork 当前仍分发该技能。

The original MIT license and copyright notice are retained. The source links and commits above identify the imported work.

## Feature decisions / 功能取舍

| Content | Decision |
|---|---|
| Routine curation / 日常整理 | Use suite's `dsh-memory-curation`: snapshots, curation scripts, archival and rendering checks. |
| Large-track compression / 大轨压缩 | Use suite's `dsh-memory-compression`: chunking, token/fact checks, staging and concurrent-addition preservation. |
| Injection diagnosis / 注入排障 | Use suite's `dsh-injection-track-triage`. |
| Upstream `memory-consolidate` skill | Removed from both the package and built-in registry; its overlapping workflow is not injected. |
| Unique candidate scanner / 独有候选预扫 | Extracted into `tools/scan-memory.mjs` and invoked from suite's curation skill. No second curation skill entry. |
| Original suite tools | All 14 JavaScript scripts retained; with the upstream scanner there are 15 helper scripts. |
| Compatibility and integrity fixes | Retain upstream fixes to the existing single plugin implementation, without introducing duplicate functional entry points. |

The seven built-in skills are `kimi-cli-calling`, `codex-cli-calling`, `grok-cli-calling`, `hermes-cli-calling`, `dsh-memory-compression`, `dsh-memory-curation`, and `dsh-injection-track-triage`.

The suite curation skill advances to `x-version: 3` to synchronize the scanner integration instructions. The compression and diagnosis skills remain at fork version 2. All provider fields use the fork's `dsh-memory-evolve` package identity.

## Candidate scanner / 候选扫描

```sh
node <package-tools>/scan-memory.mjs --dir <memoryDir> --out <workspace>/memory-scan-report.json --threshold 0.42
```

The scanner retains upstream's TF-IDF similarity, quoted-reference/supersede clues, conflict-polarity hints and candidate clustering. It reads global/user/project tracks; daily logs and todos are excluded, and archives only provide context. It does not modify memory files. `--out` writes the JSON report; without `--out` it prints the report to stdout.

候选簇只是检索辅助，不是自动合并裁决。核对原文、日期、保护项和所属项目后，沿用 suite 的快照、整理、压缩、归档与校验流程。上游重复的工具专用写入流程没有移入本 fork。

Source: upstream `skills/memory-consolidate/scripts/scan_memory.mjs` at `e6f6b84`. The algorithm is retained; the path, usage/log labels and workflow comments are adapted to the suite toolset.

## Preserved fixes / 保留修复

- Producer-owned message source kind, replacing rejected legacy injection messages.
- DSH icon/session navigation and settings describe/event compatibility.
- `Session.ownEvents()` support, snapshot sanitization and literal-variable handling.
- Display-form memory matching, archive fixes and final-response ordering.
- UTF-8 streaming decode and Git subprocess timeout settlement/cleanup.
- Skill synchronization independent of COI, whole-directory copy, default-directory handling and CRLF version parsing.

The client artifact stays unchanged. The package version remains upstream 0.1.0; these changes create no release or release tags. Raw-file prompt checks in the suite skills still apply to older or unsanitized paths, but do not establish failure in a current sanitized model input.

## Install and upgrade / 安装与升级

```sh
git clone https://github.com/Archer76/dsh-memory-evolve.git
dsh plugin --profile web add <absolute-clone-path>
```

The plugin id remains `dsh-memory-evolve`; use the fork as the upstream replacement in a profile. Keeping `.git` enables the existing updater to inspect `origin`; Git-dependency/marketplace installs may strip history. No new updater release tag is created here.

If a previous eight-skill version was installed, the skill synchronizer does not delete existing user-library folders when a skill leaves the package. Disable the old `memory-consolidate` copy in the skill manager to prevent duplicate instructions; user-edited skill files are not automatically removed. 本次只调整仓库，没有安装或修改运行中的 profile 或真实用户技能库。

## Validation / 验证

```sh
node --test tests/bundled-skills.test.js tests/tools.test.js tests/skills-sync-default-dir.test.js tests/skills-sync-decoupled.test.js tests/memory-scan.test.js tests/injection-source-kind.test.js
node --test --test-name-pattern="skills sync|issue #58|locale watcher" tests/coi.test.js tests/plugin.test.js
```

The regressions check the seven-entry registry against the actual package directories, default assembly, version protection, retained suite tools, scanner candidate logic, and the real relocated scanner CLI's read-only behavior. Use a temporary test home so assembly tests cannot synchronize skills into a real user library. The Windows test glob remains fixed.

Windows / Node v24.21.0 validation: **43/43** skill/tool/scanner/source-kind regressions and **5/5** focused sync/assembly/settings regressions passed (**48 total**). The package directories match the seven-entry registry, the real relocated scanner CLI produces a report without changing memory text, and real default assembly excludes the redundant upstream skill. All 16 original suite tool-directory files retain their content; only Windows/Git CRLF normalization differs from the source repository. The inspected client artifact and compatibility/integrity implementations are unchanged. `git diff --check` passed.

没有运行全套测试，也没有修改真实用户技能库或运行中的 profile。Full Loader/browser/model end-to-end compatibility with DSH v0.2.1-alpha.2 is not established; the previously identified lifecycle and Windows canvas issues remain outside this deduplication change.
