# CLAUDE.md — Buddha-skills 開發規範

> 本檔案為本專案的 Claude Code 指令。任何 clone 此專案的 Claude Code（包含 `@claude` GitHub App）都必須遵循以下規則。

## 專案性質

- LostSunset 專屬的 Claude Code skills 庫
- `upstream/` 下的子模組為**唯讀鏡像**，禁止任何修改
- 文件、commit message、日誌一律使用**正體中文**

## 目錄

- `.claude/skills/` — 本專案 skills
- `upstream/` — 上游參考（唯讀）
- `docs/` — 文件與開發日誌

## 開發流程

依 `CLAUDE-CODE-自動開發指令.md` 的四種模式（A 新專案 / B 開發中 / C 維護 / D 接手）自動偵測並執行。

### 模式 skill 對應

- `auto-dev-mode` — 模式偵測 router（先呼叫它判斷 A/B/C/D）
- `new-project-init` — 模式 A（Phase 0 九小節初始化）
- `auto-iterate` — 模式 B（持續迭代 B.1–B.6）
- `maintenance-patrol` — 模式 C（巡檢 + 持續改善 + 版本管理）
- `project-takeover` — 模式 D（考古 + 治理 + 切入模式 B）

## 開發日誌

**每次迭代完成前必須更新 `docs/DEV_LOG.md`**，規則見 [`docs/DEV_LOG_RULES.md`](./docs/DEV_LOG_RULES.md)。
使用 `dev-log` skill 自動套用模板。

## 六項必做更新

每次合併 PR 前：
1. Commit & 推送
2. 更新版本與 README
3. 發佈 Release（版本異動時）
4. 更新 CLAUDE.md 與 MEMORY.md
5. 更新 ROADMAP.md
6. 更新 `docs/DEV_LOG.md`

## Commit 規範

```
<type>(scope): <subject>

<body>

Refs: #issue
```

type：`feat` / `fix` / `refactor` / `test` / `docs` / `chore` / `style` / `perf` / `ci`

## 分支策略

- `main` — 穩定版
- `feature/<name>` — 功能開發
- `fix/<name>` — Bug 修復
- `chore/upstream-update-YYYY-MM-DD` — 每日上游同步（自動）

## upstream 子模組

- 追蹤各自的 `main` 分支
- 每日 UTC 18:00 自動同步（workflow `update-upstream.yml`）
- **嚴禁**手動修改 `upstream/<name>/**` 內容，由 `upstream-guard.yml` 於 PR 阻擋

## @claude 權限

- GitHub Claude App 允許直接 push commit 到 PR 分支
- 僅在 `upstream-sync` label 的 PR 或被 @mention 時作動
- 認證採用 `CLAUDE_CODE_OAUTH_TOKEN`（綁定訂閱），非 API key
