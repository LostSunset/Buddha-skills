---
name: milestone-pipeline
description: 把中大型專案（預估超過 30 步）切成 N 個可獨立交付的里程碑，每個里程碑走一次完整的 brainstorm→spec→plan→subagent-driven execute→PR→merge 流水線，再進下一個。當使用者說「做一個完整的 XX 專案」「從零建 WebGUI / 產品」「這個專案太大了怎麼拆」「做完 M2 換 M3」「幫我走完所有 milestone」時使用。與 `new-project-init` 不同：後者專注 Phase 0（需求/設計/ROADMAP），本 skill 接手 Phase 0 之後「實際開發的 N 次迭代」。
---

# milestone-pipeline

把大專案拆成 N 個里程碑，每個里程碑交付可獨立測試的軟體。

## 前置條件

- 已有 spec 或 design doc（由 `brainstorming` 產出）
- 每個里程碑可以獨立回歸測試（不是「寫到一半」的狀態）
- `superpowers:writing-plans` 與 `superpowers:subagent-driven-development` 可用

## 核心原則

1. **每個里程碑一份 plan**：不用一份 50 task 的 mega-plan。N 份 10–12 task 的小 plan，更容易停下來修正。
2. **每個里程碑一條分支一個 PR**：PR 合併之後再開下一個里程碑的分支。
3. **每個里程碑必須可交付可測試**：不能是「寫到一半」的狀態。
4. **里程碑之間的 API 在 spec 就定義好**：M3 依賴 M2 的介面必須在 M2 spec 裡寫死，不能 M3 開始才發現要回頭改 M2。

## 流程

```
spec 已就緒
    │
    ▼
為 M2 寫 plan（writing-plans）── docs/superpowers/plans/YYYY-MM-DD-m2-<topic>.md
    │
    ▼
開分支 feature/m2-<topic> ── 執行 subagent-driven-development
    │
    ▼
push + PR + CI 綠燈 + merge → main
    │
    ▼
（重複）為 M3 寫 plan → 分支 → 執行 → PR → merge
    │
    ▼
（最後）M6 收尾：測試覆蓋、lint、docker、CI、README
```

## 里程碑大小建議

- **10–12 個 task** / 里程碑。少於 8 代表切太細，多於 15 代表該拆。
- **1–3 天工作量**（人工）或 **1–3 小時 subagent 流水線**（自動）。
- 每個 task 都是 TDD 小步：寫失敗測試 → 實作 → 測試通過 → commit。

## 常見切分

| 專案類型 | 典型里程碑 |
|---------|-----------|
| 全端 app | M2 core/domain · M3 API · M4 frontend skeleton · M5 frontend features · M6 polish |
| CLI 工具 | M2 核心邏輯 · M3 CLI wrapper · M4 測試+文件 · M5 發佈 |
| Library | M2 介面+基礎 · M3 進階功能 · M4 benchmark · M5 文件+release |

## 硬規則

- **禁止跨里程碑的「依賴半成品」**：M3 不能 import M2 裡「下個 PR 才會實作」的函式。
- **禁止一份 mega-plan**：每個里程碑產出一份獨立的 `docs/superpowers/plans/*.md`。
- **禁止 main 直接 push**：必有 PR，必有 CI gate（至少 devlog-check + test）。
- **每個里程碑 merge 後要收尾 devlog**：`type: feature`、scope、決策紀錄、測試數字、後續工作。

## 與其他 skill 的銜接

| 前一步 | 本 skill | 下一步 |
|-------|---------|-------|
| `brainstorming` 產 spec | `milestone-pipeline` 切分 + 迭代 | 個別里程碑用 `writing-plans` + `subagent-driven-development` |
| `new-project-init`（Phase 0） | `milestone-pipeline`（Phase 1–5） | `maintenance-patrol`（上線後維運） |

## 常見陷阱

- **只寫了 M2 plan 就開始 M2 實作，還沒想清楚 M3**：M3 的 API 輪廓應該在 spec 裡就有，不必寫 plan，但要確定不會卡死。
- **里程碑太長**：M5 有 30 個 task → 拆成 M5a / M5b。
- **合併時發現跨里程碑 refactor**：代表切分錯了，通常是 M2 沒把 shared utility 寫好；重新評估 M2 scope。
- **PR CI 紅燈急著 bypass**：修到綠，不要 force-merge；這是 milestone 交付的硬門檻。

## 檢查清單（每個里程碑完成時）

- [ ] plan 文件 commit 在 main
- [ ] 分支已 merge 到 main
- [ ] PR 內所有 CI check 綠燈
- [ ] 里程碑 devlog 已寫（含測試數字與後續工作）
- [ ] README / 主文件有更新里程碑狀態
- [ ] 下一個里程碑的 plan 要麼已寫、要麼明確列 out of scope 不做
