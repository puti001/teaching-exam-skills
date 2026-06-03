# 幾何圖形參數目錄 (figure-catalog.md)

本文件列出國小數學支援的圖形類型及其完整參數說明，供命題或需要精確控制幾何圖形配圖時查閱。

---

## 通用結構

每個 figure spec 的頂層結構：

```json
{
  "id": "fig1",
  "type": "<圖形類型>",
  "config": { ... },
  "canvas": { "width": 280, "height": 220, "bg": "white" }
}
```

---

## 1. triangle（三角形）

```json
{
  "type": "triangle",
  "config": {
    "subtype": "general",
    "vertex_labels": ["A", "B", "C"],
    "right_angle_at": "C",
    "side_labels": {"AC": "3", "BC": "4"},
    "equal_marks": {"AB": 1, "AC": 1},
    "altitude_from": "A",
    "show_dots": true
  }
}
```

| 參數 | 說明 | 可選值 |
|------|------|--------|
| subtype | 三角形種類 | `general`（預設）、`right`（直角）、`isosceles`（等腰）、`equilateral`（正三角形） |
| vertex_labels | 頂點標籤 | 字串陣列，預設 `["A","B","C"]` |
| right_angle_at | 畫直角符號的頂點 | 頂點標籤字串 |
| side_labels | 各邊的文字標籤 | `{"AB":"5"}` 鍵為兩端頂點標籤 |
| equal_marks | 等邊刻度數 | `{"AB":1, "AC":1}` |
| altitude_from | 畫高的起始頂點 | 頂點標籤字串（會自動在對邊標示直角） |
| show_dots | 是否畫頂點黑點 | `true`（預設）|

**常用範例**：

直角三角形（標示兩股長）：
```json
{"type":"triangle","config":{"subtype":"right","vertex_labels":["A","B","C"],"right_angle_at":"C","side_labels":{"AC":"3","BC":"4"}}}
```

等腰三角形（標示等邊與底邊）：
```json
{"type":"triangle","config":{"subtype":"isosceles","equal_marks":{"AB":1,"AC":1},"side_labels":{"BC":"6"}}}
```

---

## 2. quadrilateral（四邊形）

```json
{
  "type": "quadrilateral",
  "config": {
    "subtype": "parallelogram",
    "vertex_labels": ["A", "B", "C", "D"],
    "side_labels": {"AB": "8", "BC": "5"},
    "equal_marks": {"AB": 1, "CD": 1},
    "right_angles": ["A"],
    "show_dots": true
  }
}
```

| subtype 值 | 說明 |
|-----------|------|
| `rectangle` | 長方形（自動標四個直角）|
| `square` | 正方形（自動標四個直角與四邊等長刻度）|
| `parallelogram` | 平行四邊形 |
| `trapezoid` | 梯形（等腰梯形）|
| `right_trapezoid` | 直角梯形（自動在垂直邊標直角）|
| `general` | 任意四邊形 |

**常用範例**：

長方形（標示長與寬）：
```json
{"type":"quadrilateral","config":{"subtype":"rectangle","vertex_labels":["A","B","C","D"],"side_labels":{"AB":"8","BC":"5"}}}
```

---

## 3. circle（圓與扇形）

```json
{
  "type": "circle",
  "config": {
    "center_label": "O",
    "show_center": true,
    "points": {
      "A": 30,
      "B": 150
    },
    "radius_lines": ["A", "B"],
    "radius_label": "5",
    "radius_label_at": "A",
    "central_angle": ["A", "B"]
  }
}
```

| 參數 | 說明 |
|------|------|
| points | 圓周上各點標籤 ➔ 角度（0=右，90=上，逆時針）|
| radius_lines | 畫哪些半徑線（如 `["A", "B"]`）|
| radius_label | 半徑數值或字母（如 `"5"` 或 `"r"`）|
| radius_label_at | 半徑標籤標註在靠近哪一個點 |
| central_angle | 填色扇形（圓心角）的兩端點，如 `["A", "B"]` |
| diameter | 直徑端點，如 `["A", "C"]` |

**常用範例**：

扇形面積（圓心角 120 度，標示半徑）：
```json
{"type":"circle","config":{"points":{"A":30,"B":150},"radius_lines":["A","B"],"radius_label":"6","radius_label_at":"A","central_angle":["A","B"]}}
```

---

## 4. solid_3d（立體圖形）

```json
{
  "type": "solid_3d",
  "config": {
    "subtype": "rectangular_prism",
    "show_hidden": true,
    "vertex_labels": ["A","B","C","D","E","F","G","H"],
    "labels": {
      "radius": "r",
      "height": "h"
    }
  }
}
```

| subtype 值 | 說明 | 頂點順序或標籤說明 |
|-----------|------|-----------------|
| `rectangular_prism` | 四角柱（長方體/正方體）| 頂面 ABCD，底面 EFGH |
| `cylinder` | 圓柱 | 可標示底圓半徑 radius、高 height |
| `cone` | 圓錐 | 可標示底圓半徑 radius、高 height |
| `triangular_prism` | 三角柱 | 前面 D-E-F，後面 A-B-C |
| `square_pyramid` | 四角錐 | 底面 ABCD，頂點 P |

**常用範例**：

長方體（透視虛線）：
```json
{"type":"solid_3d","config":{"subtype":"rectangular_prism","vertex_labels":["A","B","C","D","E","F","G","H"],"show_hidden":true}}
```

圓柱體（標示半徑與高）：
```json
{"type":"solid_3d","config":{"subtype":"cylinder","labels":{"radius":"5","height":"12"}}}
```

---

## 5. 坐標平面與其他進階圖形

> 💡 坐標平面 (`coordinate_plane`) 主要用於國小直角坐標的簡單點、線段繪製（不常用拋物線與複雜函數）。
> 💡 平行線 (`parallel_lines`)、三角形的心 (`triangle_center`)、相似三角形 (`similar_triangles`) 等為國中進階內容，國小命題通常不需使用。
