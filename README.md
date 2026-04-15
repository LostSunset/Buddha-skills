# Buddha-skills

[![Daily Upstream Sync](https://github.com/LostSunset/Buddha-skills/actions/workflows/update-upstream.yml/badge.svg)](https://github.com/LostSunset/Buddha-skills/actions/workflows/update-upstream.yml)
[![Upstream Guard](https://github.com/LostSunset/Buddha-skills/actions/workflows/upstream-guard.yml/badge.svg)](https://github.com/LostSunset/Buddha-skills/actions/workflows/upstream-guard.yml)
[![Claude PR Review](https://github.com/LostSunset/Buddha-skills/actions/workflows/claude-review.yml/badge.svg)](https://github.com/LostSunset/Buddha-skills/actions/workflows/claude-review.yml)
[![Release](https://img.shields.io/github/v/release/LostSunset/Buddha-skills?include_prereleases&sort=semver)](https://github.com/LostSunset/Buddha-skills/releases)
[![Submodules](https://img.shields.io/badge/upstream--submodules-3-blue)](./upstream/README.md)
[![Docs](https://img.shields.io/badge/docs-正體中文-ea5545)](./CLAUDE.md)

> LostSunset 專屬的 Claude Code skills 庫。

## 內容

- `.claude/skills/` — 本專案自建 skill（`dev-log`、`auto-dev-mode`）
- `upstream/` — 上游參考子模組（**唯讀鏡像**），每日自動同步
  - `anthropic-skills/` — [anthropics/skills](https://github.com/anthropics/skills)
  - `andrej-karpathy-skills/` — [forrestchang/andrej-karpathy-skills](https://github.com/forrestchang/andrej-karpathy-skills)
  - `oh-my-claudecode/` — [yeachan-heo/oh-my-claudecode](https://github.com/yeachan-heo/oh-my-claudecode)
- `docs/` — 開發日誌、規則、架構文件
- `CLAUDE-CODE-自動開發指令.md` — Claude Code 四種模式（A/B/C/D）自動開發流程

## Clone

```bash
git clone --recurse-submodules https://github.com/LostSunset/Buddha-skills.git
```

若已 clone：

```bash
git submodule update --init --recursive
```

## 開發規範

見 [CLAUDE.md](./CLAUDE.md) 與 [docs/DEV_LOG_RULES.md](./docs/DEV_LOG_RULES.md)。
