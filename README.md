# 教學出題技能集（Teaching & Exam Skills）

一組可重用的 **Agent Skills**，專注於**國小數學（一至六年級）命題、審題、幾何配圖與形成性評量小遊戲**。
每個技能皆為獨立資料夾，內含一份 `SKILL.md`（含 YAML frontmatter 描述觸發情境），可供 Claude Code 或任何支援 Agent Skills 規格的 AI agent 直接讀取使用。

> 所有技能皆以**繁體中文**設計，題目對齊修訂版 Bloom 認知層次。

> 💡 **專案源起**：本專案修改自「三師爸」所開發的 [國中數學出題技能集](https://github.com/mathruffian-dot/teaching-exam-skills)，特此致謝。本版本已將其全面調整為適用於**國小數學（一至六年級）**的教學出題與幾何繪圖規格。

---


## 📦 包含的技能

| 技能 | 用途 | 額外檔案 |
|------|------|----------|
| [`es-math-exam`](skills/es-math-exam/SKILL.md) | 國小數學段考出題與審題專家。設計一至六年級段考試題、審題、建立雙向細目表、Bloom 認知層次分析，產出標準版面的題目卷與答案卷。 | `references/` |
| [`es-math-context-questions`](skills/es-math-context-questions/SKILL.md) | 國小數學「生活情境非選擇題」命題。結合時事／生活情境，產出 Bloom 應用／分析層次的兩小題式非選題（共五大題，含詳解），匯出 Word（數學式用 OMML 呈現）。 | — |
| [`es-math-geometry`](skills/es-math-geometry/SKILL.md) | 國小數學幾何圖形 SVG 產生器。三角形、四邊形、圓與扇形、坐標平面、簡單立體圖（長方體、正方體、圓柱、圓錐、角柱、角錐）等，可匯出 Word／PPT。可被出題技能呼叫配圖。 | `scripts/`、`references/` |
| [`teaching-minigames`](skills/teaching-minigames/SKILL.md) | 把教材重點轉成形成性評量小遊戲，發佈為可分享 HTML + QR Code。 | `references/` |

---

## 🚀 給其他 Agent 使用

這是一個**純技能資料夾**結構。最簡單的用法：

```bash
git clone https://github.com/mathruffian-dot/teaching-exam-skills.git
```

然後讓你的 agent 讀取對應的 `skills/<技能名>/SKILL.md`。每份 `SKILL.md` 的 frontmatter 都描述了觸發情境與操作步驟。

### 安裝到 Claude Code（個人技能目錄）

```bash
# macOS / Linux
cp -r teaching-exam-skills/skills/* ~/.claude/skills/

# Windows (PowerShell)
Copy-Item teaching-exam-skills/skills/* $HOME/.claude/skills/ -Recurse
```

---

## ⚠️ 使用前注意（外部依賴）

| 技能 | 依賴 | 說明 |
|------|------|------|
| `es-math-context-questions` | `draw` 技能 | SKILL.md 內有寫死路徑 `C:/Users/mathr/.claude/skills/draw/draw.py`（gpt-image-2 生圖）。**本 repo 未含此腳本**；若不需配圖可忽略相關步驟，或自行替換為你的生圖工具。 |
| `es-math-context-questions` / `es-math-exam` | Word 匯出 | 需要可輸出 `.docx`（OMML 數學式）的環境。 |
| `es-math-geometry` | Python | `scripts/` 內為 SVG 轉檔與插入 Word/PPT 的腳本，需 Python 環境。 |
| `teaching-minigames` | GitHub Token | 發佈到 GitHub Pages 時需使用者自備 Personal Access Token（`repo` 權限）。Token 由使用者即時提供，不會被儲存。 |

---

## 💡 AI 段考命題最佳實踐與提示詞 (Best Practices & Prompts)

為了讓 AI Agent 發揮 100% 的精確出題效果，建議使用者採用以下避坑工作流與指令。

### 1. 避坑的「黃金出題三步驟」
各教科書版本（康軒、翰林、南一）每年、各校的段考範圍常有微調。**請不要直接讓 AI 憑空估算或上網搜尋**，這很容易產生單元範圍錯亂的「幻覺」。
* **第一步：提供課本目錄** ➔ 出題前，直接拍照上傳該學期實體課本的「目錄頁」圖片給 AI，或手動貼上目錄文字。這能確保出題單元 100% 正確。
* **第二步：提供難度參考範本** ➔ 將「學校平時練習卷或習作的 2~3 題」拍照或打字提供給 AI（Few-Shot 學習），AI 出出來的題目難易度就會與學校教學完全契合。
* **第三步：一鍵生成** ➔ 交由 AI 進行雙向細目表規劃並一鍵產生 Word 試卷。

### 2. 實用提示詞範本

#### 提示詞 A：在線讀取（免下載直接出題）
> 「請讀取這份國小出題技能說明：`https://github.com/你的帳號/teaching-exam-skills/blob/master/skills/es-math-exam/SKILL.md`。讀完後請扮演裡面的國小數學段考出題專家，幫我出一份【康軒版五年級下學期第二次段考】的題目。」

#### 提示詞 B：本地端執行（自動排版一鍵產出 Word 文件）
> 「請讀取本地的 `skills\es-math-exam\SKILL.md` 技能。我想出一份【翰林版五下期末考】的試卷，請幫我設計題目，並自動執行 `skills\es-math-exam\scripts\generate_exam_docx.py` 腳本，將排版好的題目卷、答案卷和雙向細目表產出到本地的 `output` 資料夾。」

---

## 📄 授權

MIT License，詳見 [LICENSE](LICENSE)。歡迎自由使用與修改。
