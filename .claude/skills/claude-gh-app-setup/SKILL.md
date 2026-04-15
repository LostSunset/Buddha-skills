---
name: claude-gh-app-setup
description: 在一個 GitHub repo 上完整設定 Claude Code Action，讓 @claude 在 Issue/PR 能用 Claude Max 訂閱（OAuth，不用付費 API key）自動回應，並以 claude[bot] 身份貼文（不是 github-actions[bot]）。當使用者說「在 repo 加上 @claude」「我用 Claude Max 訂閱怎麼接 GitHub」「@claude 回覆者變成 github-actions[bot] 不好看」「裝 Claude GitHub App」「workflow 要怎麼寫」「branch protection 怎麼設」時使用。
---

# claude-gh-app-setup

讓一個 GitHub repo 支援 `@claude` mention 觸發 Claude Code Action，用 Claude Max 訂閱的 OAuth 認證，並以 `claude[bot]` 身份回覆。

## 前置條件

- 使用者有 Claude Max 或 Team 訂閱（個人版 Pro 也可）
- Repo 已存在（public / private 皆可）
- 本地已裝 `gh` CLI 並 `gh auth login` 過
- 本地已裝 `claude` CLI（Claude Code）

## 完整流程

### Step 1 — 裝 Claude GitHub App

使用者在自己的終端機執行：

```bash
claude /install-github-app
```

流程：
1. 開瀏覽器到 GitHub App 授權頁
2. 選要授權的 repo（建議只選需要的那個，不要給 all repos）
3. 同意 permissions（Issues write、PR write、Contents write）
4. 回到 CLI，自動在該 repo 建立 **`CLAUDE_CODE_OAUTH_TOKEN`** repo secret

驗證：
```bash
gh api "repos/<owner>/<repo>/actions/secrets" -q '.secrets[].name' | grep CLAUDE_CODE_OAUTH_TOKEN
```
應輸出 `CLAUDE_CODE_OAUTH_TOKEN`。

### Step 2 — 寫 `.github/workflows/claude.yml`

```yaml
name: Claude Code

on:
  issue_comment:
    types: [created]
  pull_request_review_comment:
    types: [created]
  issues:
    types: [opened, assigned]
  pull_request_review:
    types: [submitted]

permissions:
  contents: write
  pull-requests: write
  issues: write
  id-token: write

jobs:
  claude:
    if: |
      (github.event_name == 'issue_comment' && contains(github.event.comment.body, '@claude')) ||
      (github.event_name == 'pull_request_review_comment' && contains(github.event.comment.body, '@claude')) ||
      (github.event_name == 'pull_request_review' && contains(github.event.review.body, '@claude')) ||
      (github.event_name == 'issues' && (contains(github.event.issue.body, '@claude') || contains(github.event.issue.title, '@claude')))
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: Run Claude Code
        uses: anthropics/claude-code-action@v1
        with:
          # 不傳 github_token！action 會用 Claude GitHub App 的 installation
          # token 發文，身份就是 claude[bot]。若傳 github_token 會變成
          # github-actions[bot]（Actions 內建身份）。
          claude_code_oauth_token: ${{ secrets.CLAUDE_CODE_OAUTH_TOKEN }}
```

### Step 3 — 啟用 branch protection（可選但建議）

```bash
cat > .tmp-protection.json <<'EOF'
{
  "required_status_checks": {
    "strict": true,
    "contexts": ["your-ci-check-name"]
  },
  "enforce_admins": false,
  "required_pull_request_reviews": {
    "required_approving_review_count": 0
  },
  "restrictions": null,
  "allow_force_pushes": false,
  "allow_deletions": false
}
EOF

gh api -X PUT repos/<owner>/<repo>/branches/main/protection --input .tmp-protection.json
rm .tmp-protection.json
```

### Step 4 — 驗證 bot 身份

```bash
# 開測試 issue
gh issue create --title "Test: @claude" --body "@claude 請回覆 ack"

# 等 workflow 跑完（通常 30s–1m）
until gh run list --workflow=claude.yml --limit 1 --json status -q '.[0].status' \
    | grep -q completed; do sleep 10; done

# 看留言作者
gh api "repos/<owner>/<repo>/issues/<issue-num>/comments" \
    -q '.[] | {login:.user.login, type:.user.type}'
```

期望：
```
{"login":"claude[bot]","type":"Bot"}
```

若看到 `{"login":"github-actions[bot]","type":"Bot"}` → 檢查 workflow 是否誤傳 `github_token`。

## 硬規則

- **`github_token` 在 `anthropics/claude-code-action` 的 `with:` 裡絕對不要傳**。要傳也只能是 App-minted token（`tibdex/github-app-token` 等），不能是 `${{ secrets.GITHUB_TOKEN }}`。
- **不要同時設 `anthropic_api_key` 和 `claude_code_oauth_token`**：兩者擇一。Max 訂閱用 OAuth。
- **branch protection 的 `contexts` 要與 CI 的 job id 完全一致**，否則 check 名稱對不上，protection 永遠 block。
- **OAuth token 是 repo-level secret**：換 repo 要重新 `claude /install-github-app`，或改存到 org-level secret 讓多 repo 共用。

## 常見陷阱

- **用個人 PAT 當 github_token 結果身份是 `LostSunset` 自己**：action 當成使用者操作，混淆歷史。
- **workflow trigger 不含 `issues` event**：新開 issue 內含 `@claude` 不會觸發（只回應 comment）。
- **`permissions: id-token: write` 忘記加**：OAuth flow 需要 OIDC，少這行會失敗。
- **把 Claude GitHub App 裝到整個 org 但只選 public repo**：private repo 不會回應。重裝時確認勾選。
- **branch protection 設了 `strict: true` 但 CI 跑得慢**：PR rebase 後要重跑 CI 才能 merge。改 `strict: false` 較寬鬆。

## 驗證清單

- [ ] `CLAUDE_CODE_OAUTH_TOKEN` secret 存在
- [ ] `.github/workflows/claude.yml` 有 `id-token: write` permission
- [ ] workflow `with:` block 只有 `claude_code_oauth_token`，沒有 `github_token`
- [ ] 開測試 issue `@claude` 後 workflow 觸發
- [ ] 回覆 comment 作者 = `claude[bot]`（Bot type）
- [ ] Branch protection 已啟用（若需要）

## 延伸

- **自訂 trigger words**：workflow 的 `if:` 條件改成 `@myclaude` 或其他。
- **限制回應長度 / model**：action 的 `max_turns`、`model` 等參數可設。
- **Scheduled 觸發**：加 `schedule: - cron` 讓 Claude 定時做任務（搭配 `@claude` 在某個 tracking issue）。
