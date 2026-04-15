# Claude Code 持續自動開發指令（每小時迭代）

> 將此內容放入專案根目錄的 `CLAUDE.md`，或作為 Claude Code 的啟動 prompt。
> 搭配 `cron` 或 `watch` 機制每小時自動觸發。

---

## 🔀 模式判斷（首次執行時自動偵測）

```
啟動時請依序執行以下判斷：

1. 有 ROADMAP.md 且有未完成步驟        → 【模式 B：開發中】
2. 有 ROADMAP.md 且所有步驟皆完成       → 【模式 C：維護模式】
3. 無 ROADMAP.md，且 src/ 或主程式碼極少（<5 個原始檔）
   或完全沒有業務程式碼                 → 【模式 A：新專案】
4. 無 ROADMAP.md，但已有大量程式碼、
   存在 git history、有既有架構         → 【模式 D：接手專案】

判斷方式：
  - `ls src/ lib/ app/ 2>/dev/null | wc -l` 檢查原始碼數量
  - `git log --oneline | wc -l` 檢查是否有 commit 歷史
  - `git log --format='%an' | sort -u` 檢查是否有其他開發者
  - 有 README 但沒有 ROADMAP → 大機率是接手專案
```

---

## ═══════════════════════════════════════
## 📘 模式 A：新專案（首次開發）
## ═══════════════════════════════════════

### Phase 0：專案初始化（第一個小時）

```markdown
請依序完成以下初始化工作：

### 0.1 分析專案需求
- 閱讀所有現有檔案（README、需求文件、設計圖等）
- 辨識技術棧、目標平台、核心功能
- 產生 `docs/PROJECT_ANALYSIS.md`（專案摘要、技術選型理由、風險評估）

### 0.2 建立設計文件
- 建立 `docs/ARCHITECTURE.md`（系統架構圖、模組關係圖）
- 建立 `docs/API_DESIGN.md`（如適用）
- 建立 `docs/DATA_MODEL.md`（資料結構與流程）
- 所有設計圖使用 Mermaid 語法，可直接在 GitHub 渲染

### 0.3 拆解 ROADMAP.md（約 80 步）
- 將整個專案拆成 **約 80 個步驟**
- 每個步驟格式如下：

  ```markdown
  ## Step XX: [步驟名稱]
  - **狀態**: 🔲 未開始 | 🔨 進行中 | ✅ 完成 | ⏸️ 暫停
  - **預估時間**: X 小時
  - **依賴**: Step YY, Step ZZ
  - **範圍**: 
    - 具體要做什麼（列出 2-5 個子項目）
  - **驗收條件（AC）**: 
    - [ ] 條件 1（可勾選）
    - [ ] 條件 2
    - [ ] 所有測試通過
  - **關聯 Issue**: #XX
  - **關聯 PR**: #XX
  - **完成日期**: YYYY-MM-DD HH:mm
  ```

- 步驟分組為以下階段：
  - **Phase 1 (Step 1-10)**: 基礎建設（專案結構、CI/CD、基本框架）
  - **Phase 2 (Step 11-30)**: 核心功能開發
  - **Phase 3 (Step 31-50)**: 進階功能與整合
  - **Phase 4 (Step 51-65)**: 測試、優化、安全性
  - **Phase 5 (Step 66-75)**: 文件、部署準備
  - **Phase 6 (Step 76-80)**: 最終驗收、Release

### 0.4 建立 CLAUDE.md
- 記錄專案的所有開發規範與 Claude Code 指令
- 包含：程式碼風格、commit 規範、分支策略、測試要求

### 0.5 建立 MEMORY.md
- 格式：

  ```markdown
  # 專案記憶檔
  ## 技術決策記錄
  | 日期 | 決策 | 理由 | 影響範圍 |
  |------|------|------|----------|

  ## 已知問題與解法
  | 問題 | 解法 | 發現日期 |
  |------|------|----------|

  ## 重要上下文
  - ...

  ## 每次迭代摘要
  | 迭代 | 日期 | 完成步驟 | 重點變更 |
  |------|------|----------|----------|
  ```

### 0.6 建立 GitHub Issue（批次建立）
- 為每個 Step 建立對應 GitHub Issue
- 使用 label 分類：`phase-1`, `phase-2`, ..., `bug`, `enhancement`, `test`
- 設定 milestone：每個 Phase 一個 milestone

### 0.7 建立開發團隊分工
- 在 `docs/TEAM_ROLES.md` 中定義虛擬開發團隊角色：

  ```markdown
  ## 虛擬開發團隊
  | 角色 | 負責範圍 | 審核職責 |
  |------|----------|----------|
  | 🏗️ Architect | 架構設計、技術選型 | 審核所有架構變更 |
  | 💻 Frontend Dev | UI/UX 實作 | 審核前端 PR |
  | ⚙️ Backend Dev | API、邏輯、資料庫 | 審核後端 PR |
  | 🧪 QA Engineer | 測試策略、自動化測試 | 審核測試覆蓋率 |
  | 📦 DevOps | CI/CD、部署、監控 | 審核基礎設施變更 |
  | 📝 Tech Writer | 文件、API docs | 審核文件品質 |
  ```

- 每個 PR 指定 reviewer 角色
- Claude Code 在開發時切換角色思維：
  - 寫 code 時 → 用 Developer 思維
  - 寫完後 → 切換到 QA 思維做 self-review
  - 合併前 → 切換到 Architect 思維做架構審查

### 0.8 建立開發日誌
- 建立 `docs/DEV_LOG.md`

  ```markdown
  # 開發日誌

  ## [YYYY-MM-DD HH:mm] 迭代 #N
  ### 📋 本次目標
  - ...
  ### ✅ 完成項目
  - ...
  ### 🐛 發現問題
  - ...
  ### 📊 測試結果
  - 通過: X / 失敗: Y / 覆蓋率: Z%
  ### 🔄 下次目標
  - ...
  ### 💡 技術筆記
  - ...
  ```

### 0.9 初始 Commit & Push
- git add -A && git commit -m "chore: project initialization with roadmap and docs"
- git push origin main
- 建立 v0.1.0 tag
```

