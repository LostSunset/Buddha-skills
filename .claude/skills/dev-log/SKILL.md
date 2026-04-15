---
name: dev-log
description: 用於在 Buddha-skills 專案撰寫或更新開發日誌 docs/DEV_LOG.md。當使用者要求「寫開發日誌」「記錄本次迭代」「更新 DEV_LOG」「完成一個 step」「準備合併 PR」時使用，也會在 upstream 自動同步 PR 中被 @claude 呼叫。嚴格遵循 docs/DEV_LOG_RULES.md 的七欄位模板與迭代編號規則。
---

# dev-log

在 `Buddha-skills` 專案中 append 新一筆開發日誌到 `docs/DEV_LOG.md`。

## 步驟

1. **讀取規則**：讀 `docs/DEV_LOG_RULES.md` 確認最新欄位要求
2. **讀取現有日誌**：讀 `docs/DEV_LOG.md` 全文
3. **計算迭代編號**：搜尋最後一個 `## [...] 迭代 #N` → 新迭代為 `#N+1`；若找不到則為 `#1`
4. **收集本次變更**：
   - `git log main..HEAD --oneline` 取得 commit SHA 與訊息
   - 對應到 ROADMAP Step
   - 確認 PR 編號（`gh pr view --json number` 或從分支名推斷）
5. **依七欄位模板撰寫**（見 `DEV_LOG_RULES.md` §4）
6. **Append 到檔案末尾**：使用 Edit 工具 append（old_string 為檔案最後一個非空行，new_string 為同行 + 新迭代內容），**絕不覆寫既有紀錄**
7. **確認六項必做更新 checkpoint** 皆勾選或明確標註為何略過

## 作者欄位判斷

| 場景 | 作者欄位 |
|------|----------|
| 人工開發 | GitHub username（例 `LostSunset`） |
| @claude 自動同步 | `auto/upstream-sync` |
| 人機協作 | `LostSunset + claude` |

## 檢查清單（寫完自檢）

- [ ] 迭代編號正確遞增
- [ ] 時間戳格式 `YYYY-MM-DD HH:mm`（UTC+8），自動化流程加 `(UTC)`
- [ ] 每條「完成項目」附 commit SHA 或 `(WIP)`
- [ ] 欄位齊全（📋 ✅ 🐛 📊 🔄 🔗 為必填，💡 選填）
- [ ] 未覆寫既有迭代
