# 開發日誌規則 DEV_LOG_RULES

> 任何 clone `Buddha-skills` 的 Claude Code 都必須遵循本規則撰寫 `docs/DEV_LOG.md`。
> 配合 `.claude/skills/dev-log/` skill 自動套用。

## 1. 檔案位置

`docs/DEV_LOG.md`（append-only；絕不覆寫既有紀錄）

## 2. 時間戳

- 格式：`YYYY-MM-DD HH:mm`
- 時區：台北（UTC+8）
- 自動化迭代：使用 UTC 時間並在括號內註明 `(UTC)`

## 3. 迭代編號

單調遞增整數，從 `#1` 開始。檢查方法：讀取檔案中最後一個 `## [...] 迭代 #N`，新的一筆為 `#N+1`。

## 4. 必填七欄位

```markdown
## [YYYY-MM-DD HH:mm] 迭代 #N — <作者或 auto/upstream-sync>

### 📋 本次目標
- ...

### ✅ 完成項目
- <具體變更> (commit: `abc1234`)

### 🐛 發現問題
- ...（無則寫「無」）

### 📊 測試結果
- 通過: X / 失敗: Y / 覆蓋率: Z%（無測試則寫「N/A」）

### 🔄 下次目標
- ...

### 💡 技術筆記
- ...（可選）

### 🔗 關聯
- PR: #XX
- Issue: #XX
- Step: ROADMAP Step XX
```

## 5. 作者欄位

- 人工迭代：填 GitHub username（如 `LostSunset`）
- 自動同步：固定填 `auto/upstream-sync`
- Claude 協作：填 `LostSunset + claude`

## 6. Commit 關聯

`完成項目` 的每一條必須附上 7 碼 commit SHA（`abc1234`）。若尚未 commit 則以 `(WIP)` 標示。

## 7. 上游同步特殊格式

由 `update-upstream.yml` 產生的迭代，`完成項目` 必須列出每個更新的子模組與其新 commit 範圍：

```markdown
### ✅ 完成項目
- upstream/anthropic-skills: `oldsha..newsha` (5 commits)
- upstream/oh-my-claudecode: `oldsha..newsha` (2 commits)
```

## 8. 六項必做更新 checkpoint

迭代結束前確認：
- [ ] ① Commit & push
- [ ] ② 版本與 README 更新（若版本異動）
- [ ] ③ Release 發佈（若版本異動）
- [ ] ④ CLAUDE.md / MEMORY.md 更新（若規則或決策改變）
- [ ] ⑤ ROADMAP.md 更新（Step 狀態、完成日期）
- [ ] ⑥ DEV_LOG.md 更新（本條）

## 9. 範例

```markdown
## [2026-04-15 14:30] 迭代 #1 — LostSunset

### 📋 本次目標
- 初始化倉庫並建立治理文件

### ✅ 完成項目
- 建立 .gitignore 與 README (commit: `a1b2c3d`)
- 建立 CLAUDE.md / MEMORY.md / ROADMAP.md (commit: `e4f5g6h`)

### 🐛 發現問題
- 無

### 📊 測試結果
- N/A（本階段無測試）

### 🔄 下次目標
- 建立 dev-log skill 與 auto-dev-mode skill

### 🔗 關聯
- PR: #2
- Step: ROADMAP Step 2
```
