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
