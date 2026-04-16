# 專案記憶檔

## 技術決策記錄

| 日期 | 決策 | 理由 | 影響範圍 |
|------|------|------|----------|
| 2026-04-15 | 上游以 git submodule 追蹤 main | 保持與原作者完全一致、可追溯版本 | `upstream/` |
| 2026-04-15 | 每日 UTC 18:00 自動同步 | 台北凌晨避開工作時段 | CI |
| 2026-04-15 | 文件一律正體中文 | 使用者語言偏好 | 所有文件 |
| 2026-04-15 | @claude 可直接 push 到 PR 分支 | 降低手動介入，加快同步 | workflow 權限 |
| 2026-04-15 | Claude Code Action 採用 OAuth token | 使用現有 Claude 訂閱，無需付費 API key | CI 認證 |
| 2026-04-15 | `auto-dev-mode` 定位為 router，四模式各有獨立 skill | 觸發更精準、各模式邏輯隔離、便於個別優化 | `.claude/skills/` |
| 2026-04-15 | 每個 Phase 結束打 minor version tag | 清楚的版本里程碑、便於 rollback | release 流程 |
| 2026-04-15 | `PAT_TOKEN` 重用 `gh auth token` | 免建 fine-grained PAT，同等 scope（`repo`+`workflow`） | CI |
| 2026-04-15 | marketplace 只放本 repo 自建 skill（10 個） | 避免重新發佈他人作品的授權風險，讓使用者直接接上游取得最新版 | `.claude-plugin/marketplace.json` |
| 2026-04-15 | 單一 plugin `buddha-all` 打包 10 個 skill | 降低安裝複雜度，單一指令一次取得 | marketplace 結構 |
| 2026-04-15 | marketplace version 獨立於 git tag | marketplace 穩定度與 repo 開發節奏解耦 | 版本管理 |
| 2026-04-15 | 中大型專案切多個里程碑，每個走完整 spec→plan→exec→PR | 每個里程碑都可獨立交付測試，合併 conflict 最小 | `milestone-pipeline` skill |
| 2026-04-15 | Subagent dispatch 不死守 1 task = 3 次 review | 機械 task 合併派、只跑 spec review 可省 60% 時間 | `subagent-dispatch-matrix` skill |
| 2026-04-15 | Claude Code Action workflow 不傳 `github_token` | action 會自動用 Claude GitHub App installation token，身份變 `claude[bot]` 而非 `github-actions[bot]` | `claude-gh-app-setup` skill / `.github/workflows/claude.yml` |
| 2026-04-15 | Branch protection 用 `gh api -X PUT` 腳本化 | 免進 UI、可重現、可 diff | 倉庫設定流程 |
| 2026-04-16 | @claude 不會自動合併 PR 是因為沒收到指令，不是沒有技術權限 | `GITHUB_TOKEN` 在 Actions 環境永遠存在，`gh pr merge` 一直可執行 | `claude-pr-automerge` skill / PR body 設計 |
| 2026-04-16 | `gh pr merge --merge`（無參數）在 `issue_comment` 觸發時失敗，要用分支名 | `issue_comment` checkout 是 main，無法偵測 PR；`pull_request` 才有 `GITHUB_REF` 含 PR 號 | `update-upstream.yml` PR body、`claude-pr-automerge` skill |
| 2026-04-16 | 新增 `claude-scholar` 為第 4 個 upstream submodule | 學術研究工作流（50 skills + 15 agents）補齊研究向缺口，與既有三個定位互補 | `upstream/`、`upstream-search` skill |

## 已知問題與解法

| 問題 | 解法 | 發現日期 |
|------|------|----------|
| `GITHUB_TOKEN` 建立的 PR 不觸發其他 workflow | 改用 fine-grained `PAT_TOKEN` | 2026-04-15 |

## 重要上下文

- 遠端倉庫：https://github.com/LostSunset/Buddha-skills.git
- Claude GitHub App 已安裝於本 repo
- 需於 repo secrets 設定 `CLAUDE_CODE_OAUTH_TOKEN`（由 `claude setup-token` 取得）與 `PAT_TOKEN`

## 每次迭代摘要

| 迭代 | 日期 | 完成步驟 | 重點變更 |
|------|------|----------|----------|
| #1 | 2026-04-15 | Task 1–5 | Phase 1 工作流基礎建設（倉庫、治理、skill、submodule、CI） |
| #2 | 2026-04-15 | Step 6–7 | 從 `D:\35_UAV` 抽取 pptx-visual-qa 與 resilient-image-download |
| #3 | 2026-04-15 | Step 8–10 | Phase 2 三個工具 skill（auto-iterate、upstream-search、skill-scaffold） |
| #4 | 2026-04-15 | Step 11–13 | Phase 3 四模式 skill 補齊（A/C/D + B router 化） |
| #5 | 2026-04-15 | 收尾 | 六項必做更新 + 新增 SESSIONS.md 雙層紀錄 |
| #6 | 2026-04-15 | Step 14 | Phase 4 marketplace 支援（`/plugin install buddha-all@buddha-skills`） |
| #7 | 2026-04-15 | Step 15 | Phase 5 — 從 `D:\37_PIC` 全端 WebGUI 專案抽取 3 個 skill（milestone-pipeline、subagent-dispatch-matrix、claude-gh-app-setup），marketplace 擴充為 13 個 skill |
| #8 | 2026-04-16 | — | 每日上游同步 2026-04-15（andrej-karpathy-skills README 更新，@claude bot 自動審查） |
| #9 | 2026-04-16 | — | 修正 `claude-review.yml`（`claude_args` + 移除 `github_token`），驗證 @claude 回應正常；新增 `claude-pr-automerge` skill + `update-upstream.yml` PR body 加入合併指令 |
| #10 | 2026-04-16 | — | 新增 `claude-scholar` 為第 4 個 upstream submodule（50 skills + 15 agents，學術研究向），更新 `upstream-search` skill / README / MEMORY 同步 |
