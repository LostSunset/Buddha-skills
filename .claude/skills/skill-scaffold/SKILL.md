---
name: skill-scaffold
description: 快速生成本專案新 skill 的骨架，依 upstream/anthropic-skills 的官方格式為模板。當使用者說「建立一個新 skill」「scaffold skill XX」「幫我做 XX skill 的骨架」「新增 skill 叫 XX」時使用。會產生 `.claude/skills/<name>/SKILL.md`、檢查 description 觸發力、記錄到 ROADMAP 與 DEV_LOG。
---

# skill-scaffold

生成本專案 `.claude/skills/<name>/SKILL.md` 骨架。

## 輸入需求

呼叫時需確認：

1. **skill 名稱**（kebab-case，全小寫）：例 `pptx-visual-qa`
2. **一句話目的**：這個 skill 解什麼問題
3. **觸發情境**（3–5 個使用者可能的說法）：例「做 PPTX 視覺檢查」「簡報版面 QA」
4. **主要步驟**（3–7 條）
5. **硬規則**（必做 / 禁做，各 1–3 條）

若使用者沒給齊 → 逐項問，每次問一項，不要一次拋整張表。

## 模板

```markdown
---
name: <kebab-name>
description: <一句話目的>。當使用者說「<觸發 1>」「<觸發 2>」「<觸發 3>」時使用。<補充條件>。
---

# <kebab-name>

<1–2 句話說明此 skill 的作用與邊界>

## 輸入 / 前置條件

- <必備工具或環境>
- <必備檔案或狀態>

## 步驟

1. <動作 1>
2. <動作 2>
3. <動作 3>

## 範例指令 / 片段

（若有可直接執行的 bash 或 code snippet 放這裡）

## 硬規則

- <必做 1>
- <禁做 1>

## 輸出格式

<完成後應回報給使用者的格式>
```

## 生成流程

1. 建立資料夾：`.claude/skills/<name>/`
2. 寫入 `SKILL.md`（套上面模板，填入使用者回答）
3. **description 觸發力檢查**：
   - 是否含 3+ 個具體觸發短語（使用者實際會說的話）？
   - 是否避免「always use」「might be useful」這類模糊措辭？
   - 長度：80–220 字（太短不夠觸發、太長吃掉 context）
4. **前後對照 upstream**：
   - 參考 `upstream/anthropic-skills/` 內至少一個相近主題的 SKILL.md
   - 呼叫 `upstream-search` skill 查 frontmatter 寫法
5. 更新 `ROADMAP.md`：在 Phase 2+ append 一個 Step 標記 ✅（或 🔨）
6. 呼叫 `dev-log` skill append 迭代紀錄

## 檢查清單

- [ ] 名稱 kebab-case、全小寫
- [ ] frontmatter 前後各一行 `---`
- [ ] description 含 3+ 觸發短語
- [ ] 至少一條硬規則
- [ ] 檔名為 `SKILL.md`（大寫 + 副檔名 `.md`）
- [ ] 與 upstream 慣例不衝突

## 硬規則

- 絕不覆寫已存在的 `.claude/skills/<name>/SKILL.md`；若衝突 → 停下問使用者
- 正體中文；description 本身用中文寫觸發短語
- 不動 `upstream/**`