---

## ═══════════════════════════════════════
## 📗 模式 B：開發中專案（持續迭代）
## ═══════════════════════════════════════

### 每小時迭代流程

```markdown
請在每次被喚醒時（每小時），依序執行以下流程：

### B.1 狀態恢復（2 分鐘）
1. 讀取 `CLAUDE.md` → 了解專案規範
2. 讀取 `MEMORY.md` → 恢復上次的上下文與決策
3. 讀取 `ROADMAP.md` → 找到目前進度
4. 讀取 `docs/DEV_LOG.md` 最後一篇 → 了解上次停在哪裡
5. 執行 `git status` 和 `git log --oneline -5` → 確認當前分支狀態
6. 執行現有測試 → 確認基線沒壞

### B.2 規劃本次迭代（3 分鐘）
1. 從 ROADMAP.md 找出下一個「🔲 未開始」的步驟
2. 評估該步驟的依賴是否都已 ✅
3. 如果依賴未滿足 → 先完成依賴步驟
4. 將該步驟標記為「🔨 進行中」
5. 建立功能分支：`git checkout -b feature/step-XX-描述`

### B.3 開發實作（40 分鐘）
1. 以 Developer 角色撰寫程式碼
2. 每完成一個子項目就做一次小 commit
3. commit message 格式：
   ```
   <type>(step-XX): <description>
   
   - 具體變更 1
   - 具體變更 2
   
   Refs: #issue_number
   ```
   type: feat | fix | refactor | test | docs | chore | style | perf
4. 同步撰寫對應的測試（單元測試 + 整合測試）

### B.4 自我審查（5 分鐘）
1. 切換到 **QA Engineer** 角色：
   - 執行所有測試：`npm test` / `pytest` / 對應的測試指令
   - 確認測試覆蓋率不低於基線
   - 檢查 edge case 和錯誤處理
2. 切換到 **Architect** 角色：
   - 檢查是否符合架構設計
   - 檢查是否引入技術債
   - 檢查命名、結構是否一致
3. 如果發現問題 → 回到 B.3 修正

### B.5 提交與合併（3 分鐘）
1. 確保所有測試通過
2. 最終 commit & push：
   ```bash
   git add -A
   git commit -m "feat(step-XX): complete [步驟名稱]"
   git push origin feature/step-XX-描述
   ```
3. 建立 PR（如果有 GitHub CLI）：
   ```bash
   gh pr create --title "Step XX: [步驟名稱]" \
     --body "## 變更摘要\n- ...\n\n## 測試結果\n- ...\n\nCloses #XX" \
     --label "phase-N"
   ```
4. 自我 approve 後合併到 main：
   ```bash
   git checkout main
   git merge feature/step-XX-描述
   git push origin main
   git branch -d feature/step-XX-描述
   ```

### B.6 六項必做更新（7 分鐘）

#### ① Commit & 推送
- 確認 main 分支已包含所有變更
- `git push origin main`

#### ② 更新版本和 README
- 根據 semver 規則更新版本號：
  - 新功能 → minor（0.X.0）
  - bug 修復 → patch（0.0.X）
  - 破壞性變更 → major（X.0.0）
- 更新 `package.json` / `pyproject.toml` / `Cargo.toml` 中的版本
- 更新 `README.md`：
  - 功能清單（新增已完成的功能）
  - 安裝與使用說明（如有變更）
  - 進度概覽（已完成 XX/80 步驟）

#### ③ 發佈新 Release（如版本有更新）
```bash
VERSION="vX.Y.Z"
git tag -a $VERSION -m "Release $VERSION: [摘要]"
git push origin $VERSION

