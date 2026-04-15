---
name: auto-dev-mode
description: 為 Buddha-skills 專案執行自動開發迭代。當使用者要求「執行一次迭代」「跑自動開發」「依 CLAUDE-CODE-自動開發指令執行」時使用，或被 cron/workflow 喚醒時觸發。會先偵測模式 A/B/C/D，再依 CLAUDE-CODE-自動開發指令.md 的對應流程執行，並在結束時呼叫 dev-log skill 寫日誌。
---

# auto-dev-mode

依 `CLAUDE-CODE-自動開發指令.md` 執行一次完整迭代。

## 模式偵測（優先序）

```
1. 有 ROADMAP.md 且含 🔲         → 模式 B（開發中）
2. 有 ROADMAP.md 且全部 ✅       → 模式 C（維護）
3. 無 ROADMAP.md 且 src 檔 < 5   → 模式 A（新專案）
4. 無 ROADMAP.md 但有大量程式碼  → 模式 D（接手）
```

檢查指令：

```bash
ls src/ lib/ app/ 2>/dev/null | wc -l
git log --oneline 2>/dev/null | wc -l
```

## 執行

依偵測結果進入 `CLAUDE-CODE-自動開發指令.md` 的對應章節：

- **模式 A** → Phase 0（專案初始化，八小節）
- **模式 B** → B.1–B.6（狀態恢復、規劃、實作、自我審查、提交合併、六項必做更新）
- **模式 C** → C.1–C.3（巡檢、持續改善、版本管理）
- **模式 D** → Phase D0–D2（考古、建立治理、切入模式 B）

## 結束動作

1. 呼叫 `dev-log` skill 寫入本次迭代
2. 依 `CLAUDE.md` §「六項必做更新」逐項確認
3. 輸出迭代摘要給使用者

## 注意

- **嚴禁修改 `upstream/` 內容**（僅允許 submodule pointer 由 `update-upstream.yml` 更新）
- 若偵測到 `upstream/` 內檔案被修改，立即回滾並警告使用者
- 所有文件與 commit message 使用正體中文
