# 開發工作流設計文件

- **日期**：2026-04-15
- **作者**：LostSunset（lollipopg4ao3@gmail.com）
- **遠端倉庫**：https://github.com/LostSunset/Buddha-skills.git
- **專案根**：`D:\36_Buddha-skills`
- **狀態**：已核准，待實作

---

## 1. 目標

為 `Buddha-skills` 專案建立可自動化、可協作、可追蹤的開發工作流，包含三大任務：

1. **開發日誌系統**：提供 skill 與規則檔，讓本專案（以及 clone 本專案的其他開發者 Claude Code）都能照統一格式記錄開發歷程。
2. **上游參考子模組**：以 `git submodule` 方式引入三個上游倉庫到 `upstream/`，追蹤其 `main` 分支，**嚴禁修改**，保持與原作者完全一致。
3. **每日自動同步工作流**：GitHub Actions 每日拉取上游更新，開 PR 並由 `@claude`（GitHub Claude App）自動審查影響、直接修改本專案程式碼以適配。

## 2. 專案現況

- `D:\36_Buddha-skills` 尚非 git 倉庫
- 現有檔案：`CLAUDE-CODE-自動開發指令.md`、`.omc/`
- GitHub Claude App 已安裝於 `LostSunset/Buddha-skills`

## 3. 最終目錄結構

```
D:\36_Buddha-skills\
├── .claude/
│   └── skills/
│       ├── dev-log/
│       │   └── SKILL.md                    # 開發日誌 skill
│       └── auto-dev-mode/
│           └── SKILL.md                    # 模式 A/B/C/D 判斷執行
├── .github/
│   └── workflows/
│       ├── update-upstream.yml             # 每日同步上游
│       ├── claude-review.yml               # @claude PR 審查
│       └── upstream-guard.yml              # 阻止直接修改 upstream/ 內容
├── upstream/                               # 子模組（唯讀鏡像）
│   ├── README.md                           # 註明「唯讀，禁止修改」
│   ├── anthropic-skills/                   # submodule → main
│   ├── andrej-karpathy-skills/             # submodule → main
│   └── oh-my-claudecode/                   # submodule → main
├── docs/
│   ├── DEV_LOG.md                          # 開發日誌本體
│   ├── DEV_LOG_RULES.md                    # 日誌規則（其他開發者參考）
│   ├── TEAM_ROLES.md                       # 虛擬開發團隊角色
│   ├── ARCHITECTURE.md
│   └── superpowers/specs/                  # 本 spec 所在位置
├── .gitattributes                          # upstream/** linguist-vendored=true
├── .gitignore
├── .gitmodules
├── CLAUDE.md                               # 開發規範（引用 DEV_LOG_RULES.md）
├── CLAUDE-CODE-自動開發指令.md             # 既有檔案，保留
├── MEMORY.md
├── ROADMAP.md
└── README.md
```

## 4. 任務一：開發日誌系統

### 4.1 `docs/DEV_LOG_RULES.md`（規則檔）

給「其他開發者的 Claude Code」讀取遵守的規則。用正體中文撰寫，包含：

- **時間戳格式**：`YYYY-MM-DD HH:mm`（本地時區 UTC+8）
- **迭代編號**：單調遞增，從 #1 開始
- **必填欄位**：本次目標、完成項目、發現問題、測試結果、下次目標、技術筆記
- **六項必做更新**（引用 `CLAUDE-CODE-自動開發指令.md` 的 B.6）：
  ① Commit & 推送 ② 更新版本與 README ③ 發佈 Release ④ 更新 CLAUDE/MEMORY ⑤ 更新 ROADMAP ⑥ 更新開發日誌
- **commit 關聯**：日誌項目須附 commit SHA 前 7 碼
- **上游同步紀錄**：由 workflow 自動產生的迭代，`作者` 欄位填 `auto/upstream-sync`

### 4.2 `.claude/skills/dev-log/SKILL.md`

YAML frontmatter：

