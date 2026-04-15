# 專案記憶檔

## 技術決策記錄

| 日期 | 決策 | 理由 | 影響範圍 |
|------|------|------|----------|
| 2026-04-15 | 上游以 git submodule 追蹤 main | 保持與原作者完全一致、可追溯版本 | `upstream/` |
| 2026-04-15 | 每日 UTC 18:00 自動同步 | 台北凌晨避開工作時段 | CI |
| 2026-04-15 | 文件一律正體中文 | 使用者語言偏好 | 所有文件 |
| 2026-04-15 | @claude 可直接 push 到 PR 分支 | 降低手動介入，加快同步 | workflow 權限 |
| 2026-04-15 | Claude Code Action 採用 OAuth token | 使用現有 Claude 訂閱，無需付費 API key | CI 認證 |

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
| #1 | 2026-04-15 | Task 1 | 倉庫初始化、README、.gitignore、首次 push |
