---
name: new-project-init
description: 從零初始化新專案，執行 CLAUDE-CODE-自動開發指令.md 模式 A 的完整 Phase 0（9 小節）。當使用者說「新專案初始化」「從零開始做 XX 專案」「做 mode A」「拆 ROADMAP 80 步」「專案 kick-off」時使用。前置條件：專案尚無 ROADMAP.md 且原始碼檔少於 5 個。產生 PROJECT_ANALYSIS、ARCHITECTURE、API_DESIGN、DATA_MODEL、ROADMAP（80 步）、CLAUDE.md、MEMORY.md、TEAM_ROLES、DEV_LOG、首次 commit + v0.1.0 tag。
---

# new-project-init

執行 `CLAUDE-CODE-自動開發指令.md` §Phase 0 的九小節完整初始化。

## 前置條件（必要）

```bash
# 必須全部為真
test ! -f ROADMAP.md
SRC=$(find . -type f \( -name "*.py" -o -name "*.js" -o -name "*.ts" -o -name "*.rs" -o -name "*.go" \) -not -path './node_modules/*' -not -path './.git/*' | wc -l)
test "$SRC" -lt 5
```

若條件不符 → 停止並改用 `auto-dev-mode` 重新判斷（可能應進模式 B 或 D）。

## 九小節流程

### 0.1 需求分析 → `docs/PROJECT_ANALYSIS.md`
- 閱讀所有現有檔案（README、需求、設計圖）
- 產出：專案摘要、技術選型理由、風險評估

### 0.2 設計文件
- `docs/ARCHITECTURE.md`（Mermaid 系統架構 + 模組關係）
- `docs/API_DESIGN.md`（若適用）
- `docs/DATA_MODEL.md`（資料結構與流程）

### 0.3 拆解 ROADMAP.md（約 80 步）
步驟格式（每一步都要）：

```markdown
## Step XX: [名稱]
- **狀態**: 🔲 未開始 | 🔨 進行中 | ✅ 完成 | ⏸️ 暫停
- **預估時間**: X 小時
- **依賴**: Step YY, Step ZZ
- **範圍**:
  - 2-5 個子項目
- **驗收條件（AC）**:
  - [ ] 條件 1
  - [ ] 所有測試通過
- **關聯 Issue**: #XX
- **關聯 PR**: #XX
- **完成日期**: YYYY-MM-DD HH:mm
```

階段分組：
- Phase 1 (1–10)：基礎建設
- Phase 2 (11–30)：核心功能
- Phase 3 (31–50)：進階功能
- Phase 4 (51–65)：測試、優化、安全
- Phase 5 (66–75)：文件、部署
- Phase 6 (76–80)：最終驗收、Release

### 0.4 `CLAUDE.md`
記錄：程式碼風格、commit 規範、分支策略、測試要求。

### 0.5 `MEMORY.md`
四個表格：技術決策、已知問題、重要上下文、迭代摘要。

### 0.6 GitHub Issues 批次建立
- 每個 Step 一個 Issue
- Labels：`phase-1` … `phase-6`、`bug`、`enhancement`、`test`
- 每個 Phase 一個 Milestone

### 0.7 `docs/TEAM_ROLES.md`
六虛擬角色：Architect、Frontend、Backend、QA、DevOps、Tech Writer。
每個 PR 指定 reviewer 角色；Claude 依階段切換思維。

### 0.8 `docs/DEV_LOG.md`
模板七欄：📋 🎯 ✅ 🐛 📊 🔄 💡。

### 0.9 初始 Commit + Tag
```bash
git add -A
git commit -m "chore: project initialization with roadmap and docs"
git push origin main
git tag -a v0.1.0 -m "Release v0.1.0: initial scaffolding"
git push origin v0.1.0
```

## 結束回報

```
模式 A 初始化完成
- ROADMAP 步驟數：80
- Phase 1–6 milestone：已建
- GitHub Issues：已批次建立 N 個
- 初始 tag：v0.1.0
- 下一步：呼叫 auto-iterate 開始 Step 1
```

## 硬規則

- 正體中文
- Mermaid 圖使用標準語法（可 GitHub 直接渲染）
- **嚴禁動 `upstream/**` 內容**
- 完成後自動建議使用者呼叫 `dev-log` skill 寫迭代 #1
