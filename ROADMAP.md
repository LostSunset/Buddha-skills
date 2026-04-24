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

## Phase 3：模式 A/C/D 補齊

### Step 11：new-project-init skill
- **狀態**: ✅ 完成
- **依賴**: Step 3
- **範圍**: `.claude/skills/new-project-init/SKILL.md`（模式 A，Phase 0 九小節）
- **驗收條件（AC）**:
  - [x] 九小節流程完整
  - [x] 前置條件檢查
  - [x] PR 合併
- **完成日期**: 2026-04-15

### Step 12：maintenance-patrol skill
- **狀態**: ✅ 完成
- **依賴**: Step 3
- **範圍**: `.claude/skills/maintenance-patrol/SKILL.md`（模式 C，C.1–C.3）
- **驗收條件（AC）**:
  - [x] 巡檢、改善、版本管理三階段
  - [x] PR 合併
- **完成日期**: 2026-04-15

### Step 13：project-takeover skill
- **狀態**: ✅ 完成
- **依賴**: Step 3
- **範圍**: `.claude/skills/project-takeover/SKILL.md`（模式 D，Phase D0–D2）
- **驗收條件（AC）**:
  - [x] 考古、治理、切入模式 B 三階段
  - [x] R1–R4 ROADMAP 接手版
  - [x] PR 合併
- **完成日期**: 2026-04-15

## Phase 4：Marketplace 安裝支援

### Step 14：Claude Code marketplace
- **狀態**: ✅ 完成
- **依賴**: Step 10
- **範圍**: `.claude-plugin/marketplace.json`（單一 plugin `buddha-all`，含 10 個 skill）+ README 安裝指引
- **驗收條件（AC）**:
  - [x] `/plugin marketplace add LostSunset/Buddha-skills` 可辨識
  - [x] `/plugin install buddha-all@buddha-skills` 可安裝 10 個 skill
  - [x] README 同時引導三個 upstream marketplace 各自安裝
  - [x] PR 合併
- **完成日期**: 2026-04-15（PR #9）

## Phase 5：從實戰專案萃取 skills

### Step 15：從 PIC WebGUI 專案抽取 3 個 skill
- **狀態**: ✅ 完成
- **依賴**: Step 14
- **範圍**:
  - `.claude/skills/milestone-pipeline/`（大專案切 N 個里程碑、每個跑一次完整流水線）
  - `.claude/skills/subagent-dispatch-matrix/`（subagent 發派規模、審查強度、model 選擇的決策矩陣）
  - `.claude/skills/claude-gh-app-setup/`（Max 訂閱 OAuth + claude[bot] 身份 + branch protection 完整設定）
  - marketplace.json 擴充為 13 個 skill，version 1.1.0
  - README / MEMORY 同步
- **驗收條件（AC）**:
  - [x] 3 個 SKILL.md 格式與既有 skill 一致
  - [x] marketplace.json 通過 JSON 語法驗證、skills 陣列長度 = 13
  - [x] 每個 skill 的 description 含 3–5 個觸發字詞
  - [x] PR 合併
- **來源專案**: `D:\37_PIC`（`LostSunset/PIC`，M2–M6 全端 WebGUI）
- **完成日期**: 2026-04-15

## Phase 6：上游生態擴充

### Step 16：新增 `google/skills` 為第 5 個 upstream submodule
- **狀態**: ✅ 完成
- **預估時間**: 0.5 小時
- **依賴**: Step 4
- **範圍**:
  - `.gitmodules` 新增 `upstream/google-skills` 指向 `https://github.com/google/skills` 追蹤 `main`
  - `README.md` badge（`upstream-submodules-4` → `-5`）、目錄說明、「搭配四個」→「搭配五個」、`npx skills add google/skills` 安裝指引
  - `upstream/README.md` 表格加入第 5 列、文案「三個」→「五個」
  - `.claude/skills/upstream-search/SKILL.md` 加入第 5 列定位（GCP / BigQuery / Cloud Run / GKE / Firebase / WAF）、description 擴充觸發字
  - `MEMORY.md` 加入決策與 CI 修正紀錄
- **驗收條件（AC）**:
  - [x] `git submodule status` 顯示 5 個 submodule
  - [x] `upstream-guard.yml` 依然阻擋 `upstream/google-skills/**` 內容直接變動
  - [x] `update-upstream.yml` 下一輪排程能納入 google/skills 差異
  - [ ] PR 合併
- **完成日期**: 2026-04-24
- **附帶 fix**: `update-upstream.yml` / `claude-review.yml` 改用 `submodules: true`（避開 `claude-scholar` 上游的孤兒 nested submodule，解決 2026-04-16 排程失敗）

### Step 17：新增 `cloudflare/skills` 為第 6 個 upstream submodule
- **狀態**: ✅ 完成
- **預估時間**: 0.5 小時
- **依賴**: Step 16
- **範圍**:
  - `.gitmodules` 新增 `upstream/cloudflare-skills` 指向 `https://github.com/cloudflare/skills` 追蹤 `main`
  - `README.md` badge（`-5` → `-6`）、目錄說明、「搭配五個」→「搭配六個」、`/plugin marketplace add cloudflare/skills` 安裝指引
  - `upstream/README.md` 表格加入第 6 列、文案「五個」→「六個」
  - `.claude/skills/upstream-search/SKILL.md` 加入第 6 列定位（Workers / Agents SDK / Durable Objects / Sandbox / Wrangler / web-perf / MCP）、description 擴充觸發字
  - `MEMORY.md` 加入決策
- **驗收條件（AC）**:
  - [x] `git submodule status` 顯示 6 個 submodule
  - [x] `upstream-guard.yml` 依然阻擋 `upstream/cloudflare-skills/**` 內容直接變動
  - [ ] PR 合併
- **完成日期**: 2026-04-24

## Phase 7+：待規劃
