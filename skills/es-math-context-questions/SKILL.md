---
name: es-math-context-questions
description: >
  國小數學生活情境非選擇題命題技能。當使用者需要為國小數學出非選擇題、出生活情境題、
  結合時事新聞出數學題，或想要有解析的非選題時，必須使用此技能。
  觸發情境包含：「幫我出國小非選題」、「出五大題情境非選」、「結合生活情境出題」、
  「出兩小題的非選」、「出有時事的國小數學題」、「出應用題」等。
  本技能會上網搜尋與指定國小數學單元相關的生活情境與時事新聞，轉化為符合 Bloom 認知層次
  應用／分析層次的兩小題式非選題，共五大題，皆含詳細解析，並自動匯出為 Word 文件，
  數學方程式以 OMML 格式正確呈現。
---

# 國小數學生活情境非選擇題命題技能

## 技能概覽

本技能依使用者指定的國小數學單元，上網搜尋相關生活情境與時事新聞，將真實素材轉化為非選擇題，並匯出格式正確的 Word 文件。

- **五大題**，每大題含兩小題
- **第(1)小題**：閱讀素材，理解條件即可作答（檢測 Bloom 第2級：理解）
- **第(2)小題**：承接第(1)題，結合數學單元，達到應用或分析層次（檢測 Bloom 第3-4級）
- **每大題皆含完整解析**
- **幾何圖形支援**：若題目涉及幾何情境，自動渲染 PNG 並插入 Word（內建，無需外部技能）
- **匯出 Word 文件**，數學式以 OMML 正確格式呈現

---

## 數學標記語法（題目撰寫規則）
在題目敘述與解析中，所有分數、未知數方程式等**必須**使用 `{}` 包裹。系統會自動將其編譯成 Word 的原生 **OMML 數學公式**，以呈現最專業的排版：

- **分數**：一律使用 `{frac(a,b)}`（例如 `{frac(3,4)}` ➔ 轉為上下疊加結構分數，禁止直接打斜線 `3/4`）。
- **未知數**：一律使用 `{x}`、`{y}`（例如 `{5*x = 20}` ➔ 轉為 Cambria Math 斜體方程式字元，禁止使用普通英文）。
- **不等式與四則運算**：使用 `{<=}` (≤)、`{>=}` (≥)、`{!=}` (≠)、`{*}` (×)。


---

## 處理流程

### Step 1：確認需求
向使用者確認：數學單元、年級（一年級至六年級）、版本（選填）。

---

### Step 2：網路搜尋生活情境
使用 `web_search` 工具，至少進行 3–5 次搜尋，找 5 種不同主題的國小生活素材：
- 購物消費（打折、找錢）、交通運動（速度、散步）、健康科技、環境氣候（氣溫、降雨量折線圖）等。

---

### Step 3：設計題目
每大題結構：素材段落 + 第(1)小題（理解層次）+ 第(2)小題（應用/分析層次）。
數學式必須使用 `{}` 標記。兩小題需有連貫性。

---

### Step 4：撰寫解析
每大題含：(1)解題關鍵+計算過程、(2)解題過程+答案+Bloom層次標註。

---

### Step 4.5：數學自我驗算（必做，生圖與匯出前）

> ⚠️ **數學題目在產出前必須先跑 Python 實算**。驗算不通過就修正，再繼續。

**常見國小題型驗算速查**：

| 題型 | 驗算方式 |
|------|---------|
| 比與比值（a:b = c:d）| `a*d == b*c` |
| 比例分配（總量按比例分配）| `總量 * 各份數 / 總份數` |
| 速度、時間與距離 | `距離 == 速度 * 時間` |
| 圓周長與圓面積 | `周長 == 2 * 3.14 * r`，`面積 == 3.14 * r * r` |
| 柱體表面積與體積 | `體積 == 底面積 * 高` |
| 規律性（間隔問題）| `距離 / 間隔 == 段數` |

