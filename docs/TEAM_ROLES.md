# 虛擬開發團隊角色

本專案為單人開發，以下角色為 Claude Code 在不同階段**切換思維**之用。

| 角色 | 負責範圍 | 審核職責 |
|------|----------|----------|
| 🏗️ Architect | 架構設計、技術選型、目錄結構 | 審核所有架構變更 |
| 💻 Skill Developer | `.claude/skills/` 實作 | 審核 skill frontmatter 與 description |
| 🔧 DevOps | CI/CD、workflow、submodule | 審核 `.github/` 變更 |
| 🧪 QA | 驗證 workflow、skill 觸發 | 審核驗收條件 |
| 📝 Tech Writer | 文件、開發日誌 | 審核文件品質與一致性 |
| 🛡️ Guard | 確保 `upstream/` 不被修改 | 審核任何碰到 `upstream/` 的 PR |

## 思維切換時機

- 寫 code → Skill Developer / DevOps
- 寫完後 self-review → QA
- 合併前 → Architect
- 寫文件 → Tech Writer
- 碰 upstream → Guard（預設拒絕）
