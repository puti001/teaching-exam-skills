---
name: es-math-geometry
description: >
  國小數學幾何圖形 SVG 產生器。當任何情境需要生成或繪製國小數學幾何圖形時，請一定要使用此技能。

  【獨立使用觸發情境】「幫我畫直角三角形」、「畫一個標出長寬的長方形」、「畫圓與扇形」、
  「畫平行四邊形」、「畫等腰梯形」、「畫長方體/正方體」、「畫圓柱與圓錐」、
  「幫我畫幾何圖、產生幾何圖、繪製幾何圖形」等。

  【被其他技能呼叫觸發情境】出題技能（es-math-exam）需要幾何題配圖時；
  教學簡報技能需要幾何插圖時；任何需要圖形素材的技能皆可呼叫此技能。

  支援圖形類型：
  三角形（一般/直角/等腰/等邊）、四邊形（平行四邊形/矩形/菱形/正方形/梯形）、
  圓與扇形（圓心角、弧長、半徑、直徑）、坐標平面（點、線段）、
  立體圖形（長方體、圓柱、圓錐、三角柱、四角錐、三角錐）。
  圖形可匯出至 Word（.docx）或 PowerPoint（.pptx）。
---

# 國小數學幾何圖形技能（es-math-geometry）

## 技能概覽

本技能生成適合國小試卷、簡報、教材的幾何 SVG 圖形，並輸出為 PNG 圖片檔，可直接插入 Word 文件或 PowerPoint 投影片。

**核心腳本位置**：
```bash
GEOM_DIR=""
for d in /mnt/skills/user/es-math-geometry/scripts \
          /tmp/es-math-geometry/scripts; do
  [ -f "$d/geometry_renderer.py" ] && GEOM_DIR="$d" && break
done
echo "腳本目錄：$GEOM_DIR"
```

---

## 處理流程

### Step 1：理解需求
根據使用者的描述，判斷需要哪些圖形。確認長度、角度、等邊刻度、直角符號、頂點標籤等需求。

### Step 2：建立圖形規格 JSON，儲存至 `/home/claude/geometry_spec.json`
```json
{
  "figures": [
    {
      "id": "fig1",
      "type": "triangle",
      "config": {
        "subtype": "right",
        "vertex_labels": ["A", "B", "C"],
        "right_angle_at": "C",
        "side_labels": {"AC": "3", "BC": "4"}
      },
      "canvas": {"width": 280, "height": 220}
    }
  ],
  "options": {"format": "png", "dpi": 150}
}
```

### Step 3：安裝依賴並執行渲染
```bash
pip install cairosvg python-docx python-pptx --break-system-packages -q
mkdir -p /home/claude/geometry_output
python3 "$GEOM_DIR/geometry_renderer.py" /home/claude/geometry_spec.json /home/claude/geometry_output/
```

### Step 4：視覺確認並匯出

---

## 圖形類型速查表

| type 值 | 說明 | 常用 subtype / config 參數 |
|---------|------|------------------------|
| `triangle` | 三角形 | `general`（一般）、`right`（直角）、`isosceles`（等腰）、`equilateral`（正三角形） |
| `quadrilateral` | 四邊形 | `rectangle`（長方形）、`square`（正方形）、`parallelogram`（平行四邊形）、`trapezoid`（梯形）、`right_trapezoid`（直角梯形） |
| `circle` | 圓與扇形 | `radius_lines`（畫半徑）、`central_angle`（圓心角與扇形填色） |
| `solid_3d` | 立體圖形 | `rectangular_prism`（長方體）、`cylinder`（圓柱）、`cone`（圓錐）、`triangular_prism`（三角柱）、`square_pyramid`（四角錐） |
| `coordinate_plane` | 坐標平面 | 國小簡易直角坐標（點、線段繪製） |

> 註：原本支援的「相似三角形」、「三角形三心」、「平行線截角」屬於國中進階幾何，國小出題可忽略。

---

## 參考資料位置

- 所有圖形類型的完整參數與快速複製範例：`skills/es-math-geometry/references/figure-catalog.md`
- 繪圖引擎與轉檔、Word/PPT 插入腳本位於 `skills/es-math-geometry/scripts/`
