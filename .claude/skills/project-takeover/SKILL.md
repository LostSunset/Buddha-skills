---
name: project-takeover
description: 接手既有程式碼的考古與治理建立流程，執行 CLAUDE-CODE-自動開發指令.md 模式 D 的 Phase D0–D2。當使用者說「接手這個專案」「mode D」「做專案考古」「TAKEOVER_REPORT」「既有 codebase 分析」「這是個舊專案」時使用。前置條件：無 ROADMAP.md 但已有大量程式碼或 git history。執行檔案掃描、架構逆向、健康度評估、地雷掃描，建立 CLAUDE/MEMORY/ROADMAP（R1–R4 階段）後切入模式 B。
---

# project-takeover

對既有 codebase 做系統化考古、建立治理結構。前 2–3 小時**絕不寫任何業務程式碼**。

## 前置條件

```bash
# 無 ROADMAP 但有大量原始碼或 git history
test ! -f ROADMAP.md
SRC=$(find . -type f \( -name "*.py" -o -name "*.js" -o -name "*.ts" -o -name "*.rs" -o -name "*.go" -o -name "*.java" \) -not -path './node_modules/*' -not -path './.git/*' | wc -l)
COMMITS=$(git log --oneline 2>/dev/null | wc -l)
test "$SRC" -gt 5 -o "$COMMITS" -gt 10
```

若條件不符（`src` 檔少 + commit 少）→ 改用 `new-project-init`。

## Phase D0：考古（前 2–3 小時）

### D0.1 專案掃描 → `docs/TAKEOVER_REPORT.md`

```bash
# 檔案結構
tree -L 3 -I 'node_modules|.git|__pycache__|dist|build' > docs/tree.txt

# Git 考古
git log --oneline | wc -l                                                # 總 commit 數
git log --oneline -30                                                    # 近 30 筆
git log --format='%an' | sort | uniq -c | sort -rn                       # 貢獻者
git log --since='6 months ago' --oneline | wc -l                         # 近半年活躍度
git log --diff-filter=M --name-only --format='' | sort | uniq -c | sort -rn | head -20   # 熱點檔案

# 依賴
cat package.json requirements.txt Cargo.toml go.mod 2>/dev/null

# 測試
ls tests/ test/ spec/ 2>/dev/null
```

### D0.2 架構逆向 → `docs/ARCHITECTURE_REVERSE.md`

- 入口點辨識（main / index / app）
- 模組地圖（Mermaid：已理解 vs 待釐清）
- 資料流（來源 → 處理 → 輸出）
- 設計模式（MVC / Event-driven / Plugin）

### D0.3 健康度評估 → `docs/HEALTH_ASSESSMENT.md`

| 維度 | 分數(1-5) |
|------|-----------|
| 程式碼品質 | ? |
| 測試覆蓋 | ? |
| 文件完整性 | ? |
| 依賴健康 | ? |
| 架構清晰度 | ? |
| 部署就緒度 | ? |
| 技術債嚴重度 | ? |
| Git 衛生 | ? |

### D0.4 地雷掃描 → `docs/LANDMINES.md`

```bash
# 危險標記
grep -rn 'TODO\|FIXME\|HACK\|XXX\|WARN\|DEPRECATED' --include='*.py' --include='*.js' --include='*.ts' . | wc -l

# 超長檔案（God Object 嫌疑）
find . -name '*.py' -o -name '*.js' -o -name '*.ts' | xargs wc -l | sort -rn | head -20

# 硬編碼敏感資訊
grep -rEn '(api[_-]?key|password|token|secret)\s*=\s*["\x27]' --include='*.py' --include='*.js' .

# 環境依賴
grep -rn 'os.uname\|platform.system' --include='*.py' .
```

## Phase D1：治理結構（第 3–4 小時）

### D1.1 `CLAUDE.md`
- 這個專案「是什麼」、「不是什麼」
- 既有風格（跟隨、不強改）
- 已知地雷與限制
- 建構 / 測試 / 執行指令

### D1.2 `MEMORY.md`
- 考古發現的上下文
- 原始開發者設計意圖（從 git log / README 推斷）
- 已知 workaround 與歷史原因

### D1.3 `ROADMAP.md`（接手版，R1–R4，80 步）

**關鍵差異**：每步多兩欄位

```markdown
- **變更風險**: 🟢 低 | 🟡 中 | 🔴 高
- **影響範圍**: [受影響模組]
- **回滾方案**: 若出問題如何復原
```

階段：
- **Phase R1 (1–15)：穩定化** — 修測試、補 CI、修依賴漏洞、補 `.env.example`、清 dead code
- **Phase R2 (16–35)：補強** — 補測試（熱點檔優先）、補文件、重構 God Object
- **Phase R3 (36–60)：功能開發** — 新功能附完整測試
- **Phase R4 (61–80)：成熟化** — 效能、安全審計、全面文件

### D1.4 Git Issue 同步
- 既有 Issue → 整合進 ROADMAP
- 為新步驟建 Issue，保留原脈絡

### D1.5 初始 commit

```bash
git checkout -b takeover/init-governance
git add docs/ CLAUDE.md MEMORY.md ROADMAP.md
git commit -m "docs: add governance structure for project takeover"
git push -u origin takeover/init-governance
gh pr create --title "Takeover: governance init" --body "..."
```

## Phase D2：切入模式 B（第 5 小時起）

治理建好後 → 呼叫 `auto-iterate`，但加上接手專案的保守策略：

1. **優先 Phase R1**，不跳級
2. **一次只改一個模組**，改完全測
3. **高風險步驟（🔴）** → 更詳細的 self-review
4. **尊重既有風格**：新 code 跟隨命名規範、不做純風格 reformatting
5. **持續考古**：每迭代更新 MEMORY、LANDMINES、ARCHITECTURE_REVERSE

## 結束回報

```
模式 D 考古 + 治理完成
- TAKEOVER_REPORT：已產出
- 架構逆向圖：N 個模組
- 健康度總分：X/40
- 發現地雷：TODO/FIXME/HACK N 個、超長檔 M 個、硬編碼敏感 K 筆
- ROADMAP：R1–R4 共 80 步（其中 🔴 高風險 H 步）
- 下一步：呼叫 auto-iterate 從 Step 1（R1 穩定化）開始
```

## 硬規則

- 考古階段**絕不寫業務 code**，只產文件
- 跟隨既有風格，除非有明確技術理由才替換
- 不動 `upstream/**`
- 正體中文