**執行驗算程式碼結構**（同國中版，檢查 `/home/claude/exam_data.json` 中的數學數值）。

---

### Step 5：整理為 JSON 資料，儲存為 `/home/claude/exam_data.json`

JSON 格式與國中版完全一致。

---

### Step 5.5：幾何圖形渲染（若 JSON 中有 geometry 欄位）

**判斷是否需要執行**：掃描 `exam_data.json`，若任何題目或子題的 `geometry` 欄位不為 `null`，則執行本步驟。

```bash
# ── 0. 確認幾何腳本位置 ────────────────────────────────────
GEOM_DIR=""
for d in /mnt/skills/user/es-math-geometry/scripts \
          /tmp/es-math-geometry/scripts; do
  [ -f "$d/geometry_renderer.py" ] && GEOM_DIR="$d" && break
done

pip install cairosvg python-docx --break-system-packages -q

# ── 2. 從 exam_data.json 提取所有 geometry spec ─────────────
python3 - <<'PYEOF'
import json
exam = json.load(open('/home/claude/exam_data.json', encoding='utf-8'))
figures = []

def collect(geo, prefix):
    if geo and geo.get('spec'):
        spec = dict(geo['spec'])
        spec['id'] = prefix
        spec.setdefault('canvas', {'width': 260, 'height': 210})
        figures.append({
            'id': spec['id'],
            'type': spec['type'],
            'config': spec.get('config', {}),
            'canvas': spec['canvas'],
            '_caption': geo.get('caption', ''),
            '_key': prefix
        })

for q in exam.get('questions', []):
    n = q['number']
    collect(q.get('geometry'), f"q{n}_main")
    collect(q.get('sub1', {}).get('geometry'), f"q{n}_sub1")
    collect(q.get('sub2', {}).get('geometry'), f"q{n}_sub2")

if figures:
    spec_data = {
        'figures': [{'id': f['id'], 'type': f['type'], 'config': f['config'], 'canvas': f['canvas']} for f in figures],
        'options': {'format': 'png', 'dpi': 150}
    }
    json.dump(spec_data, open('/home/claude/geometry_spec.json', 'w', encoding='utf-8'), ensure_ascii=False, indent=2)
    mapping = {f['id']: {'caption': f['_caption'], 'key': f['_key']} for f in figures}
    json.dump(mapping, open('/home/claude/geometry_mapping.json', 'w', encoding='utf-8'), ensure_ascii=False, indent=2)
fi
PYEOF

# ── 3. 渲染 ────────────────────────────────────────────────
if [ -f /home/claude/geometry_spec.json ]; then
  mkdir -p /home/claude/geometry_output
  python3 "$GEOM_DIR/geometry_renderer.py" /home/claude/geometry_spec.json /home/claude/geometry_output/
fi
```

---

### Step 6：匯出 Word 文件（必做）

```bash
# Step 6-1：找腳本目錄
for d in /mnt/skills/user/es-math-context-questions/scripts \
          /tmp/es-math-context-questions/scripts; do
  [ -f "$d/generate_exam_docx.py" ] && SKILL_SCRIPTS="$d" && break
done

pip install python-docx lxml --break-system-packages -q

# Step 6-3：產生文件
cd "$SKILL_SCRIPTS"
python3 generate_exam_docx.py /home/claude/exam_data.json /home/claude/exam_output.docx
```

#### Step 6.5：將幾何圖形與情境插圖插入 Word（與國中版邏輯相同，將腳本與圖片路徑指向 es-math 目錄）

#### Step 6.7：複製最終輸出
```bash
UNIT=$(python3 -c "import json; d=json.load(open('/home/claude/exam_data.json')); print(d['unit'])")
cp /home/claude/exam_output.docx "/mnt/user-data/outputs/非選擇題_${UNIT}.docx"
```

最後使用 `present_files` 提供 Word 文件下載。
