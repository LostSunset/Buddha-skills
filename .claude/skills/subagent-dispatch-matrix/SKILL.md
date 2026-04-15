---
name: subagent-dispatch-matrix
description: 決定一次 subagent dispatch 包含幾個 task、要不要跑完整 spec+quality 兩階段審查、用哪個 model。當使用者說「這些 task 要不要合併發派」「subagent 太慢」「一次做三個 task 可以嗎」「哪些 task 要 review、哪些可以 skip」「用 sonnet 還是 opus」時使用。目的：在品質與速度間做有根據的取捨，避免每個 task 都死板走三輪審查。
---

# subagent-dispatch-matrix

Subagent dispatch 的規模與審查強度決策矩陣。

## 核心觀念

`superpowers:subagent-driven-development` 的預設節奏是「1 implementer + 1 spec reviewer + 1 quality reviewer = 3 次 dispatch / task」。完整執行 12 task 就是 36 次 dispatch。實戰裡這對機械性 task 是浪費，對判斷性 task 卻剛好。本 skill 幫你判斷每個 task 該走哪條路徑。

## 決策矩陣

### 維度 1 — Task 類型

| 類型 | 訊號 | 建議 |
|------|------|------|
| **機械** | 純照 plan 抄 code、1–2 檔、明確 expected output | 合併 2–3 個一起派，審查只跑 spec（略 quality） |
| **整合** | 跨多檔協調、需要 pattern matching | 獨立派，spec+quality 都跑 |
| **判斷** | 架構決策、效能、或 plan 寫得不夠死 | 獨立派、用較強 model、spec+quality 都跑且留意 reviewer 反饋 |

### 維度 2 — 衝突風險

| 情境 | 是否可合併 |
|------|-----------|
| 多 task 都只 add 新檔 | ✅ 合併安全 |
| 多 task 都改同一檔案 | ✅ 合併（讓單一 subagent 一次改完） |
| 多 task 互相 import / 依賴 | ✅ 合併（因為需要一起才能 typecheck 通過） |
| 多 task 要改彼此獨立的大檔 | ❌ 獨立派，避免 merge conflict |

### 維度 3 — Review 強度

| Task 性質 | Spec Review | Quality Review |
|-----------|------------|----------------|
| Scaffold / 裝依賴 | ❌ skip（實作即是「檢查有沒有裝」） | ❌ skip |
| 純 add file 小元件 | ✅ 必做（檢查命名、prop 與 plan 一致） | 看規模選（小於 80 行 skip 可） |
| 核心邏輯 / 適配器 | ✅ 必做 | ✅ 必做 |
| 修 bug / refactor | ✅ 必做 | ✅ 必做（容易壞別的地方） |

### 維度 4 — Model 選擇

| 任務 | 推薦 |
|------|------|
| 機械抄寫、單元測試 | `sonnet` |
| 跨檔整合、debug、review | `sonnet` 或 `opus`（視檔案量） |
| 架構設計、大重構、爭議仲裁 | `opus` |

## 實戰 heuristic

1. **12-task plan 不等於 12 次 dispatch**。典型分佈：3–5 次 dispatch 就夠。例如：T1 單派、T2+T3 合併、T4 單派、T5+T6 合併、T7+T8+T9 合併、T10+T11+T12 合併。
2. **Controller 在每次 dispatch 後看報告，決定是否 review**。報告說「全綠無問題」就跳過 quality review；報告說「我改了 plan 外的東西」就補 quality review。
3. **不要讓 subagent 讀 plan 檔**。Controller 把需要的 task 全文貼進 prompt，保持 subagent context 乾淨。
4. **一個 subagent 一次最多 3 個 task**。超過就分成兩次派，subagent context 會混亂。

## 硬規則

- **合併派的 task 必須有一條路徑能回滾**：最好每個 task 獨立 commit（subagent 在 prompt 裡指示「每完成一個 task 就 commit，不要等全部做完」）。
- **修 bug / reviewer 抓出的 issue 一律要 re-review**：不能跳過「改完再看一次」。
- **若 subagent 回報 DONE_WITH_CONCERNS 或 BLOCKED**：要麼 controller 親自確認，要麼補完整審查，不能直接 mark complete。

## 常見陷阱

- **過度審查**：每個 scaffold task 都跑 spec+quality，多花 60–70% 時間。
- **過度合併**：把 6 個判斷性 task 塞一個 subagent，context 塞爆、錯漏多。
- **為了快而 skip review 但 task 其實是判斷性**：下一個 task build on 錯誤假設，debug 成本更高。
- **每次都 opus**：sonnet 對機械 task 完全夠、快 2x、token 便宜；留 opus 給真正難的。

## 檢查清單（每次 dispatch 前）

- [ ] 這次包多少個 task？2–3 為上限
- [ ] 這些 task 之間有衝突風險嗎？若有，拆開
- [ ] 選哪個 model？機械用 sonnet、判斷用 opus
- [ ] 要跑 spec review 嗎？（判斷性 ✅、機械 skip）
- [ ] 要跑 quality review 嗎？（核心邏輯 ✅、小元件 skip）
- [ ] prompt 裡有完整的 code/expected output 嗎？

## 數字參考（實戰）

12-task 里程碑，典型 dispatch 分佈：
- 1 scaffold dispatch（T1）
- 4 實作 dispatch（T2 單獨、T3+T4+T5 合、T6+T7 合、T8+T9 合）
- 2 close-out dispatch（T10+T11、T12）
- 3–4 次 review dispatch（只在判斷性 task 跑完整兩輪）
- 1–2 次 fix dispatch（reviewer 抓到的問題）

總計約 **12–15 次** dispatch，比預設 36 次 少 60%。
