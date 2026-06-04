---
name: es-math-exam
description: >
  國小數學段考出題與審題專家技能。當使用者需要為國小數學（一年級至六年級）設計段考試題、審核試題品質、
  建立雙向細目表、分析題目認知層次分佈，或產出完整格式的題目卷與答案卷時，請一定要使用此技能。
  觸發情境包含：「幫我出國小數學考題」、「審一下這份國小考卷」、「做雙向細目表」、「依Bloom分級檢查題目」、
  「產出國小數學試題」、「段考命題」、「國小數學考卷格式」等。
  此技能整合修訂版 Bloom 認知層次四級分類（記憶/理解/應用/分析）、雙向細目表格式，以及國小段考卷的標準版面規格。
---

# 國小數學段考命題審題技能

## 技能概覽

本技能支援兩種模式，協助教師完成國小數學段考的完整命題與審題流程：

**模式 A：出題模式**（全新命題）
1. 需求訪談 — 確認年級、範圍、題型、配分
2. 規劃雙向細目表 — 章節 × 認知層次比例
3. 生成題目 — 按 Bloom 四級分類產出
4. **幾何圖形渲染** — 若有幾何題，自動產生配圖 PNG（內建，無需呼叫外部技能）
5. 格式輸出 — 題目卷、答案卷、雙向細目表（三份 Word），幾何圖自動插入對應位置

**模式 B：審題模式**（審核現有考卷）
1. 接收考卷 — 使用者貼入文字或上傳圖片/Word
2. 逐題審查 — 判定 Bloom 層次、section 歸屬、答案與題目品質
3. 輸出分級報告 — 顯示統計與偏差提示
4. 格式輸出 — 僅產出雙向細目表（一份 Word）

---

## 處理流程

### Step 1：需求確認（必做）

向使用者確認以下資訊（未提供則詢問）：

| 項目 | 說明 |
|------|------|
| 年級 | 一年級 / 二年級 / 三年級 / 四年級 / 五年級 / 六年級 |
| 學期 & 次別 | 第一學期第三次段考 等 |
| 考試範圍 | 章節名稱 + 版本（翰林版 / 康軒版 / 南一版） |
| 題型需求 | 選擇題幾題、填充題幾題、計算/應用題幾題、配分 |
| 難度目標 | 參考 Bloom 各級比例，或教師自訂 |
| 模式 | **A. 出題**（全新產出題目卷+答案卷+細目表） / **B. 審題**（審核現有考卷，只產細目表） |

---

### Step 2：建立雙向細目表

讀取 `references/shuangxiang-table.md`（若有）取得格式規範，依下列結構規劃：

- **縱軸**：教材章節（依考試範圍列出各節名稱與課綱代碼，如「N-5-6 面積」）
- **橫軸**：認知層次（記憶 / 理解 / 應用 / 分析）
- **細格**：分數（題數），例如 `6（2）`

**國小段考建議比例**（來自 Bloom 分級專家設定）：

| 層次 | 定義 | 建議佔比 |
|------|------|---------|
| 第1級：記憶 | 回憶公式、基本定義、長度/重量/時間單位記誦 | 15% |
| ... | 單位換算、辨別幾何特徵、解讀一維/二維圖表 | 25% |
| 第3級：應用 | 多步驟整數/分數/小數運算、兩步驟應用題、求多邊形面積與體積、使用等量公理解未知數 | 40% |
| 第4級：分析 | 複雜情境應用題、規律性推導、可能性與簡單機率分析、非典型問題（如雞兔同籠、間隔問題） | 20% |

> ⚠️ 若偏離此比例超過 ±10%，需提醒調整。

---

### Step 3：題目生成（出題模式 A 專用）

依雙向細目表各格生成題目，每題須標註：
- 題號、題型（選擇 / 填充 / 應用題）
- Bloom 等級與判定關鍵理由
- 正確答案 + 解題過程
- **幾何圖形需求**（若題目需要圖，填寫 `geometry` 欄位，見下方 JSON 規格）

**各層次出題原則**（參考 `references/bloom-taxonomy.md`）：

- **第1級（記憶）**：辨認公式正確性、回憶基本單位換算，例如「1公里等於幾公尺？」
- **第2級（理解）**：解釋步驟、辨別圖形特徵，例如「下列哪一個圖形是線對稱圖形？」
- **第3級（應用）**：給定具體數值/情境，帶公式或兩步驟計算，例如「求底為6公分、高為4公分的三角形面積」
- **第4級（分析）**：多條件整合、推論隱含關係，例如「將一個邊長10公分的正方體，切割成邊長2公分的小正方體，共可切成幾個？」

