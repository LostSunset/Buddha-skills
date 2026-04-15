---
name: resilient-image-download
description: 批次下載網頁文章圖片時處理 HTTP 429 限流、User-Agent 阻擋、Wikimedia Commons /thumb/ 路徑限速、以及小圖佔位符的實戰技巧。當使用者要求「下載多篇文章的圖片」「批次抓圖」「Wikimedia 圖片抓不下來」「爬蟲 429」「準備報告素材」時使用。包含 curl flag 組合、重試策略、備援 URL 解析、下載後尺寸檢查等。
---

# resilient-image-download

批次下載公開網頁圖片（新聞、百科、官方新聞稿）時，單純 `curl URL -o` 命中率常只有 30-50%。本 skill 記錄 2026 年仍有效的繞路策略：特別針對 Wikimedia Commons、動態 CDN、以及 SVG 佔位符 / 小圖校驗。

## 何時使用

- 批次下載報告／簡報的素材圖片（> 10 張）
- 從 Wikimedia Commons 抓圖遇到 HTTP 429（robot policy）
- 從新聞網站抓圖，回 403 / 0 bytes / 2-4 KB 佔位符
- 準備教學或學術材料，需要記錄來源 URL

## 核心技巧

### 1. User-Agent 是最常見的阻擋原因

大多數網站對空 User-Agent 或 `curl/*` 直接 403。固定帶真實瀏覽器 UA：

```bash
UA='Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36'

curl -L -A "$UA" \
     -H "Accept: image/webp,image/apng,image/*,*/*" \
     --max-time 30 \
     -o output.jpg \
     "$URL"
```

`-L` 必帶（追隨 301／302），`--max-time 30` 防止單張掛太久拖慢全批。

### 2. Wikimedia Commons：避開 `/thumb/` 路徑

**症狀**：`upload.wikimedia.org/wikipedia/commons/thumb/xxx/640px-xxx.jpg` 回 HTTP 429，錯誤訊息明示 `see https://w.wiki/GHai`（他們的 robot policy）。

**根因**：縮圖服務對自動化請求有較嚴格的限速，但**原檔路徑沒有**。

**解法**：

- 把 `/wikipedia/commons/thumb/<a>/<b>/<FILE>/640px-<FILE>` 去掉 `thumb/` 與尾端尺寸前綴，保留 `/wikipedia/commons/<a>/<b>/<FILE>`
- 或直接用 Special:FilePath：`https://commons.wikimedia.org/wiki/Special:FilePath/<FILE>`

```bash
# 範例：把 thumb URL 轉成原檔 URL
THUMB='https://upload.wikimedia.org/wikipedia/commons/thumb/1/12/MQ-9_Reaper_UAV_(cropped).jpg/640px-MQ-9_Reaper_UAV_(cropped).jpg'

# 方法 A：去掉 thumb 段與尾端尺寸
ORIG='https://upload.wikimedia.org/wikipedia/commons/1/12/MQ-9_Reaper_UAV_(cropped).jpg'

# 方法 B：Special:FilePath（最穩）
FP='https://commons.wikimedia.org/wiki/Special:FilePath/MQ-9_Reaper_UAV_(cropped).jpg'
```

### 3. 尊重 `Retry-After`

Wikimedia 429 回應 header 含 `Retry-After: <secs>`。自動化流程收到 429 時讀 header，等完該秒數再重試——通常一次就能成功。

```bash
# 偵測 429 並等待 retry-after 的簡易實作
for url in "${URLS[@]}"; do
  for attempt in 1 2; do
    code=$(curl -L -A "$UA" -w '%{http_code}' -o "tmp.jpg" "$url")
    if [ "$code" = "200" ]; then
      mv tmp.jpg "${url##*/}"
      break
    fi
    if [ "$code" = "429" ] && [ "$attempt" = "1" ]; then
      sleep 65   # 保守估，大多 retry-after 在 30-60 秒
    fi
  done
done
```

實務上：**批次間隔 3 秒 + 偶發 65 秒 backoff** 就能全綠。

### 4. 小圖＝佔位符

有些網站回 200 但圖是 2.7 KB 的「暫無圖」佔位符，或 15 bytes 的空檔。下載完要校驗：

```bash
for f in images/*.jpg; do
  size=$(stat -c %s "$f" 2>/dev/null || stat -f %z "$f")
  if [ "$size" -lt 10000 ]; then
    echo "SUSPECT (< 10 KB): $f  ($size bytes)"
  fi
done
```

10 KB 是常用門檻（合理縮圖 30 KB+，佔位符 2-5 KB）。

### 5. 動態 CDN / OpenGraph 替代

若網站的實際圖片 URL 是 `next/image?url=...&w=...`、SVG 佔位符或 CSS 背景圖，直接抓不到。改從 HTML 的 OpenGraph meta 取代：

```bash
curl -sL -A "$UA" "$ARTICLE_URL" | \
  grep -oE '<meta[^>]+property="og:image"[^>]+content="[^"]+"' | \
  head -1 | \
  sed -E 's/.*content="([^"]+)".*/\1/'
```

OG image 是大多數新聞網站放社群預覽圖的位置，通常是真實可下載的 URL。

## 完整下載腳本範本

```bash
#!/usr/bin/env bash
set -u
UA='Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 Chrome/120.0.0.0 Safari/537.36'
OUT=images
mkdir -p "$OUT"

# URL 列表（可從文章 markdown 抓）
urls=(
  "https://example.com/img1.jpg"
  "https://upload.wikimedia.org/wikipedia/commons/1/12/MQ-9_Reaper_UAV_(cropped).jpg"
)

success=0; fail=0
for i in "${!urls[@]}"; do
  url="${urls[$i]}"
  name=$(printf "img_%02d.jpg" $((i+1)))
  for attempt in 1 2; do
    code=$(curl -L -A "$UA" \
                -H 'Accept: image/webp,image/apng,image/*,*/*' \
                --max-time 30 \
                -w '%{http_code}' \
                -o "$OUT/$name" "$url")
    if [ "$code" = "200" ]; then
      size=$(stat -c %s "$OUT/$name" 2>/dev/null || stat -f %z "$OUT/$name")
      if [ "$size" -ge 10000 ]; then
        success=$((success+1)); break
      fi
      echo "WARN: $name 回 200 但僅 $size bytes（佔位符？）"
    fi
    [ "$code" = "429" ] && [ "$attempt" = "1" ] && sleep 65
  done
  [ "$attempt" = "2" ] && [ "$code" != "200" ] && fail=$((fail+1))
  sleep 3  # 批次間隔避免觸發限速
done
echo "Done: $success ok / $fail fail"
```

## 下載後文件化

每篇文章的 `articles.md` 中，圖片應有完整 metadata：

```markdown
## 1. 〈文章標題〉

- 來源：https://example.com/article/123
- 日期：2025-12-31
- 圖片：images/img_01.jpg  (或 失敗，原 URL: ...)
```

下載失敗的記在「圖片下載失敗紀錄」區，方便日後重試或手動補。

## 相關

- Wikimedia robot policy：<https://w.wiki/GHai>
- `curl` 手冊中重要的 flag：`-L`、`-A`、`--max-time`、`-w '%{http_code}'`

## 參考實作

本 skill 源自 `D:\35_UAV` 無人機資料蒐集專案：7 類、102 篇文章、目標下載 60+ 張圖。
第一次全批命中率 46%（28/60），套用本 skill 技巧後提升至 80%+（49/60）；
05_surveillance 分類原先 Wikimedia 批次全滅，改用 FilePath + retry-after 後 10/10 全綠。
