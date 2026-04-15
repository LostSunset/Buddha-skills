---
name: pptx-visual-qa
description: 使用 LibreOffice + PyMuPDF 把 PPTX 轉成 PNG 以便視覺檢查版面、覆蓋、斷句、切齊與字數溢位。當使用者要求「檢查簡報排版」「把 pptx 轉成圖片 preview」「視覺 QA 投影片」「每頁截圖比對」，或在用 python-pptx / pptxgenjs 產生簡報後需要逐頁驗證時使用。提供完整「產生 → 渲染 → 檢視 → 修正」迭代迴圈。
---

# pptx-visual-qa

PPTX 無法在終端直接檢視，產生時 python-pptx / pptxgenjs 沒有所見即所得；字級、換行、覆蓋、溢位只能靠真的渲染後用眼睛看。本 skill 建立一條穩定的「把簡報每一頁變成 PNG 然後用 Read 工具看圖」的工作流。

## 何時使用

- 使用者說「產生 ppt 後要你自己檢查排版」「視覺驗證簡報」「逐頁 QA」「截圖 preview」
- 用 python-pptx / pptxgenjs 建立簡報後，要確認：
  - 大字在窄卡片內是否換行撞到標籤
  - 文字方塊是否超出投影片邊界
  - 圖片是否與說明對上、圖說國家／機種是否錯配
  - 頁碼、頁尾是否正確
- CJK 內容（中文／日文）特別需要此流程——英文字的寬度估算與中文全形字差很大

## 前置需求

| 工具 | 用途 | 安裝 |
|------|------|------|
| LibreOffice（`soffice`） | PPTX → PDF | Windows：`winget install TheDocumentFoundation.LibreOffice`｜macOS：`brew install --cask libreoffice`｜Linux：`apt install libreoffice` |
| PyMuPDF（`fitz`） | PDF → PNG | `pip install PyMuPDF` |
| Pillow | 圖片處理（常與 python-pptx 一起用） | `pip install Pillow` |

Windows LibreOffice 預設路徑：`C:/Program Files/LibreOffice/program/soffice.exe`

## 步驟

### 1. 產生 PPTX

略（視需求，python-pptx 或 pptxgenjs 皆可）。

### 2. 轉 PDF（LibreOffice headless）

```bash
# Windows
"C:/Program Files/LibreOffice/program/soffice.exe" --headless --convert-to pdf output.pptx

# macOS / Linux
soffice --headless --convert-to pdf output.pptx
```

預設輸出到當前工作目錄。若需指定目錄：加上 `--outdir path/`。

### 3. PDF → PNG（PyMuPDF）

`render_slides.py`：

```python
import fitz
from pathlib import Path

pdf = fitz.open("output.pdf")
out = Path("preview")
out.mkdir(exist_ok=True)
for i, page in enumerate(pdf, start=1):
    pix = page.get_pixmap(dpi=110)          # 110 dpi 在 Claude Read 視覺品質與檔案大小上平衡較好
    pix.save(str(out / f"slide_{i:02d}.png"))
print(f"rendered {len(pdf)} pages to {out}")
```

執行：`python render_slides.py`

### 4. 用 Read 工具檢視

```
Read file_path=/abs/path/preview/slide_01.png
Read file_path=/abs/path/preview/slide_02.png
...
```

可在單一回合並行讀多頁以加速。**務必使用絕對路徑**。

### 5. 列出問題

檢查清單（依重要性排序）：

1. **覆蓋 / 重疊**：大字穿越到下一行、圖片蓋到文字、裝飾線穿過字
2. **溢位 / 截斷**：文字框被邊界切掉、項目數過多撐破卡片
3. **斷句 / 換行**：CJK 大字是否因卡片太窄而換行撞到下方標籤（最常見的 bug）
4. **對齊 / 切齊**：欄位左緣沒對齊、卡片間距不均、頁尾與內容貼太近（< 0.3"）
5. **字數過多**：一個項目塞太多字，或副標文字過長換行 3 行以上
6. **國家／機種錯配**：圖說宣告的物件與圖片實際內容不符（特別是共用圖片資料夾的情境）
7. **頁碼 / 頁尾**：實際總頁數與 hard-coded `TOTAL` 是否一致

### 6. 修正後重跑

只要修改 build 腳本再次跑：

```bash
python build_ppt.py && \
soffice --headless --convert-to pdf output.pptx && \
python render_slides.py
```

然後回到步驟 4 重讀受影響頁面。**不要只重讀被你修正的頁**——一個修正常常連帶影響上下頁。

## 常見坑

### MuPDF warning: No common ancestor in structure tree

無害警告，PNG 仍會正確產出。LibreOffice 產生的 PDF 標籤樹不完整時出現。

### 字大小估算

CJK 字元寬度 ≈ 字級（pt）的 1/72 英寸。例：
- 60pt 中文字 ≈ 0.83 吋寬
- 「450萬」4 字（含半形數字）≈ 0.83 × 3.2 ≈ 2.65 吋
- 如果卡片寬只有 2.7 吋，會剛好邊緣斷——實務上要留 10-15% 安全邊，建議降到 44pt

### 數字與中文混排

stat callout 同時支援「450萬」「合法代噴」「TIA」時，以字元長度自動切換字級：

```python
num_size = 44 if len(num) <= 4 else 28
```

### 渲染 dpi 選擇

| dpi | 檔案大小 | Read 工具顯示 | 用途 |
|-----|---------|---------------|------|
| 96 | 小 | 粗糙 | 快速掃描整體 |
| 110 | 中 | 文字勉強可讀 | **日常 QA 建議值** |
| 150 | 大 | 文字清晰 | 最終精修 |
| 200+ | 很大 | 過剩 | 輸出給使用者 |

### 視覺驗證時要換個視角

產生 PPT 的 agent 會「以為自己照寫的跑」，但真實渲染常有 bug。
Anthropic `pptx` skill 建議用 subagent 做視覺 QA（fresh eyes），避免確認偏誤。
小案子一個人做可以，但要刻意帶著「這裡一定有 bug」的心態看圖。

## 相關工具

- `upstream/anthropic-skills/skills/pptx/SKILL.md`：官方 pptx skill，含更完整的 QA checklist 與 typography 建議
- python-pptx 官方文件：<https://python-pptx.readthedocs.io/>
- PyMuPDF 官方文件：<https://pymupdf.readthedocs.io/>

## 參考實作

本 skill 源自 `D:\35_UAV` 無人機簡報專案的實戰經驗：29 頁 16:9 簡報、4 次迭代修正覆蓋／錯配／頁碼／國家標籤問題，整體流程約 15 分鐘。