```yaml
---
name: dev-log
description: 用於在 Buddha-skills 專案中撰寫或更新開發日誌。當完成一個步驟、準備 commit、建立 PR、合併上游更新、或使用者要求「寫開發日誌／記錄迭代」時使用。遵循 docs/DEV_LOG_RULES.md 規則與 CLAUDE-CODE-自動開發指令.md 的 Phase 0.8 模板。
---
```

內容章節：
1. 規則來源指引（指向 `docs/DEV_LOG_RULES.md`）
2. 寫入步驟：讀取 `docs/DEV_LOG.md` → 判斷下一個迭代編號 → 依模板 append → 不覆寫既有紀錄
3. 模板（Phase 0.8 的七欄位）
4. 範例（一則正體中文迭代紀錄）

### 4.3 `.claude/skills/auto-dev-mode/SKILL.md`

依 `CLAUDE-CODE-自動開發指令.md` 的模式偵測邏輯包裝成 skill，觸發於使用者要求「執行自動開發迭代」或被 cron/workflow 喚醒時。

## 5. 任務二：upstream 子模組

### 5.1 新增指令

```bash
git submodule add https://github.com/anthropics/skills upstream/anthropic-skills
git submodule add https://github.com/forrestchang/andrej-karpathy-skills upstream/andrej-karpathy-skills
git submodule add https://github.com/yeachan-heo/oh-my-claudecode upstream/oh-my-claudecode
```

### 5.2 `.gitmodules` 設定

所有子模組皆加上：

```
[submodule "upstream/..."]
    path = upstream/...
    url = https://github.com/.../...
    branch = main
    update = merge
```

### 5.3 `.gitattributes`

```
upstream/** linguist-vendored=true
upstream/** linguist-generated=true
```

### 5.4 `upstream/README.md`

正體中文說明：
- 這三個子模組為**上游參考鏡像**
- **禁止任何修改**，必須與原作者 `main` 分支一致
- 更新途徑：每日自動 workflow，或手動 `git submodule update --remote --merge`

### 5.5 防改動 guard workflow（`upstream-guard.yml`）

觸發於 PR：
- 檢查 diff 若包含 `upstream/<name>/**` 的**內容變更**（非 submodule pointer 變更）→ `exit 1`
- 只允許 `.gitmodules` 與 submodule commit SHA 的變更
- 例外：由 `github-actions[bot]` 或 `update-upstream` workflow 產生的 PR 可通過

## 6. 任務三：每日自動同步工作流

### 6.1 `.github/workflows/update-upstream.yml`

```yaml
name: Daily Upstream Sync
on:
  schedule:
    - cron: '0 18 * * *'   # 每日 UTC 18:00 ≒ 台北 02:00
  workflow_dispatch:

permissions:
  contents: write
  pull-requests: write

jobs:
  sync:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          submodules: recursive
          fetch-depth: 0
          token: ${{ secrets.PAT_TOKEN }}

      - name: Update submodules
        id: update
        run: |
          git submodule update --remote --merge
          if git diff --quiet; then
            echo "changed=false" >> $GITHUB_OUTPUT
          else
            echo "changed=true" >> $GITHUB_OUTPUT
          fi

      - name: Generate diff summary
        if: steps.update.outputs.changed == 'true'
        run: |
          DATE=$(date -u +%Y-%m-%d)
          echo "DATE=$DATE" >> $GITHUB_ENV
          {
            echo "## 上游更新摘要（$DATE）"
            for sub in upstream/*/; do
              name=$(basename "$sub")
              echo ""
              echo "### $name"
              git -C "$sub" log --oneline -20 HEAD@{1}..HEAD 2>/dev/null || echo "(無新 commit)"
            done
          } > upstream-diff.md

      - name: Create Pull Request
        if: steps.update.outputs.changed == 'true'
        uses: peter-evans/create-pull-request@v6
        with:
          token: ${{ secrets.PAT_TOKEN }}
          branch: chore/upstream-update-${{ env.DATE }}
          title: "chore(upstream): 每日同步 ${{ env.DATE }}"
          body-path: upstream-diff.md
          commit-message: "chore(upstream): daily sync ${{ env.DATE }}"
          labels: upstream-sync, claude-review-requested
```

