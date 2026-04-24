---
name: upstream-search
description: 跨六個 upstream submodule（anthropic-skills、andrej-karpathy-skills、oh-my-claudecode、claude-scholar、google-skills、cloudflare-skills）搜尋 skill 範例、寫法慣例、frontmatter 模式、工具用法。當使用者問「upstream 有沒有類似的 skill」「怎麼寫 XX 風格的 skill」「參考 anthropic / karpathy / omc / scholar / google / cloudflare 怎麼做 XX」「找 upstream 範例」「學術 / 論文 / research skill 範例」「GCP / Google Cloud / BigQuery / Cloud Run / GKE skill 範例」「Cloudflare / Workers / Agents SDK / Durable Objects / Wrangler / MCP skill 範例」時使用。只讀搜尋，不修改 upstream 內容。
---

# upstream-search

在 `upstream/` 下六個子模組中找 skill 與寫法範例。

## 六個來源定位

| Submodule | 特色 | 適合找什麼 |
|-----------|------|------------|
| `anthropic-skills/` | 官方出品、規範最嚴謹 | frontmatter 格式、描述撰寫範例、skill 結構標準 |
| `andrej-karpathy-skills/` | 教學導向 | 教育性 prompt、步驟展示、學習型 skill |
| `oh-my-claudecode/` | 社群生態、整合豐富 | agent/team 整合、MCP、pipeline、tmux/worktree 模式 |
| `claude-scholar/` | 學術研究向（50 skills + 15 agents） | 論文寫作、citation、LaTeX、Obsidian、Kaggle、ML 研究工作流 |
| `google-skills/` | Google Cloud 官方（13 skills，Apache 2.0） | GCP 產品（BigQuery、Cloud Run、GKE、Firebase、AlloyDB、Cloud SQL）、Well-Architected Framework、Cloud Recipe 寫法 |
| `cloudflare-skills/` | Cloudflare 官方（8 skills + 2 commands + 5 MCP servers） | Workers、Agents SDK、Durable Objects、Sandbox SDK、Wrangler CLI、web-perf（Core Web Vitals）、build-agent/build-mcp 斜線指令、遠端 MCP 設定 |

## 搜尋範本

### 找 frontmatter 與 description 寫法

```bash
grep -rn "^description:" upstream/anthropic-skills/ --include="SKILL.md" | head -20
```

### 找特定主題

```bash
# 找 PDF 處理類
grep -rln -i "pdf" upstream/ --include="SKILL.md"

# 找 MCP 設定
grep -rln "mcp" upstream/ --include="*.md" -l
```

### 找 skill 結構範例（章節分佈）

```bash
for f in upstream/anthropic-skills/**/SKILL.md; do
  echo "=== $f ==="
  grep '^##' "$f"
done
```

### 跨 repo 統計 skill 數量

```bash
find upstream -name "SKILL.md" | awk -F/ '{print $2}' | sort | uniq -c
```

## 回應格式

對使用者的提問，輸出：

1. **來源分類**：每個匹配屬於哪個 submodule
2. **路徑 + 簡述**：`upstream/<name>/<path>` — 一行摘要
3. **關鍵片段**：必要時節錄 5–15 行程式碼或 markdown
4. **建議套用**：適合本專案的是哪幾個？可直接拿來參考 frontmatter 還是整個結構？

## 硬規則

- **只讀**：絕不 Edit/Write `upstream/**` 任何檔案
- 若使用者明確要求「搬一份到本地」 → 建立 `.claude/skills/<new-name>/` **重寫**（可引用但不複製，原作者 LICENSE 以 submodule 為準）
- 若找不到相符內容 → 明確告知，不要杜撰