**選擇題格式規範**：
- 四選一（A/B/C/D），每題必須提供完整四個選項
- 干擾選項需具學習意義（常規計算錯誤或概念迷思）
- 每題明確有唯一正確答案

**答案分佈規則（必須嚴格遵守）**：
- 答案必須平均分佈於 A、B、C、D 四個選項，每個選項出現次數應大致相等（±1題以內）
- **禁止連續兩題答案相同**（例如：第3題答A，第4題不得再答A）
- 命題完成後，**必須自我檢查**答案序列，確認無連續重複，並確認各選項出現次數平衡

---

### Step 4：認知層次審查（審題模式 B 專用）

> **觸發條件**：使用者提供已出好的國小考卷（貼入文字、上傳圖片或 .docx），要求審題或產雙向細目表。

#### 4-1 接收考卷資訊
向使用者確認以下資訊（若考卷內已有則自動提取，不需重複詢問）：年級、學期次別、考試範圍（對照 `references/es-math-curriculum.md` 提取課綱代碼）、配分規則。

#### 4-2 逐題審查
對每道題目判定：
- **section**：歸屬哪個節次與課綱代碼（如「5-2 異分母分數的加減 N-5-2」）
- **bloom_level**：第1~4級，填寫完整格式如「第3級（應用）」
- **品質檢查**：題目敘述是否清晰、非選題是否合理

#### 4-3 整理為審題 JSON，並儲存為 `/home/claude/exam_data.json`

#### 4-4 執行腳本（審題模式，產出雙向細目表 + 審題報告）

```bash
# 找腳本目錄
for d in /mnt/skills/user/es-math-exam/scripts           /tmp/es-math-exam/scripts; do
  [ -f "$d/generate_exam_docx.py" ] && SKILL_SCRIPTS="$d" && break
done

pip install python-docx lxml --break-system-packages -q
mkdir -p /home/claude/exam_output
cd "$SKILL_SCRIPTS"

# ★ 審題模式加 --blueprint-only，產出雙向細目表與審題報告
python3 generate_exam_docx.py /home/claude/exam_data.json /home/claude/exam_output/ --blueprint-only

GRADE=$(python3 -c "import json; d=json.load(open('/home/claude/exam_data.json')); print(d['grade'])")
EN=$(python3 -c "import json; d=json.load(open('/home/claude/exam_data.json')); print(d['exam_number'])")
cp "/home/claude/exam_output/${GRADE}數學第${EN}次段考_雙向細目表.docx" "/mnt/user-data/outputs/"
[ -f "/home/claude/exam_output/${GRADE}數學第${EN}次段考_審題報告.docx" ] && \
  cp "/home/claude/exam_output/${GRADE}數學第${EN}次段考_審題報告.docx" "/mnt/user-data/outputs/"
echo "✅ 完成"
```

最後使用 `present_files` 工具同時提供兩份 Word 檔下載（雙向細目表、審題報告）。

---

### Step 5：整理為 JSON 資料

將所有題目整理為以下格式，儲存為 `/home/claude/exam_data.json`：

#### ▶ geometry 欄位說明（出題模式專用）
若題目需要幾何圖形配圖，在該題加入 `geometry` 欄位；不需要圖形則設為 `null`。

**幾何圖形 spec 對照（常用類型快速複製）：**

| 題目類型 | geometry.spec.type | 常用 subtype / config |
|---------|-------------------|----------------------|
| 三角形面積 | `triangle` | `right` / `general`（標底與高） |
| 平行四邊形 | `quadrilateral` | `parallelogram` |
| 梯形 | `quadrilateral` | `trapezoid` |
| 圓與扇形 | `circle` | `central_angle`（扇形填色） |
| 立體圖形 | `solid_3d` | `rectangular_prism` / `cylinder` / `cone` |

**JSON 格式（含 geometry 欄位）：**