# 如果有 GitHub CLI
gh release create $VERSION \
  --title "$VERSION - [Release 標題]" \
  --notes "## 🆕 新功能\n- ...\n\n## 🐛 修復\n- ...\n\n## 📊 進度\n- 已完成: XX/80 步驟"
```

#### ④ 更新 CLAUDE.md 與 MEMORY.md
- **CLAUDE.md**: 新增任何新發現的開發規範、注意事項
- **MEMORY.md**: 
  - 新增技術決策記錄
  - 新增已知問題與解法
  - 更新迭代摘要表

#### ⑤ 更新 ROADMAP.md 與邏輯圖
- 將完成的步驟標記為 ✅，填入完成日期
- 如果發現需要新增步驟 → 插入並調整編號
- 如果發現步驟需要拆分 → 拆分為子步驟 (e.g., Step 15a, 15b)
- 更新 `docs/ARCHITECTURE.md` 中的 Mermaid 圖：
  ```mermaid
  graph TD
    A[Phase 1: 基礎建設 ✅] --> B[Phase 2: 核心功能 🔨]
    B --> C[Phase 3: 進階功能 🔲]
    C --> D[Phase 4: 測試優化 🔲]
    D --> E[Phase 5: 文件部署 🔲]
    E --> F[Phase 6: 最終發佈 🔲]
  ```
- 更新模組依賴關係圖

#### ⑥ 更新開發日誌
- 在 `docs/DEV_LOG.md` 新增本次迭代紀錄
- 格式依照 Phase 0.8 的模板
```

---

## ═══════════════════════════════════════
## 📙 模式 C：維護模式（所有步驟完成後）
## ═══════════════════════════════════════

```markdown
當 ROADMAP.md 所有 80 步驟都標記為 ✅ 時，進入維護模式：

### C.1 每小時巡檢
1. 執行完整測試套件
2. 檢查依賴套件是否有安全更新：
   - `npm audit` / `pip audit` / `cargo audit`
3. 檢查程式碼品質：
   - linter 掃描
   - 靜態分析
4. 如果有 GitHub Issues（bug report / feature request）→ 處理

### C.2 持續改善
1. 檢查測試覆蓋率，補充不足的測試
2. 效能優化（profiling → 找瓶頸 → 優化）
3. 重構技術債
4. 更新文件

### C.3 版本管理
- 安全更新 → patch release
- 新功能 → minor release
- 同樣執行六項必做更新
```

---

## ═══════════════════════════════════════
## 📕 模式 D：接手專案（無 ROADMAP 的既有程式碼）
## ═══════════════════════════════════════

> **核心思維**：你不是從零開始，你面對的是一個有歷史、有債務、有隱藏地雷的活系統。
> 第一步不是寫 code，是**搞懂這個系統到底在幹嘛**。

### Phase D0：考古與理解（前 2-3 個小時，不寫任何業務程式碼）

