---
name: maintenance-patrol
description: 執行 CLAUDE-CODE-自動開發指令.md 模式 C 的巡檢與持續改善流程。當使用者說「跑維護模式」「mode C 巡檢」「依賴審計」「安全掃描」「技術債清理」「專案已完成要持續維護」時使用。前置條件：ROADMAP.md 所有步驟皆為 ✅。執行 C.1 巡檢、C.2 持續改善、C.3 版本管理（依需要發 patch/minor release）。
---

# maintenance-patrol

專案進入維護期後的例行巡檢與改善。

## 前置條件

```bash
# ROADMAP 所有步驟皆 ✅，無 🔲 / 🔨
test -f ROADMAP.md && ! grep -qE "🔲|🔨" ROADMAP.md
```

若仍有未完成步驟 → 停止並改用 `auto-iterate` 處理。

## C.1 每小時巡檢

### 測試套件
```bash
npm test 2>&1 | tail -5   # 或 pytest / cargo test
```
- 通過 → 記錄
- 失敗 → 建 `fix/patrol-YYYYMMDD-<slug>` 分支修正

### 依賴安全
```bash
npm audit                     # Node
pip-audit                     # Python
cargo audit                   # Rust
```
有高危漏洞 → 立即 patch。

### 程式碼品質
- Linter：`eslint . / ruff . / clippy`
- 靜態分析：`mypy / tsc --noEmit`
- 記錄新增的 warning 數

### GitHub Issues
- 掃 `bug` / `security` label
- 新 Issue → 建對應 `fix/` 或 `enhancement/` 分支

## C.2 持續改善

### 覆蓋率補強
```bash
# 取得覆蓋率報告後挑最低的 3 個模組補測試
npm test -- --coverage | grep -E "^.*\s+[0-9]+\." | sort -k2 -n | head -3
```

### 效能優化
1. Profile：找出最慢的 call path
2. 優化 → 附 benchmark before/after
3. 避免過度優化非熱點

### 重構技術債
- 掃 `TODO|FIXME|HACK|XXX` 統計
- 一次只處理一個，配測試保護

### 文件更新
- README 功能清單對齊實作
- API docs 對齊介面
- 注釋密度過低的檔案補文件

## C.3 版本管理

| 變更類型 | 版本號 |
|---------|--------|
| 安全修復 / bug fix | patch（v0.X.**Y+1**） |
| 新功能 / enhancement | minor（v0.**X+1**.0） |
| 破壞性變更 | major（v**X+1**.0.0） |

```bash
VERSION="vX.Y.Z"
git tag -a "$VERSION" -m "Release $VERSION: <摘要>"
git push origin "$VERSION"
gh release create "$VERSION" --title "$VERSION" --notes "..."
```

## 六項必做更新

每輪巡檢結束均執行（見 `CLAUDE.md`）：
1. Commit & push
2. 版本與 README 更新
3. Release（若版本異動）
4. CLAUDE.md / MEMORY.md
5. ROADMAP（補 Phase X+ 維護項目）
6. DEV_LOG 迭代紀錄

## 結束回報

```
維護巡檢完成
- 測試：X pass / Y fail
- 依賴漏洞：N 個（修復 M）
- Lint warning：±K
- 技術債：清除 T 個 TODO
- 發佈：vX.Y.Z（若有）
- 下次巡檢：<建議時間>
```

## 硬規則

- 絕不為了讓 test 通過而刪 test
- 絕不 `--no-verify` 跳過 hook
- 依賴大版本更新需開獨立 PR，不與常規巡檢混在一起
- 不動 `upstream/**`
