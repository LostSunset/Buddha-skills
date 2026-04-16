---
name: claude-pr-automerge
description: 讓 @claude 在 GitHub Actions workflow 中自動合併 PR，而不只是留言審查。當使用者說「@claude 合併後說沒有權限」「@claude 不會自動 merge」「想讓 claude 自動合併 upstream sync PR」「@claude 只留言不 merge」「讓 bot 自動按 merge」「workflow 自動 merge」時使用。
---

# claude-pr-automerge

讓 @claude 在完成審查後**直接呼叫 `gh pr merge` 合併 PR**，而不是留言請人工合併。

## 問題根因

@claude 說「合併 PR 超出我的操作權限」不是技術限制，而是**沒有收到合併指令**。  
`GITHUB_TOKEN` 在 GitHub Actions 環境中永遠存在，`gh pr merge` 技術上一直可以執行。

## 核心技巧

在觸發 @claude 的 PR body 或 comment 裡**明確寫出 merge 指令**：

```
若無影響，使用 `dev-log` skill 寫入 DEV_LOG 後，執行以下指令直接合併本 PR：

```bash
gh pr merge <BRANCH_NAME> --merge --repo <owner>/<repo>
```

**不需要留言，寫完 dev-log 後直接合併即可。**
```

## 為什麼要指定分支名，不用 `gh pr merge --merge`（無參數）

| 觸發方式 | checkout ref | `gh pr merge --merge` 能否偵測 PR |
|----------|-------------|----------------------------------|
| `pull_request` event | PR merge commit | ✅ 可以（`GITHUB_REF` 含 PR 號） |
| `issue_comment` event | `main`（預設分支） | ❌ 偵測不到 PR，會出錯 |

大多數 @claude 觸發都是透過 `issue_comment`（在 PR 上留言），checkout 是 main。  
**解法**：在 PR body 生成時（日期已知）直接把分支名寫死：

```bash
# 在生成 PR body 的 workflow step 裡（$DATE 已知）
echo "gh pr merge chore/upstream-update-$DATE --merge --repo owner/repo"
```

這樣 PR body 裡會有具體的分支名，@claude 可以直接複製執行。

## 完整範例：upstream 同步 PR 自動合併

### `update-upstream.yml`（生成 PR body 的 step）

```bash
DATE=$(date -u +%Y-%m-%d)
# ...
{
  echo "@claude 請審查本 PR 的上游變更，檢查是否影響以下路徑："
  echo "- \`.claude/skills/**\`"
  echo "- 根目錄治理文件"
  echo ""
  echo "若有影響請直接 push commit 修正，修正後再合併。"
  echo "若無影響，使用 \`dev-log\` skill 寫入 \`docs/DEV_LOG.md\` 後，執行："
  echo ""
  echo "\`\`\`bash"
  echo "gh pr merge chore/upstream-update-$DATE --merge --repo LostSunset/Buddha-skills"
  echo "\`\`\`"
  echo ""
  echo "**不需要留言，寫完 dev-log 後直接合併即可。**"
} > /tmp/pr-body.md
```

### `claude-review.yml`（讓 @claude 能執行 gh 指令）

```yaml
permissions:
  contents: write
  pull-requests: write
  issues: write
  id-token: write

jobs:
  claude:
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: Run Claude Code
        uses: anthropics/claude-code-action@v1
        with:
          claude_code_oauth_token: ${{ secrets.CLAUDE_CODE_OAUTH_TOKEN }}
          trigger_phrase: "@claude"
          claude_args: "--allowed-tools Bash,Read,Write,Edit,Grep,Glob"
          # 不傳 github_token → 身份為 claude[bot]
          # GITHUB_TOKEN 仍在環境中，gh pr merge 可以正常執行
```

## 驗證

```bash
# 觸發後確認 PR 已合併（state 應為 MERGED）
gh pr view <branch-name> --repo <owner>/<repo> --json state -q '.state'
```

## 硬規則

- **PR body 裡必須有明確的 `gh pr merge` 指令**，@claude 不會自己判斷要合併
- **分支名要在生成 PR body 的 step 裡寫死**，不能用 `${{ github.event.pull_request.number }}` （那時 PR 還沒建立）
- **`claude-review.yml` 的 `permissions` 要有 `pull-requests: write`**，否則 merge 會被 403 擋下
- **不傳 `github_token`** 給 `claude-code-action`（保持 `claude[bot]` 身份）；`GITHUB_TOKEN` 環境變數由 Actions runner 自動提供，`gh` CLI 會用它

## 常見陷阱

- **`gh pr merge --merge`（無參數）在 `issue_comment` 觸發時失敗**：checkout 是 main，偵測不到 PR。改用分支名。
- **`gh pr merge` 被 branch protection 擋下**：需要通過所有 required status checks。可把 @claude 的 review job 加入 required checks，或改用 `--admin` bypass（謹慎使用）。
- **PR body 裡的指令格式不對**：指令要在 fenced code block（\`\`\`bash）裡，@claude 才能正確識別並執行。

## 延伸

- **有影響時也要合併**：把 merge 指令移到「push 修正後」的說明裡，讓 @claude push commit 再合併。
- **要求 @claude 先 approve 再 merge**：加 `gh pr review --approve` 後接 `gh pr merge`（需 `pull-requests: write`）。
- **多 repo 共用**：把分支名 pattern 和 repo 路徑抽成 workflow 變數，複製到其他 repo 只需改這兩個值。