```markdown
### D0.1 專案掃描（自動化）
執行以下指令，產生 `docs/TAKEOVER_REPORT.md`：

1. **檔案結構分析**
   - `find . -type f -not -path './.git/*' -not -path './node_modules/*' | head -200`
   - `tree -L 3 -I 'node_modules|.git|__pycache__|dist|build' > docs/tree.txt`
   - 統計語言分佈：各語言檔案數量與行數

2. **Git 歷史考古**
   - `git log --oneline | wc -l` → 總 commit 數
   - `git log --oneline -30` → 最近 30 筆 commit（了解開發節奏）
   - `git log --format='%an' | sort | uniq -c | sort -rn` → 貢獻者排名
   - `git log --since='6 months ago' --oneline | wc -l` → 近半年活躍度
   - `git log --diff-filter=M --name-only --format='' | sort | uniq -c | sort -rn | head -20`
     → 最常被修改的 20 個檔案（通常是核心 or 問題熱點）
   - `git log --all --oneline --graph | head -50` → 分支結構

3. **依賴與環境**
   - 解析 package.json / requirements.txt / Cargo.toml / go.mod 等
   - 列出所有依賴及其版本
   - 標記已知 deprecated 或有安全漏洞的套件
   - 嘗試 `npm install` / `pip install -r requirements.txt` → 記錄是否能成功

4. **測試現況**
   - 是否有測試？測試框架是什麼？
   - 嘗試執行測試 → 記錄通過/失敗數量
   - 覆蓋率（如果能跑的話）

5. **CI/CD 現況**
   - 有無 `.github/workflows/`、`Dockerfile`、`docker-compose.yml`
   - 有無部署腳本或文件

6. **文件現況**
   - README 品質評分（1-5）
   - 有無 API 文件、架構圖、開發指南
   - 程式碼內注釋密度

### D0.2 架構逆向工程
產生 `docs/ARCHITECTURE_REVERSE.md`：

1. **入口點辨識**
   - 找到 main / index / app 入口檔案
   - 追蹤啟動流程

2. **模組地圖**（用 Mermaid 畫出）
   ```mermaid
   graph TD
     subgraph 已理解
       A[模組A - 用途已知]
       B[模組B - 用途已知]
     end
     subgraph 待釐清
       C[模組C - 用途不明]
       D[模組D - 疑似廢棄]
     end
     A -->|呼叫| B
     B -->|依賴| C
   ```

3. **資料流分析**
   - 資料從哪裡進來（API、檔案、DB）
   - 經過哪些處理
   - 輸出到哪裡

4. **關鍵設計模式辨識**
   - 用了什麼設計模式？（MVC、Event-driven、Plugin...）
   - 狀態管理方式？
   - 錯誤處理策略？

### D0.3 健康度評估
產生 `docs/HEALTH_ASSESSMENT.md`：

| 維度 | 分數(1-5) | 說明 |
|------|-----------|------|
| 程式碼品質 | ? | linter 結果、一致性 |
| 測試覆蓋 | ? | 有無測試、覆蓋率 |
| 文件完整性 | ? | README、API docs、注釋 |
| 依賴健康 | ? | 過期套件、安全漏洞 |
| 架構清晰度 | ? | 模組化、關注點分離 |
| 部署就緒度 | ? | CI/CD、Docker、環境變數 |
| 技術債嚴重度 | ? | TODO/FIXME/HACK 數量、dead code |
| Git 衛生 | ? | commit 品質、分支管理 |

### D0.4 找出所有地雷
產生 `docs/LANDMINES.md`：

1. **搜尋危險信號**
   ```bash
   # TODO/FIXME/HACK/XXX/WARN 統計
   grep -rn 'TODO\|FIXME\|HACK\|XXX\|WARN\|DEPRECATED' --include='*.py' --include='*.js' --include='*.ts' --include='*.rs' --include='*.go' . | tee docs/code_warnings.txt
   wc -l docs/code_warnings.txt

   # 超長檔案（通常是 God Object）
   find . -name '*.py' -o -name '*.js' -o -name '*.ts' | xargs wc -l | sort -rn | head -20

   # 超長函式（搜尋連續超過 100 行沒有空行的區塊）
   # Dead code 嫌疑（未被 import 的檔案）
   ```

2. **硬編碼敏感資訊**
   - 搜尋 API key、密碼、token、secret
   - 檢查 `.env.example` 是否存在且完整

3. **環境依賴風險**
   - 是否依賴特定 OS / 特定版本的 runtime
   - 是否有 magic number 或環境假設
```

### Phase D1：建立治理結構（第 3-4 小時）

