# 開發 Session 紀錄

> 以「一次 Claude Code 對話」為單位的高層摘要。細項迭代見 [`DEV_LOG.md`](./DEV_LOG.md)。

---

## SESSION #1：2026-04-15（台北）— LostSunset + claude

**主題**：Buddha-skills 從零到 v0.3.0

### 範圍

- 倉庫初始化、治理結構、開發日誌系統
- upstream 子模組（三個）+ 每日同步 workflow + `@claude` 審查
- 自建 skills 10 個（dev-log、auto-dev-mode + 四模式 + 工具 + 實戰）

### 產出

| 類別 | 數量 |
|------|------|
| PR 合併 | 8 個（#1–#8，含一個由外部 session 推來的 PR #5） |
| GitHub Release | 3 個（v0.1.0、v0.2.0、v0.3.0） |
| ROADMAP Step | 13 個全部 ✅ |
| 自建 skill | 10 個 |
| Workflow | 3 個（update-upstream、claude-review、upstream-guard） |
| Repo secrets | 2 個（`CLAUDE_CODE_OAUTH_TOKEN`、`PAT_TOKEN`） |
| 包含的 DEV_LOG 迭代 | #1、#2、#3、#4 |

### 關鍵決策

- 上游 submodule 追蹤 `main`，每日 UTC 18:00（台北 02:00）自動同步
- Claude Code Action 採 OAuth token 認證（計入訂閱，無 API 費用）
- `PAT_TOKEN` 重用本機 `gh auth token`，免建 fine-grained PAT
- 四模式各有獨立 skill，`auto-dev-mode` 轉為 router

### 未決 / 後續觀察

- 等明日 02:00 首次 cron 實際觸發，觀察 `@claude` 真實 push 行為
- skills 在實戰中的 description 觸發力待微調
- Phase 4+ 視實際使用需求規劃

### 主要 commits

- `11cf48c` chore: repository initialization
- `304aa56` docs(step-2): governance structure
- `0978572` feat(step-3): dev-log and auto-dev-mode skills
- `7127b35` chore(step-4): upstream submodules and guard
- `cdd4981` ci(step-5): daily upstream sync and @claude review
- `4073c86` docs: README badges
- `67b3d15` feat: pptx-visual-qa and resilient-image-download（rebased PR #5）
- `16a8318` feat(step-8-10): Phase 2 skills
- `449f8c3` feat(step-11-13): Phase 3 四模式 skill 補齊

### 相關 Tags

- [v0.1.0](https://github.com/LostSunset/Buddha-skills/releases/tag/v0.1.0)
- [v0.2.0](https://github.com/LostSunset/Buddha-skills/releases/tag/v0.2.0)
- [v0.3.0](https://github.com/LostSunset/Buddha-skills/releases/tag/v0.3.0)
- [v0.4.0](https://github.com/LostSunset/Buddha-skills/releases/tag/v0.4.0)

### 延伸（Session #1 續）：marketplace 安裝支援

**新增**：`.claude-plugin/marketplace.json` — marketplace `buddha-skills` + plugin `buddha-all`（10 skills）

**使用方式**：
```bash
/plugin marketplace add LostSunset/Buddha-skills
/plugin install buddha-all@buddha-skills
```

對應 DEV_LOG 迭代 #6、PR #9、Release v0.4.0。
