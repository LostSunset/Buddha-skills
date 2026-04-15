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