```json
{
  "school_year": "114",
  "semester": "1",
  "exam_number": "3",
  "grade": "五年級",
  "scope": "南一版第五冊第三章～第五章",
  "total_pages": 2,
  "mc_scoring": "1~10題每題4分，共40分",
  "mc_questions": [
    {
      "number": 1,
      "section": "3-2 三角形的面積 N-5-6",
      "bloom_level": "第3級（應用）",
      "question": "如圖，直角三角形 ABC 中，∠C = 90°，兩股長分別為 AC = 3 公分，BC = 4 公分，求此三角形的面積為多少平方公分？",
      "options": { "A": "5", "B": "6", "C": "12", "D": "24" },
      "answer": "B",
      "solution": "直角三角形面積 = 底 × 高 ÷ 2 = 3 × 4 ÷ 2 = 6",
      "geometry": {
        "caption": "圖一",
        "spec": {
          "type": "triangle",
          "config": {
            "subtype": "right",
            "vertex_labels": ["A", "B", "C"],
            "right_angle_at": "C",
            "side_labels": {"AC": "3", "BC": "4"}
          },
          "canvas": {"width": 250, "height": 200}
        }
      }
    },
    {
      "number": 2,
      "section": "4-1 因數與倍數 N-5-1",
      "bloom_level": "第2級（理解）",
      "question": "下列哪一個數是 18 的因數？",
      "options": { "A": "4", "B": "5", "C": "6", "D": "8" },
      "answer": "C",
      "solution": "18 ÷ 6 = 3，整除，故 6 是 18 的因數",
      "geometry": null
    }
  ],
  "open_questions": [
    {
      "number": 1,
      "section": "5-1 梯形面積 N-5-6",
      "total_points": 10,
      "context": "如圖，有一個梯形 ABCD 的花圃，上底 AD = 6 公尺，下底 BC = 10 公尺，高為 4 公尺。",
      "geometry": {
        "caption": "圖二",
        "spec": {
          "type": "quadrilateral",
          "config": {
            "subtype": "trapezoid",
            "vertex_labels": ["A", "B", "C", "D"],
            "side_labels": {"AD": "6", "BC": "10"},
            "show_height": true,
            "height_label": "4"
          },
          "canvas": {"width": 280, "height": 220}
        }
      },
      "sub_questions": [
        {
          "label": "(1)",
          "bloom_level": "第3級（應用）",
          "points": 5,
          "question": "求這個梯形花圃的面積是多少平方公尺？",
          "answer": "32",
          "solution": "梯形面積 = (上底 + 下底) × 高 ÷ 2 = (6 + 10) × 4 ÷ 2 = 32"
        }
      ]
    }
  ]
}
```

---

### Step 5.2：多代理人自動化審查與品質校驗 (Multi-Agent Quality Check) (推薦 🌟)
為了 100% 消除「範圍幻覺」與「幾何畫圖格式錯誤」，在完成 `exam_data.json` 後、正式執行 Word 生成前，**必須**啟動一個獨立的子代理（Subagent）扮演 `es-math-reviewer` 進行第三方審查：

1. **啟動審查 Agent**：
   - 任務：讀取 `exam_data.json` 並獨立執行 Python 代碼驗算所有答案，同時核對幾何配圖的實線與虛線是否符合出題情境。
2. **審查重點項目**：
   - **數學正確度**：所有應用題和填充題的數值，必須用 Python 實算驗證，不得出現 AI 計算偏差。
   - **幾何配圖完整性**：若題目有「如圖」等字眼，檢查對應的幾何 config 是否 100% 存在，且實線與虛線（如對稱軸虛線、底邊實線）配置是否正確。
   - **對稱軸標記**：檢查對稱軸是否使用 `"altitude_from"` 繪製並正確標上 `"altitude_label": "L"`。
3. **回饋與修正**：
   - 若審查 Agent 發現任何錯誤，出題 Agent 必須自動修正 `exam_data.json` 中的數據與圖形規格。
   - 重複此流程，直到審查 Agent 給出 **「✅ 審查通過」** 的綠燈，才能繼續進行後續的幾何渲染與 Word 生成。

---


### Step 5.5：幾何圖形渲染（若 JSON 中有 geometry 欄位）

**判斷是否需要執行**：掃描 `exam_data.json`，若任何題目的 `geometry` 欄位不為 `null`，則執行本步驟。

