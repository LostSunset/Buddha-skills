---
name: auto-iterate
description: 一鍵執行 CLAUDE-CODE-自動開發指令.md 的模式 B（開發中專案）完整迭代。當使用者說「跑一次迭代」「執行下一個 step」「auto iterate」「做下一個 ROADMAP 任務」時使用。會自動走完 B.1 狀態恢復 → B.2 規劃 → B.3 開發 → B.4 自我審查 → B.5 提交合併 → B.6 六項必做更新，並在結尾以 dev-log skill 寫入迭代紀錄。
---

# auto-iterate

執行一次完整的模式 B 迭代。僅處理 `ROADMAP.md` 存在且尚有 🔲 未完成步驟的情境；若不符合請改用 `auto-dev-mode` 做整體模式偵測。

## 前置檢查

```bash
test -f ROADMAP.md && grep -q "🔲" ROADMAP.md
```

若不成立 → 停止並建議使用者改叫 `auto-dev-mode`。

## 六階段流程

### B.1 狀態恢復（2 分鐘）

1. 讀取 `CLAUDE.md`、`MEMORY.md`、`ROADMAP.md`
2. 讀取 `docs/DEV_LOG.md` 最後一筆迭代
3. `git status` 與 `git log --oneline -5`
4. 若有既存測試：執行一次取得基線

### B.2 規劃本次迭代（3 分鐘）

1. 找到第一個「🔲 未開始」且依賴已滿足的步驟
2. 將該步驟標為「🔨 進行中」
3. 建立分支：`git checkout -b feature/step-XX-<kebab-slug>`
4. 向使用者回報：即將處理哪一個 Step、預計子項目、分支名

### B.3 開發實作

- 以 **Skill Developer / DevOps 思維**實作
- 每完成一個子項目做一次小 commit，格式：

  ```
  <type>(step-XX): <description>

  - 變更 1
  - 變更 2
  ```

  type：`feat` / `fix` / `refactor` / `test` / `docs` / `chore` / `style` / `perf` / `ci`
- 同步撰寫對應測試（若步驟牽涉可測邏輯）

### B.4 自我審查

1. **QA 思維**：跑測試、檢查 edge case
2. **Architect 思維**：架構一致性、命名、技術債
3. **Guard 思維**：確認未動到 `upstream/<name>/**` 內容

發現問題 → 回 B.3 修正。

### B.5 提交與合併

```bash
git push -u origin feature/step-XX-<slug>
gh pr create --title "Step XX: <標題>" --body "<摘要 + Closes ROADMAP Step XX>"
# 自驗通過後
gh pr merge --squash --delete-branch
git checkout main && git pull
```

### B.6 六項必做更新

依 `CLAUDE.md` §「六項必做更新」逐條執行：
1. Commit & push（上面 B.5 已做）
2. 版本與 README 更新（若適用）
3. Release 發佈（若版本異動）
4. CLAUDE.md / MEMORY.md 更新
5. ROADMAP.md 更新（該 Step 改 ✅、填完成日期、勾選 AC）
6. 呼叫 `dev-log` skill append 新迭代

## 硬規則

- **嚴禁修改 `upstream/<name>/**` 內容**
- 所有文件、commit message 採正體中文
- 分支命名 `feature/step-XX-...` 或 `fix/...`

## 結束回報

格式：

```
迭代 #N 完成（Step XX）
- 分支：feature/step-XX-...
- PR：#XX（已合併）
- 本次 commit：<sha 列表>
- ROADMAP 進度：XX / 總步驟
- 下次目標：Step YY
```