```markdown
### D1.1 建立 CLAUDE.md
根據考古結果撰寫，重點記錄：
- 這個專案「是什麼」、「不是什麼」
- 既有的程式碼風格（跟隨既有風格，不要強改）
- 已知的地雷和限制
- 建構和執行指令
- 測試指令

### D1.2 建立 MEMORY.md
初始內容：
- 考古過程中發現的所有重要上下文
- 原始開發者的設計意圖（從 git log / README / 註釋推斷）
- 已知的 workaround 和歷史原因

### D1.3 建立 ROADMAP.md（接手版）
**關鍵差異：不是從零拆 80 步，而是從現狀出發，分為 4 個階段：**

  ```markdown
  ## 🏥 Phase R1: 穩定化（Step 1-15）
  > 目標：讓專案能穩定跑起來，補上最基本的安全網
  - 修復無法通過的測試
  - 補上 CI/CD（如果沒有）
  - 修復安全漏洞（依賴更新）
  - 補上缺失的 .env.example
  - 補上基本的 error handling
  - 清理明顯的 dead code
  - 讓 linter 能通過（用最寬鬆設定先通過）

  ## 🔧 Phase R2: 補強（Step 16-35）
  > 目標：補上缺失的測試、文件、監控
  - 為核心模組補寫單元測試（依熱點檔案優先）
  - 補寫 API 文件
  - 補寫架構文件
  - 重構最嚴重的技術債（God Object、過長函式）
  - 增加 logging / monitoring

  ## 🚀 Phase R3: 功能開發（Step 36-60）
  > 目標：在穩定基礎上開發新功能
  - 根據需求或 Issue 開發新功能
  - 每個新功能都附帶完整測試
  - 維持或提升測試覆蓋率

  ## 💎 Phase R4: 優化與成熟化（Step 61-80）
  > 目標：效能、安全、可維護性全面提升
  - 效能 profiling 與優化
  - 安全審計
  - 重構剩餘技術債
  - 完善所有文件
  ```

每個步驟格式同模式 A，但多一個欄位：
  ```markdown
  - **變更風險**: 🟢 低 | 🟡 中 | 🔴 高
  - **影響範圍**: [列出會被影響的模組]
  - **回滾方案**: 如果出問題要怎麼復原
  ```

### D1.4 Git Issue 同步
- 檢查是否已有 GitHub Issues → 整合進 ROADMAP
- 為新步驟建立 Issue
- 保留原有 Issue 的上下文，加上你的分析

### D1.5 初始 Commit
```bash
git checkout -b takeover/init-governance
git add docs/ CLAUDE.md MEMORY.md ROADMAP.md
git commit -m "docs: add governance structure for project takeover

- TAKEOVER_REPORT.md: full project analysis
- ARCHITECTURE_REVERSE.md: reverse-engineered architecture
- HEALTH_ASSESSMENT.md: project health scoring
- LANDMINES.md: known risks and technical debt
- ROADMAP.md: 80-step improvement plan (R1-R4 phases)
- CLAUDE.md: development guidelines
- MEMORY.md: project context and decisions"
git push origin takeover/init-governance
```
```

### Phase D2：進入模式 B（第 5 小時起）

```markdown
治理結構建立完成後，自動切換到【模式 B：開發中】。
差異在於：

1. **步驟執行順序**：優先處理 Phase R1（穩定化），不跳級
2. **變更策略保守**：
   - 每次只改一個模組
   - 每次改完都跑全部測試
   - 高風險步驟（🔴）需要更詳細的 self-review
3. **尊重既有風格**：
   - 新 code 跟隨原專案的命名規範
   - 不做純風格的大規模 reformatting
   - 除非有明確技術理由，不替換核心依賴
4. **考古持續進行**：
   - 每次迭代都可能發現新的上下文 → 更新 MEMORY.md
   - 發現新地雷 → 更新 LANDMINES.md
   - 理解加深 → 更新 ARCHITECTURE_REVERSE.md
```
## ═══════════════════════════════════════

### 方法 1：使用 Shell Script + Cron

