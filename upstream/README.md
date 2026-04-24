# upstream/

> ⚠️ **唯讀鏡像，禁止修改**

本目錄為五個上游專案的 git submodule 鏡像，用途是作為本專案的參考資源。

| 目錄 | 上游倉庫 | 追蹤分支 |
|------|----------|----------|
| `anthropic-skills/` | [anthropics/skills](https://github.com/anthropics/skills) | `main` |
| `andrej-karpathy-skills/` | [forrestchang/andrej-karpathy-skills](https://github.com/forrestchang/andrej-karpathy-skills) | `main` |
| `oh-my-claudecode/` | [yeachan-heo/oh-my-claudecode](https://github.com/yeachan-heo/oh-my-claudecode) | `main` |
| `claude-scholar/` | [Galaxy-Dawn/claude-scholar](https://github.com/Galaxy-Dawn/claude-scholar) | `main` |
| `google-skills/` | [google/skills](https://github.com/google/skills) | `main` |

## 規則

1. **絕不修改任何 `upstream/<name>/**` 內的檔案**。內容必須與原作者完全一致。
2. 更新唯一途徑：
   - 自動：`.github/workflows/update-upstream.yml` 每日 UTC 18:00 執行
   - 手動：`git submodule update --remote --merge`
3. 任何 PR 若修改 `upstream/<name>/**` 內容（非 submodule pointer），會被 `.github/workflows/upstream-guard.yml` 阻擋。
4. 原作者授權與 LICENSE 以各子專案為準，本倉庫不重製授權。

## 初次 clone

```bash
git clone --recurse-submodules https://github.com/LostSunset/Buddha-skills.git
```

已 clone 者：

```bash
git submodule update --init --recursive
```
