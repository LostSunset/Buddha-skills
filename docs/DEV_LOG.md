# 開發日誌 DEV_LOG

> 規則見 [`DEV_LOG_RULES.md`](./DEV_LOG_RULES.md)。新增迭代請 append 到檔案末尾，不要覆寫既有紀錄。

---

## [2026-04-15 12:00] 迭代 #1 — LostSunset + claude

### 📋 本次目標

- 建立 Buddha-skills 開發工作流基礎建設（Phase 1 Step 1–5）

### ✅ 完成項目

- 倉庫初始化、`.gitignore`、`README.md`、首次 push (commit: `11cf48c`)
- 治理文件：`CLAUDE.md`、`MEMORY.md`、`ROADMAP.md`、`docs/TEAM_ROLES.md`、`docs/DEV_LOG_RULES.md`、`docs/ARCHITECTURE.md` (commit: `304aa56`, PR #1)
- 建立 `dev-log` 與 `auto-dev-mode` 兩個 skill (commit: `0978572`, PR #2)
- 加入三個 upstream submodule 追蹤 main，`.gitattributes`、`upstream/README.md`、`upstream-guard.yml` (commit: `7127b35`, PR #3)
- `update-upstream.yml`（每日 UTC 18:00）與 `claude-review.yml`（@claude 觸發，OAuth token 認證）(commit: `cdd4981`, PR #4)
- 設定 repo secrets：`CLAUDE_CODE_OAUTH_TOKEN`、`PAT_TOKEN`
- 手動觸發 `Daily Upstream Sync` workflow 驗證成功（run 24435644751）
- 打 `v0.1.0` tag 並建立 GitHub Release

### 🐛 發現問題

- 無

### 📊 測試結果

- N/A（本階段無程式碼測試；workflow 驗證：1 次成功）

### 🔄 下次目標

- 等上游專案首次更新時觀察 `@claude` 實際於 PR 上的回應與 push 行為
- 視需要在 Phase 2 新增本專案自建 skills

### 💡 技術筆記

- `PAT_TOKEN` 直接重用本機 `gh auth token`（scope: `repo`, `workflow`, `read:org`, `gist`），無需另建 fine-grained PAT
- `CLAUDE_CODE_OAUTH_TOKEN` 以 `claude setup-token` 取得，用量計入 Claude 訂閱，無額外 API 費用
- 三個 submodule 皆追蹤各自 `main` 分支並設 `update = merge`
- 所有文件與 commit message 採正體中文

### 🔗 關聯

- PR: #1, #2, #3, #4
- Release: [v0.1.0](https://github.com/LostSunset/Buddha-skills/releases/tag/v0.1.0)
- Step: ROADMAP Step 1–5（全部 ✅）

---

## [2026-04-15 12:15] 迭代 #2 — LostSunset + claude

### 📋 本次目標

- 從 `D:\35_UAV` 無人機簡報專案抽取兩項實戰技術，新增為本 repo 的 skills
- 對外貢獻場景：PPTX 視覺 QA 與批次圖片下載（Wikimedia 429 繞路）

### ✅ 完成項目

- 新增 `.claude/skills/pptx-visual-qa/SKILL.md`——LibreOffice + PyMuPDF 把 PPTX 轉 PNG 做版面檢查的完整流程 (WIP)
- 新增 `.claude/skills/resilient-image-download/SKILL.md`——批次圖片下載的 User-Agent、Wikimedia `/thumb/` 繞路、Retry-After、小圖校驗技巧 (WIP)

### 🐛 發現問題

- 無

### 📊 測試結果

- N/A（skill 為文件，無自動化測試；實戰驗證來自來源專案 29 頁簡報與 60+ 張圖片下載）

### 🔄 下次目標

- 若有其他專案也用到類似流程，可於下次迭代補 `video-visual-qa`（影片逐幀截圖檢查）對應 skill

### 💡 技術筆記

- Wikimedia `/thumb/` 對自動化有嚴格限速但原檔路徑沒有；`Special:FilePath/<FILE>` 是最穩備援
- python-pptx 的 CJK 字寬估算 ≈ 字級 pt × 1/72 吋，建議比估算值再降 10-15% 作安全邊
- PDF→PNG 日常 QA 建議 110 dpi，最終精修 150 dpi

### 🔗 關聯

- PR: #5
- Issue: —
- Step: Phase 1 額外新增（Step 6、Step 7 — 實戰 skill 抽取）

---

## [2026-04-15 12:45] 迭代 #3 — LostSunset + claude

### 📋 本次目標

- 完成 Phase 2 三個自建 skill：`auto-iterate`、`upstream-search`、`skill-scaffold`

### ✅ 完成項目

- `.claude/skills/auto-iterate/SKILL.md`：包裝 CLAUDE-CODE-自動開發指令.md 模式 B 一鍵執行（B.1–B.6 + 六項必做更新）(WIP)
- `.claude/skills/upstream-search/SKILL.md`：跨三個 submodule 搜尋範本、只讀規則 (WIP)
- `.claude/skills/skill-scaffold/SKILL.md`：依 upstream/anthropic-skills 慣例生成新 skill 骨架、description 觸發力檢查 (WIP)
- README 加上 CI / Release / Submodule badges (commit: `4073c86`, PR #6)

### 🐛 發現問題

- PR #5 與 Phase 1 的 DEV_LOG 迭代 #1 衝突 → 本地 rebase + 重新編號為迭代 #2 後合併

### 📊 測試結果

- N/A（skill 為文件）

### 🔄 下次目標

- 實戰使用 Phase 2 三個 skill；若有觸發不順或描述不精準，依用例回補

### 💡 技術筆記

- `auto-iterate` 與 `auto-dev-mode` 分工：前者僅處理模式 B（有 ROADMAP 持續迭代），後者做整體模式偵測
- `skill-scaffold` 會呼叫 `upstream-search` 作為參考資源鏈

### 🔗 關聯

- PR: #6（badges），Phase 2 skills PR：本次迭代
- Step: ROADMAP Step 8、9、10

---

## [2026-04-15 13:15] 迭代 #4 — LostSunset + claude

### 📋 本次目標

- 補齊 CLAUDE-CODE-自動開發指令.md 四模式的獨立 skill 包裝（模式 B 已有 `auto-iterate`）

### ✅ 完成項目

- `.claude/skills/new-project-init/SKILL.md`：模式 A Phase 0 九小節（分析、設計、ROADMAP 80 步、治理、Issue、團隊、日誌、首次 commit + v0.1.0 tag）
- `.claude/skills/maintenance-patrol/SKILL.md`：模式 C C.1 巡檢 + C.2 持續改善 + C.3 版本管理
- `.claude/skills/project-takeover/SKILL.md`：模式 D D0 考古 + D1 治理 + D2 切入模式 B（R1–R4 接手版 ROADMAP）

### 🐛 發現問題

- 無

### 📊 測試結果

- N/A

### 🔄 下次目標

- 四模式 skill 齊備，`auto-dev-mode` 可轉為純 router（偵測後 delegate 到對應 skill）

### 💡 技術筆記

- 四個模式 skill 的 description 皆含前置條件明文，降低誤觸機率
- `project-takeover` 強調「前 2–3 小時絕不寫業務 code」，符合原文保守策略

### 🔗 關聯

- PR: #8
- Step: ROADMAP Step 11、12、13

---

## [2026-04-15 13:45] 迭代 #5 — LostSunset + claude

### 📋 本次目標

- 執行六項必做更新收尾本 session：README 進度、CLAUDE/MEMORY 補記、新增 SESSIONS.md

### ✅ 完成項目

- README 新增「進度」與「skills 一覽」小節
- CLAUDE.md 補「模式 skill 對應」小節
- MEMORY.md 新增 4 條技術決策 + 迭代 #2/#3/#4 表格更新
- 新增 `docs/SESSIONS.md`，記錄 Session #1（本次從 0 到 v0.3.0 的完整摘要）

### 🐛 發現問題

- 無

### 📊 測試結果

- N/A

### 🔄 下次目標

- 等 02:00 cron 首次實戰觸發觀察 `@claude` 行為
- 視實戰反饋微調 skills description

### 💡 技術筆記

- SESSIONS.md 以「一次對話」為單位做高層摘要，DEV_LOG 仍保留每次迭代細節 → 雙層記憶系統
- 六項必做更新本次全部自動化完成（除 Release 已於 v0.3.0 階段完成）

### 🔗 關聯

- SESSION: #1
- Release: v0.3.0
- Step: 無新增 ROADMAP Step（純收尾）

---

## [2026-04-15 14:15] 迭代 #6 — LostSunset + claude

### 📋 本次目標

- 讓 Buddha-skills 可透過 `/plugin install` 安裝（類比 `anthropics/skills`）

### ✅ 完成項目

- 新增 `.claude-plugin/marketplace.json`：marketplace `buddha-skills` + 單一 plugin `buddha-all`（含全部 10 個 skill）(commit: `b2ed13e`, PR #9)
- README 新增「以 Claude Code Plugin 安裝」小節，同步引導三個 upstream marketplace 各自的安裝指令 (commit: `87468b1`)
- JSON 以 node 驗證語法、skills 陣列長度 = 10
- ROADMAP Step 14 標 ✅（Phase 4 完成）
- CLAUDE.md 新增「Marketplace 發佈」小節（skill 增減時須同步 marketplace.json）
- MEMORY.md 補 3 條新決策 + 迭代 #5/#6 表格

### 🐛 發現問題

- 無

### 📊 測試結果

- JSON schema 驗證通過（`node -e`）
- 實機 `/plugin install` 驗收待使用者下次開新 session 時執行

### 🔄 下次目標

- 實機 `/plugin install buddha-all@buddha-skills` 驗證 10 個 skill 能被 Claude Code 辨識
- 若有遺漏或路徑錯誤 → 修正 marketplace.json

### 💡 技術筆記

- 上游三個 repo 都有自己的 marketplace（anthropic-skills = `anthropic-agent-skills`、omc = `omc`），改讓使用者各自 `/plugin marketplace add`，避免重新發佈他人作品的授權風險
- karpathy-skills 只有 1 個 skill 且無 marketplace，改採「手動複製」建議
- 本 repo 的 `upstream/` 只作開發參考；執行期請直接接上游 marketplace

### 🔗 關聯

- PR: #9
- Step: ROADMAP Step 14
- Release: v0.4.0（本次）

---

## [2026-04-15 19:55] 迭代 #7 — LostSunset + claude

### 📋 本次目標

- 從 `D:\37_PIC`（LostSunset/PIC，全端 WebGUI 專案 M2–M6）抽取可重用 skill 回寫 Buddha-skills。

### ✅ 完成項目

- 新增 `.claude/skills/milestone-pipeline/SKILL.md`：大專案切 N 個里程碑、每個走完整 spec→plan→execute→PR→merge 流水線 (commit: `(pending)`)
- 新增 `.claude/skills/subagent-dispatch-matrix/SKILL.md`：subagent dispatch 規模、審查強度、model 選擇決策矩陣，實戰證實可省 60% dispatch 次數 (commit: `(pending)`)
- 新增 `.claude/skills/claude-gh-app-setup/SKILL.md`：Max 訂閱 OAuth 完整設定、`claude[bot]` 身份關鍵（workflow 不傳 `github_token`）、branch protection (commit: `(pending)`)
- `.claude-plugin/marketplace.json`：skills 陣列 10 → 13，version 1.0.0 → 1.1.0
- `README.md`：skills 數量 10 → 13，skills 一覽新增「實戰（PIC 全端 WebGUI）」類別
- `MEMORY.md`：補 4 條技術決策（milestone 切分、subagent 調度、GitHub App bot 身份、branch protection 腳本化）+ 迭代 #7 表格
- `ROADMAP.md`：新增 Phase 5 Step 15

### 🐛 發現問題

- 無

### 📊 測試結果

- marketplace.json 結構保持一致（單一 plugin + skills 陣列）
- 3 個 SKILL.md frontmatter description 都含 3–5 個觸發句（與既有 skill 一致）

### 🔄 下次目標

- 實機 `/plugin install buddha-all@buddha-skills` 驗證 13 個 skill 都能被 Claude Code 辨識
- 若後續 PIC 專案有 Phase 2（VTK 3D / remote runner）實戰，再回頭抽第 4 個 skill

### 💡 技術筆記

- `claude-gh-app-setup` 的關鍵陷阱（workflow 別傳 `github_token`）是在 PIC 專案 merge 後 smoke test 才發現的 — 其他專案都是 `claude[bot]`，只有我們是 `github-actions[bot]`，拿掉那行就對了
- `subagent-dispatch-matrix` 的 60% 節省是實測數字：PIC 的 12-task M5 實際只派了 12–15 次而非 36 次，其中機械 task（scaffold、form sections、viz components）全部合併 + 只跑 spec review
- `milestone-pipeline` 是 PIC 整個開發流程的骨架：M2 core → M3 API → M4 frontend skeleton → M5 frontend features → M6 polish，每個都是一條 feature 分支、一個 PR、一次 merge

### 🔗 關聯

- PR: (pending)
- Step: ROADMAP Step 15
- 來源專案: LostSunset/PIC（M2–M6 全端 WebGUI，main HEAD 截至今日 c8ab4c4）

---

## [2026-04-16 01:18 (UTC)] 迭代 #8 — auto/upstream-sync

### 📋 本次目標

- 每日上游同步 2026-04-15：更新 `upstream/andrej-karpathy-skills` submodule

### ✅ 完成項目

- upstream/andrej-karpathy-skills: `fb8fdb0..c9a44ae` (3 commits) (commit: `d2d9730`)

### 🐛 發現問題

- 無

### 📊 測試結果

- N/A（上游變更僅為 README 更新，無功能影響）

### 🔄 下次目標

- 持續觀察每日上游同步；若有 skill 相關變更再評估是否需要同步調整

### 💡 技術筆記

- 本次上游 3 commits 皆為 andrej-karpathy-skills README 文字更新（加入專案連結與社群連結）
- 對本專案 `.claude/skills/**`、`docs/`、`CLAUDE.md`/`MEMORY.md`/`ROADMAP.md` 均無影響

### 🔗 關聯

- PR: #11
- Issue: —
- Step: 上游同步（無對應 ROADMAP Step）

---

## [2026-04-16 09:30] 迭代 #9 — LostSunset + claude

### 📋 本次目標

- 修正 @claude 不會自動合併 PR 的問題
- 將「讓 @claude 自動合併 PR」技巧做成 skill

### ✅ 完成項目

- 確認 @claude workflow 修正成功（PR #11，run 24486791825，2m5s success）
- `update-upstream.yml` PR body 新增明確合併指令：`gh pr merge chore/upstream-update-$DATE --merge --repo LostSunset/Buddha-skills` (commit: `635d6fc`)
- 新增 `.claude/skills/claude-pr-automerge/SKILL.md`：完整說明為何 @claude 不合併（沒有指令而非沒有權限）、`issue_comment` vs `pull_request` checkout 差異、分支名寫死技巧
- `marketplace.json` 擴充為 14 個 skill，version 1.1.0 → 1.2.0
- `README.md` 新增「CI / GitHub Actions」類別、skill 數更新為 14
- `MEMORY.md` 補 2 條技術決策 + 迭代 #9 表格

### 🐛 發現問題

- `gh pr merge --merge`（無參數）在 `issue_comment` 觸發時找不到 PR（checkout 是 main 分支，無 PR 上下文）

### 📊 測試結果

- workflow 修正後 @claude 回應正常（PR #11 驗證）
- 合併指令改用分支名後可在任何觸發類型執行

### 🔄 下次目標

- 等下次每日 upstream sync 觸發，驗證 @claude 能自動合併 PR

### 💡 技術筆記

- @claude 說「合併超出我的操作權限」是錯誤的判斷——`GITHUB_TOKEN` 在 Actions 環境永遠存在，`gh pr merge` 一直可執行，只是沒有收到明確指令
- `issue_comment` 觸發時 `actions/checkout` 預設 checkout `main`，`pull_request` 觸發才會有 `refs/pull/N/merge`
- PR body 裡的 merge 指令要放在 fenced code block（\`\`\`bash）裡，@claude 才能識別為可執行指令

### 🔗 關聯

- PR: (pending)
- Step: 無新 ROADMAP Step（skill 新增 + CI 修正）
- Commits: `635d6fc`（CI 修正）+ skill/marketplace/README/MEMORY

---

## [2026-04-16 10:00] 迭代 #10 — LostSunset + claude

### 📋 本次目標

- 新增 `Galaxy-Dawn/claude-scholar` 為第 4 個 upstream submodule（學術研究向）

### ✅ 完成項目

- `git submodule add -b main https://github.com/Galaxy-Dawn/claude-scholar upstream/claude-scholar`
- `.gitmodules` 新增 `claude-scholar` 項目 + `update = merge`
- `upstream/README.md` submodule 表格加入第 4 列
- `README.md` 多處更新：badge（`upstream-submodules-3` → `-4`）、目錄說明、`/plugin marketplace add Galaxy-Dawn/claude-scholar` 安裝指引、「搭配三個」→「搭配四個」
- `.claude/skills/upstream-search/SKILL.md` 新增 claude-scholar 定位（學術研究向 / 50 skills + 15 agents），description 加入 scholar / 學術 / 論文 / research 觸發字
- `MEMORY.md` 補 1 條技術決策 + 迭代 #8/#9/#10 表格校正

### 🐛 發現問題

- 原 MEMORY.md 表格只到 #7，補入 `@claude` 自動同步的 #8 與前次 skill 新增的 #9

### 📊 測試結果

- `upstream/claude-scholar/` 已成功 clone（內含 50 skills、15 agents、multilingual docs）
- `.gitmodules` 結構與既有 3 個 submodule 一致
- `update-upstream.yml` 自動處理 `upstream/*/` 全部 submodule，無需額外修改

### 🔄 下次目標

- 下次每日 sync 觸發時觀察 claude-scholar 是否正常進入 PR 摘要
- 視需要從 claude-scholar 抽取學術研究相關 skill（如 `citation-verification`、`ml-paper-writing`）為 Phase 6 來源

### 💡 技術筆記

- `claude-scholar` 本身有 `.claude-plugin/marketplace.json`，使用者可直接 `/plugin marketplace add Galaxy-Dawn/claude-scholar` 安裝，不需經由本 repo
- 四個 upstream 的定位已明確分工：anthropic（官方規範）/ karpathy（教學）/ omc（社群整合）/ scholar（學術研究）

### 🔗 關聯

- PR: (pending)
- Step: 無新 ROADMAP Step（submodule 擴充）

---

## [2026-04-24 10:00] 迭代 #11 — LostSunset + claude

### 📋 本次目標

- 新增 `google/skills` 為第 5 個 upstream submodule（Google Cloud 官方技能庫）
- 順帶修正 2026-04-16 排程失敗的 `submodules: recursive` 問題

### ✅ 完成項目

- **分支保護啟用**：`gh api -X PUT repos/LostSunset/Buddha-skills/branches/main/protection` 套用 1 approval + admin bypass（`enforce_admins: false`），防禁 force push / deletion
- **修正 CI 排程失敗**：`update-upstream.yml` / `claude-review.yml` 將 `submodules: recursive` 改為 `submodules: true`
  - 失敗原因：`upstream/claude-scholar` 上游在 `plugins/marketplaces/ai-research-skills` 等 4 處含 gitlink（mode 160000）但 `.gitmodules` 檔缺失，recursive 會 `fatal: No url found for submodule path`
  - 本 repo 只需 5 個頂層鏡像，不需進入 nested 階層
- **新增 google/skills submodule**（pin `6f0b877`）：
  - `.gitmodules` 加入第 5 個區塊，`update = merge` 與其他 4 個一致
  - `upstream/google-skills/` 含 13 個 Cloud 技能（BigQuery、Cloud Run、GKE、Firebase、AlloyDB、Cloud SQL、WAF security/reliability/cost 等）
- **同步更新文件與 skill**：
  - `README.md`：badge `upstream-submodules-5`、上游清單加入 Google Cloud 定位、`npx skills add google/skills` 指引
  - `upstream/README.md`：文案「三個」→「五個」（原文案其實落後，一次補齊）、表格第 5 列
  - `.claude/skills/upstream-search/SKILL.md`：description 加入 google / GCP / BigQuery / Cloud Run / GKE 觸發字，來源表新增第 5 列
  - `MEMORY.md`：補 2026-04-17（CI submodules 修正）與 2026-04-24（google/skills 新增）兩條決策
  - `ROADMAP.md`：新增 Phase 6 Step 16

### 🐛 發現問題

- `upstream/claude-scholar` 上游本身有「孤兒 nested submodule」狀態（gitlink 但 `.gitmodules` 為空），屬於上游 bug；本 repo 僅能繞過 recursive 避開

### 📊 測試結果

- `git submodule update --init`（非 recursive）正常完成，5 個 submodule 全 checkout
- `git status` 乾淨，僅保留預期 diff
- `upstream-guard.yml` 邏輯未更動，仍會阻擋 `upstream/**` 內容變更

### 🔄 下次目標

- 下一輪 UTC 18:00 排程觀察 `update-upstream.yml` 是否順利處理 5 個 submodule
- 若 google/skills 更新頻繁，評估是否需要從中抽取 GCP 相關 skill 落到本 repo `.claude/skills/`

### 💡 技術筆記

- 5 個 upstream 的定位分工：anthropic（官方規範）/ karpathy（教學）/ omc（社群整合）/ scholar（學術研究）/ google（雲端產品）
- branch protection `enforce_admins: false` 讓 admin（本人）可直接 push 到 main，但 workflow 用的 PAT 會走 PR 流程
- `submodules: true` 對多數 repo 已足夠；`recursive` 需確認每個子模組的 `.gitmodules` 完整性

### 🔗 關聯

- PR: (pending — `feature/upstream-google-skills`)
- Step: ROADMAP Step 16（Phase 6）

---

## [2026-04-24 12:00] 迭代 #12 — LostSunset + claude

### 📋 本次目標

- 新增 `cloudflare/skills` 為第 6 個 upstream submodule（Cloudflare Workers / Agents SDK / Durable Objects / MCP 官方技能庫）

### ✅ 完成項目

- PR #12 合併（google/skills + CI 修正 submodules: recursive→true）
- **新增 cloudflare/skills submodule**（pin `0438a07`）：
  - `.gitmodules` 加入第 6 個區塊，`update = merge` 與其他 5 個一致
  - `upstream/cloudflare-skills/` 含 8 個 SKILL.md（cloudflare、agents-sdk、durable-objects、sandbox-sdk、wrangler、web-perf、workers-best-practices、cloudflare-email-service）+ `/cloudflare:build-agent` `/cloudflare:build-mcp` 斜線指令 + 5 個遠端 MCP server 設定
- **同步更新文件與 skill**：
  - `README.md`：badge `upstream-submodules-6`、上游清單加入 Cloudflare 定位、`/plugin marketplace add cloudflare/skills` 指引
  - `upstream/README.md`：文案「五個」→「六個」、表格第 6 列
  - `.claude/skills/upstream-search/SKILL.md`：description 加入 cloudflare / Workers / Agents SDK / Durable Objects / Wrangler / MCP 觸發字，來源表新增第 6 列
  - `MEMORY.md`：補 2026-04-24 cloudflare 決策
  - `ROADMAP.md`：新增 Phase 6 Step 17

### 🐛 發現問題

- 無

### 📊 測試結果

- `git submodule status` 顯示 6 個 submodule 正確 checkout
- `git status` 乾淨
- cloudflare/skills 含自己的 `.claude-plugin/marketplace.json`，使用者可直接 `/plugin marketplace add cloudflare/skills` 安裝

### 🔄 下次目標

- 下一輪 UTC 18:00 排程觀察 `update-upstream.yml` 是否順利納入 6 個 submodule
- 視需要從 cloudflare-skills 抽取邊緣運算相關 skill 範例（如 `build-mcp` 斜線指令的設計）

### 💡 技術筆記

- 6 個 upstream 的定位分工：anthropic（官方規範）/ karpathy（教學）/ omc（社群整合）/ scholar（學術研究）/ google（主流雲端）/ cloudflare（邊緣運算）
- cloudflare/skills 是第一個帶 **slash commands + MCP servers** 設定進來的 upstream，可作為「完整 Claude Code plugin」結構的參考範本

### 🔗 關聯

- PR: (pending — `feature/upstream-cloudflare-skills`)
- Step: ROADMAP Step 17（Phase 6）
