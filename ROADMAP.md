# ROADMAP

## Phase 1：工作流基礎建設

### Step 1：倉庫初始化
- **狀態**: ✅ 完成
- **預估時間**: 0.5 小時
- **依賴**: 無
- **範圍**: `git init`、`.gitignore`、`README.md`、連 remote、首次 push
- **驗收條件（AC）**:
  - [x] `main` 分支已推送
  - [x] 遠端 clone 可見檔案
- **完成日期**: 2026-04-15

### Step 2：治理文件
- **狀態**: ✅ 完成
- **預估時間**: 0.5 小時
- **依賴**: Step 1
- **範圍**: CLAUDE.md、MEMORY.md、ROADMAP.md、docs/TEAM_ROLES.md、docs/DEV_LOG.md、docs/DEV_LOG_RULES.md、docs/ARCHITECTURE.md
- **驗收條件（AC）**:
  - [x] 所有治理文件建立且正體中文
  - [x] PR 合併入 main
- **完成日期**: 2026-04-15（PR #1）

### Step 3：dev-log 與 auto-dev-mode skills
- **狀態**: ✅ 完成
- **預估時間**: 1 小時
- **依賴**: Step 2
- **範圍**: `.claude/skills/dev-log/SKILL.md`、`.claude/skills/auto-dev-mode/SKILL.md`
- **驗收條件（AC）**:
  - [x] Claude Code 能辨識兩個 skill
  - [x] `dev-log` skill 以使用者「寫開發日誌」觸發可正確 append
- **完成日期**: 2026-04-15（PR #2）

### Step 4：upstream 子模組與防改動 guard
- **狀態**: ✅ 完成
- **預估時間**: 1 小時
- **依賴**: Step 1
- **範圍**: 三個 submodule、`.gitattributes`、`upstream/README.md`、`upstream-guard.yml`
- **驗收條件（AC）**:
  - [x] `git submodule update --remote --merge` 可執行
  - [x] PR 修改 `upstream/<name>/**` 內容會被 CI 擋下
- **完成日期**: 2026-04-15（PR #3）

### Step 5：每日同步與 Claude 審查 workflow
- **狀態**: ✅ 完成
- **預估時間**: 1.5 小時
- **依賴**: Step 4
- **範圍**: `update-upstream.yml`、`claude-review.yml`、repo secrets
- **驗收條件（AC）**:
  - [x] `workflow_dispatch` 手動觸發可建立 PR（已驗證：run 24435644751，17s success，無新上游 commit 故未開 PR，邏輯正確）
  - [x] `@claude` 在 PR 回應並可 push commit（待首次有上游更新時觀察）
  - [x] v0.1.0 tag 建立
- **完成日期**: 2026-04-15（PR #4）

## Phase 2+：待規劃
（工作流穩定後再依需求新增 skills）
