# Buddha-skills

[![Daily Upstream Sync](https://github.com/LostSunset/Buddha-skills/actions/workflows/update-upstream.yml/badge.svg)](https://github.com/LostSunset/Buddha-skills/actions/workflows/update-upstream.yml)
[![Upstream Guard](https://github.com/LostSunset/Buddha-skills/actions/workflows/upstream-guard.yml/badge.svg)](https://github.com/LostSunset/Buddha-skills/actions/workflows/upstream-guard.yml)
[![Claude PR Review](https://github.com/LostSunset/Buddha-skills/actions/workflows/claude-review.yml/badge.svg)](https://github.com/LostSunset/Buddha-skills/actions/workflows/claude-review.yml)
[![Release](https://img.shields.io/github/v/release/LostSunset/Buddha-skills?include_prereleases&sort=semver)](https://github.com/LostSunset/Buddha-skills/releases)
[![Submodules](https://img.shields.io/badge/upstream--submodules-4-blue)](./upstream/README.md)
[![Docs](https://img.shields.io/badge/docs-正體中文-ea5545)](./CLAUDE.md)

> LostSunset 專屬的 Claude Code skills 庫。

## 內容

- `.claude/skills/` — 本專案自建 skill（`dev-log`、`auto-dev-mode`）
- `upstream/` — 上游參考子模組（**唯讀鏡像**），每日自動同步
  - `anthropic-skills/` — [anthropics/skills](https://github.com/anthropics/skills)
  - `andrej-karpathy-skills/` — [forrestchang/andrej-karpathy-skills](https://github.com/forrestchang/andrej-karpathy-skills)
  - `oh-my-claudecode/` — [yeachan-heo/oh-my-claudecode](https://github.com/yeachan-heo/oh-my-claudecode)
  - `claude-scholar/` — [Galaxy-Dawn/claude-scholar](https://github.com/Galaxy-Dawn/claude-scholar)（學術研究向：50 skills + 15 agents）
- `docs/` — 開發日誌、規則、架構文件
- `CLAUDE-CODE-自動開發指令.md` — Claude Code 四種模式（A/B/C/D）自動開發流程

## 以 Claude Code Plugin 安裝（推薦）

### 本 repo 的 14 個自建 skill

```bash
/plugin marketplace add LostSunset/Buddha-skills
/plugin install buddha-all@buddha-skills
```

### 搭配四個上游 skill 庫（各自獨立安裝，保持原作者維護）

本 repo 的 `upstream/` 是開發時的參考鏡像，**執行期**請直接安裝上游各自的 marketplace，取得最新版與原作者的維護：

```bash
# anthropic-skills（官方 skills 庫）
/plugin marketplace add anthropics/skills
/plugin install document-skills@anthropic-agent-skills
/plugin install example-skills@anthropic-agent-skills
/plugin install claude-api@anthropic-agent-skills

# oh-my-claudecode（32 skills + 28 agents 的多代理編排）
/plugin marketplace add yeachan-heo/oh-my-claudecode
/plugin install oh-my-claudecode@omc

# claude-scholar（學術研究向，50 skills + 15 agents）
/plugin marketplace add Galaxy-Dawn/claude-scholar
/plugin install claude-scholar@claude-scholar

# andrej-karpathy-skills 目前只有 1 個 skill、無 marketplace；可手動複製單檔使用
```

不需要 clone 本 repo 也不需管 submodule。

## Clone（開發或修改本 repo 時才需要）

```bash
git clone --recurse-submodules https://github.com/LostSunset/Buddha-skills.git
```

若已 clone：

```bash
git submodule update --init --recursive
```

## 開發規範

見 [CLAUDE.md](./CLAUDE.md) 與 [docs/DEV_LOG_RULES.md](./docs/DEV_LOG_RULES.md)。

## 進度

- **目前版本**：[v0.5.0](https://github.com/LostSunset/Buddha-skills/releases/tag/v0.5.0)
- **已完成 Phase**：Phase 1（工作流基礎）、Phase 2（自建 skills）、Phase 3（四模式補齊）、Phase 4（marketplace）、Phase 5（PIC 實戰抽取）
- **ROADMAP Step**：15 / 15 ✅
- **自建 skills**：14 個

### skills 一覽

| 類別 | Skills |
|------|--------|
| 基礎 | `dev-log`、`auto-dev-mode`（模式 router） |
| 四模式（對應 CLAUDE-CODE-自動開發指令.md） | `new-project-init`（A）、`auto-iterate`（B）、`maintenance-patrol`（C）、`project-takeover`（D） |
| 工具 | `upstream-search`、`skill-scaffold` |
| 實戰（PPTX / 圖片） | `pptx-visual-qa`、`resilient-image-download` |
| 實戰（PIC 全端 WebGUI） | `milestone-pipeline`、`subagent-dispatch-matrix`、`claude-gh-app-setup` |
| CI / GitHub Actions | `claude-pr-automerge` |

完整 ROADMAP 見 [ROADMAP.md](./ROADMAP.md)。