```bash
# ── 0. 確認幾何腳本位置 ──────────────────────────────────────
GEOM_DIR=""
for d in /mnt/skills/user/es-math-geometry/scripts \
          /tmp/es-math-geometry/scripts; do
  [ -f "$d/geometry_renderer.py" ] && GEOM_DIR="$d" && break
done
echo "幾何腳本目錄：$GEOM_DIR"

# ── 1. 安裝依賴 ────────────────────────────────────────────
pip install cairosvg python-docx --break-system-packages -q

# ── 2. 從 exam_data.json 建立 geometry_spec.json ──
python3 - <<'PYEOF'
import json, pathlib
exam = json.load(open('/home/claude/exam_data.json', encoding='utf-8'))
figures = []

def collect(q, prefix):
    geo = q.get('geometry')
    if geo and geo.get('spec'):
        spec = dict(geo['spec'])
        spec['id'] = f"{prefix}_fig"
        spec.setdefault('canvas', {'width': 260, 'height': 210})
        figures.append({
            'id': spec['id'],
            'type': spec['type'],
            'config': spec.get('config', {}),
            'canvas': spec['canvas'],
            '_caption': geo.get('caption', ''),
            '_question_key': prefix
        })

for q in exam.get('mc_questions', []):
    collect(q, f"mc_{q['number']}")
for q in exam.get('open_questions', []):
    collect(q, f"open_{q['number']}")
    for sq in q.get('sub_questions', []):
        label = sq['label'].strip('()')
        collect(sq, f"open_{q['number']}_{label}")

if figures:
    spec_data = {
        'figures': [{'id': f['id'], 'type': f['type'], 'config': f['config'], 'canvas': f['canvas']} for f in figures],
        'options': {'format': 'png', 'dpi': 150}
    }
    json.dump(spec_data, open('/home/claude/geometry_spec.json', 'w', encoding='utf-8'), ensure_ascii=False, indent=2)
    mapping = {f['id']: {'caption': f['_caption'], 'question_key': f['_question_key']} for f in figures}
    json.dump(mapping, open('/home/claude/geometry_mapping.json', 'w', encoding='utf-8'), ensure_ascii=False, indent=2)
    print(f"✅ 需要渲染 {len(figures)} 張幾何圖形")
else:
    print("ℹ&nbsp;無幾何圖形")
PYEOF

# ── 3. 渲染 ────────────────────────────────────
if [ -f /home/claude/geometry_spec.json ]; then
  mkdir -p /home/claude/geometry_output
  python3 "$GEOM_DIR/geometry_renderer.py" /home/claude/geometry_spec.json /home/claude/geometry_output/
  echo "✅ 幾何圖形渲染完成"
fi
```

---

### Step 6：匯出 Word 文件（必做）

```bash
# Step 6-1：找腳本目錄
for d in /mnt/skills/user/es-math-exam/scripts \
          /tmp/es-math-exam/scripts; do
  [ -f "$d/generate_exam_docx.py" ] && SKILL_SCRIPTS="$d" && break
done

pip install python-docx lxml --break-system-packages -q
mkdir -p /home/claude/exam_output
cd "$SKILL_SCRIPTS"
python3 generate_exam_docx.py /home/claude/exam_data.json /home/claude/exam_output/
```

#### Step 6.5：將幾何圖形插入題目卷（與國中版邏輯相同，將 $GEOM_DIR 指向 es-math-geometry）
```bash
# 後處理插入，此處會將幾何圖形 PNG 插入 Word 中對應題號...
```

#### Step 6.6：複製最終輸出到 outputs
```bash
GRADE=$(python3 -c "import json; d=json.load(open('/home/claude/exam_data.json')); print(d['grade'])")
EN=$(python3 -c "import json; d=json.load(open('/home/claude/exam_data.json')); print(d['exam_number'])")
cp "/home/claude/exam_output/${GRADE}數學第${EN}次段考_題目卷.docx" "/mnt/user-data/outputs/"
cp "/home/claude/exam_output/${GRADE}數學第${EN}次段考_答案卷（教師版）.docx" "/mnt/user-data/outputs/"
cp "/home/claude/exam_output/${GRADE}數學第${EN}次段考_雙向細目表.docx" "/mnt/user-data/outputs/"
```

---

## 數學標記語法
在題目文字、選項與解析中，所有數學方程式、分數、未知數等均**必須**使用大括號 `{}` 包裹。系統在生成 docx 考卷時，將會自動將這些語法轉換為 Word 原生 **OMML 數學公式節點**，而非普通文字，以呈現最專業的排版格式：
- **分數**：一律使用 `{frac(a,b)}`（系統會轉為 Word 原生之上下疊加結構分數，禁止直接打 `a/b` 或使用普通文字）。
- **未知數**：一律使用 `{x}` 或 `{y}`（系統會轉為 Word 方程式專用的 Cambria Math 斜體字元，禁止使用普通半形英文字）。
- **四則運算與不等式**：
  - 乘號：使用 `{a*b}` ➔ 轉為 `a×b`
  - 關係符號：使用 `{<=}` (≤)、`{>=}` (≥)、`{!=}` (≠)


---

## 快速參考：各技能詳細說明位置

| 需要什麼 | 讀取哪個檔案 |
|----------|-------------|
| 108課綱國小數學完整章節與學習內容代碼 | `references/es-math-curriculum.md` |
| 幾何圖形完整參數 + 快速複製範例 | `skills/es-math-geometry/references/figure-catalog.md` |
| 雙向細目表格式規範 | `references/shuangxiang-table.md` |
