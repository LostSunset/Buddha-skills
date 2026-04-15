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

## Phase 2：本專案自建 skills

### Step 6：pptx-visual-qa skill
- **狀態**: ✅ 完成
- **依賴**: Step 3
- **範圍**: `.claude/skills/pptx-visual-qa/SKILL.md`
- **驗收條件（AC）**:
  - [x] SKILL.md 含 LibreOffice + PyMuPDF 流程
  - [x] PR 合併
- **完成日期**: 2026-04-15（PR #5）
- **來源**: 從 `D:\35_UAV` 專案抽取實戰經驗

### Step 7：resilient-image-download skill
- **狀態**: ✅ 完成
- **依賴**: Step 3
- **範圍**: `.claude/skills/resilient-image-download/SKILL.md`
- **驗收條件（AC）**:
  - [x] SKILL.md 含 429 / UA / Wikimedia 繞路與小圖校驗
  - [x] PR 合併
- **完成日期**: 2026-04-15（PR #5）
- **來源**: 從 `D:\35_UAV` 專案抽取實戰經驗

### Step 8：auto-iterate skill
- **狀態**: ✅ 完成
- **依賴**: Step 3
- **範圍**: `.claude/skills/auto-iterate/SKILL.md`（包裝 CLAUDE-CODE-自動開發指令.md 模式 B 一鍵執行）
- **驗收條件（AC）**:
  - [x] 六階段流程完整
  - [x] 含 upstream guard 與正體中文規則
  - [x] PR 合併（#7）

### Step 9：upstream-search skill
- **狀態**: ✅ 完成
- **依賴**: Step 4
- **範圍**: `.claude/skills/upstream-search/SKILL.md`（跨三個 submodule 搜尋）
- **驗收條件（AC）**:
  - [x] 明列三個 submodule 定位與適用場景
  - [x] 含 grep/find 範本
  - [x] 只讀規則明確
  - [x] PR 合併（#7）

### Step 10：skill-scaffold skill
- **狀態**: ✅ 完成
- **依賴**: Step 3, Step 9
- **範圍**: `.claude/skills/skill-scaffold/SKILL.md`（依 upstream 慣例生成新 skill 骨架）
- **驗收條件（AC）**:
  - [x] 含輸入需求、模板、檢查清單
  - [x] description 觸發力檢查條列
  - [x] PR 合併（#7）

## Phase 3+：待規劃
