# Suite integration / Suite 合并说明

This fork retains the upstream `dsh-memory-evolve` package identity and integrates the additions from `Archer76/dsh-memory-evolve-suite` without reverting newer upstream fixes. 本 fork 保留上游包身份，移入 suite 增量，同时保留上游的新修复。

## Sources and history / 来源与历史

- Upstream: [csyangwen/dsh-memory-evolve](https://github.com/csyangwen/dsh-memory-evolve), commit `e6f6b84c56f0fc18ba4a6a1a5959fbf86ea9ec8b`.
- Suite: [Archer76/dsh-memory-evolve-suite](https://github.com/Archer76/dsh-memory-evolve-suite), commit `1746629e9e6aae383c391ded45b7124404e4230e` (0.3.2).
- Fork: [Archer76/dsh-memory-evolve](https://github.com/Archer76/dsh-memory-evolve).

The two source repositories have independent Git roots. The integration commit retains both histories: the upstream tree is the base, and selected suite additions are applied before completing the merge. 这不是将 suite 整目录覆盖到上游；双父合并保留两个仓库历史，对同名实现逐项比较后采用较新的上游实现。

The upstream MIT license is retained. Suite's original license is the same MIT text with the same copyright notice. The source links and exact suite commit above identify the imported work.

## Imported additions / 迁入内容

| Content | Decision |
|---|---|
| `dsh-memory-compression` | Imported: large-track compression, snapshots, verification and staging. 提供大轨压缩流程。 |
| `dsh-memory-curation` | Imported: routine cleanup and archival. 提供日常整理及归档流程。 |
| `dsh-injection-track-triage` | Imported: historical prompt interpolation diagnosis and session inspection. 提供插值故障取证流程。 |
| `tools/` | Imported unchanged: 14 JavaScript CLI scripts, CommonJS package metadata and the edits example. |
| Bundled-skill and tool tests | Imported and adapted for eight skills, upstream CRLF handling and isolated source fixtures. |
| `.gitattributes` | Skill documents retain LF in distributed checkouts. |
| Fork metadata and installation docs | `repository` and quick-start commands point to this fork. The plugin id remains `dsh-memory-evolve`. |

Upstream's four CLI skills and `memory-consolidate` remain, for a total of **eight built-in skills**. New skill provider fields use `dsh-memory-evolve`; their `x-version` is 2 so an existing version-1 copy can receive the fork-specific updates. Users who customized these skills should back up their copies before enabling the usual version-gated synchronization.

The imported skills explain that upstream now sanitizes double opening braces in memory snapshots. Their historical diagnosis steps still apply to older versions or unsanitized paths; raw-file checks do not establish failure in the current final model input. The PowerShell recursive-search example was corrected.

## Preserved upstream fixes / 保留的上游修复

Suite's four `source.kind` changes are already covered by upstream `f48ab4d`, which centralizes the producer-owned source kind and has regression tests. The older suite versions of these files are not copied over.

The integration also retains upstream's:

- DSH icon and session-navigation compatibility changes (`e6f6b84`).
- Settings describe/read and commit-event compatibility (`db83f84`).
- `Session.ownEvents()` support, snapshot sanitization and literal-variable handling.
- Display-form memory matching, archive fixes and final-response ordering.
- UTF-8 streaming decode and Git subprocess timeout cleanup.
- Skill synchronization independent of the COI toggle, whole-directory copy, default-directory handling and CRLF version parsing.

The client bundle is the upstream artifact, unchanged by this integration. New skills and tools do not require a client rebuild. The package version stays at upstream 0.1.0; this integration does not publish a release or import suite release tags into the fork remote.

## Tool usage / 脚本用法

Use the package's `tools` directory directly; every script gives usage when called without arguments. Scripts operate on explicitly supplied track files or temporary staging paths. Review the skill workflows and backup requirements before running write operations on real memory.

| Scripts | Purpose |
|---|---|
| `track-index.js`, `track-summaries.js` | Track inventory and summaries / 条目索引与摘要 |
| `check-summaries.js`, `check-inject.js` | Summary format and raw prompt rendering checks / 摘要与原始注入检查 |
| `verify-merge.js`, `verify-hard.js` | Coverage/token checks and hard-fact checks / 覆盖与事实校验 |
| `assemble.js`, `assemble-and-swap.js` | Staging and explicit write/archive operations / 装配、落盘及归档 |
| `unexpire.js`, `curate.js` | Outdated-fact replacement and routine curation / 过期更正与整理 |
| `ztail.js`, `zscan.js`, `zdump.js`, `msgsrc.js` | Session-frame inspection and message-source inspection / 会话取证 |

`check-inject.js` may need `DSH_SYSTEM_PROMPT_MODULE` to locate a compatible installed renderer. Zstd tools require a Node runtime exposing the corresponding zlib API. Session file names and generations must match the actual DSH installation.

## Install / 安装

```sh
git clone https://github.com/Archer76/dsh-memory-evolve.git
# Use the absolute path to that clone:
dsh plugin --profile web add <absolute-clone-path>
```

Keeping the clone's `.git` directory allows the existing updater to inspect its `origin`. Marketplace or Git-dependency installs may strip Git history. This integration creates no release tags, so a merged commit is not a new tagged updater release.

This fork uses the same plugin id, tool names and storage conventions as upstream. Install it as the upstream replacement in a profile, not as a second copy of the original plugin in that same profile. This integration does not migrate existing memories or install the fork into a running profile.

## Validation and compatibility / 验证与兼容性

Run the imported and upstream skill regressions:

```sh
node --test tests/bundled-skills.test.js tests/tools.test.js tests/skills-sync-default-dir.test.js tests/skills-sync-decoupled.test.js tests/skill-memory-consolidate.test.js tests/injection-source-kind.test.js
node --test --test-name-pattern="skills sync|issue #58|DSH 0.2.0" tests/coi.test.js tests/plugin.test.js
```

The package test glob no longer uses shell single quotes, which previously produced a zero-test success on Windows. Set a temporary test home when running assembly tests so automatic skill synchronization cannot write to a real user skill library.

Validation on Windows with bundled Node v24.21.0: the six-file skill/tool/source-kind suite passed **42/42**, the focused sync/assembly suite passed **4/4**, and the retained settings-event regression passed **1/1**. The additional Windows `cmd` glob smoke executed the named `BUILTIN_SKILLS` test successfully; its 74 reported successes include empty filtered-file entries and are not counted as 74 behavior tests. All 16 imported tool-directory files (14 scripts plus metadata/example) were byte-identical to suite. `git diff --check` passed, and the upstream client bundle and inspected compatibility fixes were unchanged.

Windows 验证：47 项针对性回归全部通过，另验证默认命令 shell 能通过修正后的 glob 实际运行技能清单测试；测试主目录隔离在工作区中，没有修改真实用户技能库。没有运行全套测试或在现有 DSH profile 安装插件。

This merge does not certify complete DSH v0.2.1-alpha.2 compatibility: upstream compatibility changes are preserved, but full Loader/browser/model end-to-end verification and the earlier lifecycle/Windows canvas findings are outside this integration's implementation scope.