建立 PR 時，body 於檔案末尾 append 一段 `@claude` 指令：

```
---

@claude 請審查本 PR 的上游變更，檢查是否影響下列路徑的功能：
- `.claude/skills/**`
- `docs/**`（非 `docs/superpowers/specs/`）
- 根目錄 `CLAUDE.md`、`MEMORY.md`、`ROADMAP.md`

若有影響請直接 push commit 到本 PR 分支修正。若無影響，留言「No impact」即可。完成後依 `docs/DEV_LOG_RULES.md` 寫入開發日誌。
```

### 6.2 `.github/workflows/claude-review.yml`

由 `anthropics/claude-code-action@v1`（官方 GitHub App action）驅動，觸發於 `issue_comment` 與 PR event 含 `@claude`：

- 允許工具：`Bash(git*)`, `Read`, `Write`, `Edit`, `Grep`, `Glob`
- `ANTHROPIC_API_KEY` 由 repo secret 提供
- 允許 push commit 至 PR 分支（權限：`contents: write`, `pull-requests: write`）
- 自動簽名：`Co-Authored-By: Claude <noreply@anthropic.com>`

### 6.3 所需 secret

- `ANTHROPIC_API_KEY`
- `PAT_TOKEN`：Personal Access Token（需 `repo`、`workflow` 權限）——因為 `GITHUB_TOKEN` 建立的 PR 不會觸發其他 workflow（包括 `@claude` 審查）

## 7. 實作 PR 分批

| # | PR 標題 | 主要內容 |
|---|---------|----------|
| 1 | `chore: init repository` | `git init`、連 remote、`.gitignore`、初版 `README.md` |
| 2 | `docs: governance structure` | `CLAUDE.md`、`MEMORY.md`、`ROADMAP.md`、`docs/TEAM_ROLES.md`、`docs/DEV_LOG.md`、`docs/DEV_LOG_RULES.md` |
| 3 | `feat: dev-log & auto-dev-mode skills` | `.claude/skills/dev-log/`、`.claude/skills/auto-dev-mode/` |
| 4 | `chore: add upstream submodules` | 三個子模組、`upstream/README.md`、`.gitattributes`、`upstream-guard.yml` |
| 5 | `ci: daily upstream sync & claude review` | `update-upstream.yml`、`claude-review.yml` |

## 8. 風險與對策

| 風險 | 對策 |
|------|------|
| 上游 breaking change 連鎖破壞本專案 skill | `@claude` PR 審查；若失敗人工介入前 PR 不 merge |
| `@claude` 無限循環觸發 | claude-review workflow 過濾掉由 `github-actions[bot]` 發起的事件 |
| 子模組被意外修改 | `upstream-guard.yml` PR 層阻擋 |
| PAT 洩漏 | 使用 fine-grained PAT，只授權本 repo |
| 每日同步造成 PR 堆積 | 若已有未 merge 的 `chore/upstream-update-*` 分支，則覆蓋更新而非新建 |

## 9. 驗收條件

- [ ] 本地可執行 `git submodule update --remote --merge` 並看到更新
- [ ] `upstream-guard.yml` 能阻擋修改 `upstream/` 內容的 PR
- [ ] `update-upstream.yml` 可用 `workflow_dispatch` 手動觸發成功
- [ ] 手動觸發一次後，PR 被建立並觸發 `@claude` 回應
- [ ] `dev-log` skill 能被 Claude Code 正確辨識並觸發
- [ ] 其他開發者 clone 後，其 Claude Code 讀 `CLAUDE.md` 即能套用 `DEV_LOG_RULES.md`
- [ ] 全部 5 個 PR 皆合併入 `main`，並打上 `v0.1.0` tag