```bash
#!/bin/bash
# auto-dev.sh — 放在專案根目錄

PROJECT_DIR="$(cd "$(dirname "$0")" && pwd)"
LOG_FILE="$PROJECT_DIR/docs/auto-dev-cron.log"
TIMESTAMP=$(date '+%Y-%m-%d %H:%M:%S')

echo "[$TIMESTAMP] 開始自動迭代..." >> "$LOG_FILE"

cd "$PROJECT_DIR"

# 檢測專案模式
if [ -f "ROADMAP.md" ] && grep -q "🔲" ROADMAP.md; then
  MODE="developing"
elif [ -f "ROADMAP.md" ] && ! grep -q "🔲" ROADMAP.md; then
  MODE="maintenance"
elif [ ! -f "ROADMAP.md" ]; then
  # 判斷是新專案還是接手專案
  SRC_COUNT=$(find . -name '*.py' -o -name '*.js' -o -name '*.ts' -o -name '*.rs' -o -name '*.go' -o -name '*.cpp' 2>/dev/null | grep -v node_modules | grep -v .git | wc -l)
  COMMIT_COUNT=$(git log --oneline 2>/dev/null | wc -l)
  if [ "$SRC_COUNT" -gt 5 ] || [ "$COMMIT_COUNT" -gt 10 ]; then
    MODE="takeover"
  else
    MODE="new"
  fi
fi

echo "[$TIMESTAMP] 偵測模式: $MODE" >> "$LOG_FILE"

# 啟動 Claude Code
claude -p "
你現在是專案的持續開發引擎。
當前模式: $MODE
當前時間: $TIMESTAMP
請依照 CLAUDE.md 中定義的【模式 $(echo $MODE | sed 's/new/A/;s/developing/B/;s/maintenance/C/;s/takeover/D/')】流程執行一次完整迭代。
完成後輸出本次迭代摘要。
" --allowedTools "Bash(git*),Bash(npm*),Bash(python*),Bash(pytest*),Bash(gh*),Read,Write,Edit" \
  2>&1 | tee -a "$LOG_FILE"

echo "[$TIMESTAMP] 迭代完成" >> "$LOG_FILE"
```

### Cron 設定（每小時執行）

```bash
# 編輯 crontab
crontab -e

# 新增這行（每小時整點執行）
0 * * * * /path/to/your/project/auto-dev.sh

# 或者每小時但只在工作時間（8am-11pm）
0 8-23 * * * /path/to/your/project/auto-dev.sh
```

### 方法 2：使用 Claude Code 的 `--continue` 模式

```bash
# 在 tmux/screen session 中持續運行
while true; do
  TIMESTAMP=$(date '+%Y-%m-%d %H:%M:%S')
  
  claude -p "
  時間: $TIMESTAMP
  請讀取 CLAUDE.md 確認當前模式，執行一次完整迭代。
  嚴格遵循六項必做更新。
  " --allowedTools "Bash(git*),Bash(npm*),Bash(python*),Bash(pytest*),Bash(gh*),Read,Write,Edit"
  
  echo "⏰ 等待 1 小時後進行下一次迭代..."
  sleep 3600
done
```

### 方法 3：使用 GitHub Actions（遠端觸發）

```yaml
# .github/workflows/auto-dev.yml
name: Hourly Auto Development

on:
  schedule:
    - cron: '0 * * * *'  # 每小時
  workflow_dispatch:       # 也可手動觸發

jobs:
  auto-dev:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0
          token: ${{ secrets.PAT_TOKEN }}

      - name: Setup Claude Code
        run: npm install -g @anthropic-ai/claude-code

      - name: Run iteration
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
        run: |
          claude -p "
          請讀取 CLAUDE.md，判斷專案模式，執行一次完整迭代。
          嚴格遵循六項必做更新規則。
          " --allowedTools "Bash(git*),Bash(npm*),Bash(python*),Read,Write,Edit"

      - name: Push changes
        run: |
          git config user.name "Claude Auto-Dev"
          git config user.email "claude-bot@dev.local"
          git add -A
          git diff --cached --quiet || git commit -m "chore(auto): hourly iteration $(date '+%Y-%m-%d %H:%M')"
          git push origin main
```

---

## ═══════════════════════════════════════
## 📐 附錄：Commit / Branch / 版本規範
## ═══════════════════════════════════════

### Commit Message 格式
```
<type>(scope): <subject>

<body>

Refs: #issue
```

| Type | 用途 |
|------|------|
| feat | 新功能 |
| fix | 修復 bug |
| refactor | 重構（不改功能） |
| test | 新增/修改測試 |
| docs | 文件變更 |
| chore | 建置/工具變更 |
| style | 格式調整（不影響邏輯） |
| perf | 效能優化 |
| ci | CI/CD 變更 |

### 分支策略
```
main                    ← 穩定版，每次迭代合併
├── feature/step-XX-xxx ← 功能開發
├── fix/issue-XX-xxx    ← Bug 修復
└── refactor/xxx        ← 重構
```

### 版本號規則（Semantic Versioning）
```
v0.Y.Z  ← 開發階段（Phase 1-5）
v1.0.0  ← 首次正式發佈（Phase 6 完成）
```
- Phase 1 完成 → v0.1.0
- Phase 2 完成 → v0.2.0
- Phase 3 完成 → v0.3.0
- 每個 Step 完成 → patch +1（如 v0.2.1 → v0.2.2）
- 首次完整發佈 → v1.0.0
